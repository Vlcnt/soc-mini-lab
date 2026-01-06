# 01 — Identity & Privileges (Windows)

## Baseline: am I receiving Security events?
```powershell
Get-WinEvent -LogName Security -MaxEvents 5
```

## Local user creation (Event ID 4720)
```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4720} -MaxEvents 20
```

## User added to Administrators (Event ID 4732)
```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4732} -MaxEvents 20
```

## Live state validation
```cmd
net user
```

```cmd
net localgroup Administrators
```

## Quick remediation (lab)
```cmd
net localgroup Administrators <USERNAME> /delete
```

```cmd
net user <USERNAME> /active:no
```

```cmd
net user <USERNAME> /delete
```
