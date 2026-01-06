# 02 — Persistence & Scheduled Tasks (Windows)

## Task enumeration (quick)
```cmd
schtasks /query
```

## Task enumeration (deep: triggers, run-as, actions)
```cmd
schtasks /query /fo LIST /v
```

## Task Scheduler Operational log (timeline)
Enable (lab):
```cmd
wevtutil sl Microsoft-Windows-TaskScheduler/Operational /e:true
```

Latest events:
```powershell
Get-WinEvent -LogName "Microsoft-Windows-TaskScheduler/Operational" -MaxEvents 50
```

Filter common suspicious executions:
```powershell
Get-WinEvent -LogName "Microsoft-Windows-TaskScheduler/Operational" |
Where-Object { $_.Message -match "cmd.exe|powershell.exe" } |
Select-Object -First 50
```

## Task containment / cleanup
Disable:
```cmd
schtasks /change /tn "TaskName" /disable
```

Delete:
```cmd
schtasks /delete /tn "TaskName" /f
```

Verify:
```cmd
schtasks /query /tn "TaskName"
```

---

## Ghost / Insider-like: when the effect persists after task removal
Possible causes “legitimate but sneaky”:
- Startup folder with `.lnk` (target/args)
- Startup folder with `.txt/.log/.ini` documents (opened by Notepad via association)
- Policy `Explorer\Run`
- Local logon scripts (GroupPolicy scripts)
- Task that launches a wrapper (cmd/powershell) instead of the direct payload

### Defensive sweep (lab) to find and disable Notepad-related autostarts
> Note: “reversible” script (renames to `.disabled` where possible). Use in a lab or with caution.

```powershell
# ============================
# NOTEPAD AUTOSTART FIND & FIX
# ============================

$pattern = '(?i)\bnotepad(\.exe)?\b'

Write-Host "`n=== 0) WHO LAUNCHED NOTEPAD (PARENT PROCESS) ==="
$np = Get-CimInstance Win32_Process -Filter "Name='notepad.exe'" -ErrorAction SilentlyContinue |
      Sort-Object CreationDate | Select-Object -Last 1

if ($np) {
    $pp  = Get-CimInstance Win32_Process -Filter "ProcessId=$($np.ParentProcessId)" -ErrorAction SilentlyContinue
    $gpp = if ($pp) { Get-CimInstance Win32_Process -Filter "ProcessId=$($pp.ParentProcessId)" -ErrorAction SilentlyContinue }

    [pscustomobject]@{
        NotepadPID      = $np.ProcessId
        NotepadCmd      = $np.CommandLine
        ParentPID       = $np.ParentProcessId
        ParentName      = $pp.Name
        ParentCmd       = $pp.CommandLine
        GrandParentName = $gpp.Name
        GrandParentCmd  = $gpp.CommandLine
    } | Format-List
}

Write-Host "`n=== 1) SCHEDULED TASKS (Execute/Arguments) ==="
$taskHits = foreach ($t in (Get-ScheduledTask -ErrorAction SilentlyContinue)) {
    foreach ($a in $t.Actions) {
        $exec = $null; $args = $null
        if ($a.PSObject.Properties.Name -contains 'Execute')   { $exec = $a.Execute }
        if ($a.PSObject.Properties.Name -contains 'Arguments') { $args = $a.Arguments }

        if (($exec -match $pattern) -or ($args -match $pattern)) {
            [pscustomobject]@{
                TaskPath = $t.TaskPath
                TaskName = $t.TaskName
                Execute  = $exec
                Args     = $args
            }
        }
    }
}

if ($taskHits) {
    $taskHits | Format-Table -AutoSize
    foreach ($h in $taskHits) {
        Disable-ScheduledTask -TaskName $h.TaskName -TaskPath $h.TaskPath -ErrorAction SilentlyContinue | Out-Null
    }
}

Write-Host "`n=== 2) STARTUP FOLDERS (.lnk / scripts / docs) ==="
$startupPaths = @(
    "$env:APPDATA\Microsoft\Windows\Start Menu\Programs\Startup",
    "$env:ProgramData\Microsoft\Windows\Start Menu\Programs\Startup"
)

$w = New-Object -ComObject WScript.Shell
$startupHits = @()

foreach ($p in $startupPaths) {
    if (Test-Path $p) {
        # .lnk target/args
        Get-ChildItem $p -Force -Filter *.lnk -ErrorAction SilentlyContinue | ForEach-Object {
            $s = $w.CreateShortcut($_.FullName)
            if (($s.TargetPath -match $pattern) -or ($s.Arguments -match $pattern)) {
                $startupHits += $_.FullName
            }
        }

        # scripts that contain "notepad"
        Get-ChildItem $p -Force -Include *.bat,*.cmd,*.ps1 -ErrorAction SilentlyContinue | ForEach-Object {
            if (Select-String -Path $_.FullName -Pattern $pattern -Quiet -ErrorAction SilentlyContinue) {
                $startupHits += $_.FullName
            }
        }

        # documents that may open via association (Notepad)
        Get-ChildItem $p -Force -Include *.txt,*.log,*.ini -ErrorAction SilentlyContinue | ForEach-Object {
            $startupHits += $_.FullName
        }
    }
}

$startupHits = $startupHits | Select-Object -Unique
if ($startupHits) {
    $startupHits | ForEach-Object {
        Rename-Item -Path $_ -NewName ((Split-Path $_ -Leaf) + ".disabled") -Force -ErrorAction SilentlyContinue
    }
}

Write-Host "`n=== 3) POLICY Explorer\Run ==="
$policyKeys = @(
 "HKCU:\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer\Run",
 "HKLM:\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer\Run"
)

foreach($k in $policyKeys){
  if(Test-Path $k){
    $props = Get-ItemProperty $k -ErrorAction SilentlyContinue
    foreach ($pname in ($props.PSObject.Properties | Where-Object {$_.MemberType -eq 'NoteProperty'} | Select-Object -ExpandProperty Name)) {
      $val = $props.$pname
      if (($val -is [string]) -and ($val -match $pattern)) {
        Remove-ItemProperty -Path $k -Name $pname -Force -ErrorAction SilentlyContinue
      }
    }
  }
}

Write-Host "`n=== 4) GROUP POLICY SCRIPTS (content) ==="
$gpDirs = @(
    "C:\Windows\System32\GroupPolicy\User\Scripts\Logon",
    "C:\Windows\System32\GroupPolicy\Machine\Scripts\Startup"
)

foreach ($d in $gpDirs) {
    if (Test-Path $d) {
        Get-ChildItem $d -Recurse -Force -ErrorAction SilentlyContinue |
        Where-Object { -not $_.PSIsContainer } | ForEach-Object {
            if (Select-String -Path $_.FullName -Pattern $pattern -Quiet -ErrorAction SilentlyContinue) {
                Rename-Item -Path $_.FullName -NewName ($_.Name + ".disabled") -Force -ErrorAction SilentlyContinue
            }
        }
    }
}

Stop-Process -Name notepad -Force -ErrorAction SilentlyContinue
Write-Host "`n✔ Sweep complete. Log out/log in or reboot and verify." -ForegroundColor Green
```
