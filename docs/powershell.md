---
layout: default
title: powershell
parent: kb
has_toc: true
---
<details close markdown="block">
  <summary>
    Содержание
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

# Количество объектов в фильтре
```powershell
(Get-ADObject -Filter * -SearchBase "OU=Users,DC=local").Count
```
# Поиск строки в файлах без учета регистра
```powershell
Select-String -Path "C:\tmp\dir\*" -Pattern "строка_для_поиска" -Recurse -CaseSensitive
```
# ldap filters
Excludes all users who are disabled.
```powershell
&(objectCategory=person)(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=2)
```

# WinRM (Windows Remote Management)
```powershell
# Open PowerShell with administrative privileges

# Check the current TrustedHosts list
Get-Item WSMan:\localhost\Client\TrustedHosts

# Add the remote PC to the TrustedHosts list
Set-Item WSMan:\localhost\Client\TrustedHosts -Value "RemotePCName" -Force

# Verify the updated TrustedHosts list
Get-Item WSMan:\localhost\Client\TrustedHosts

# Clearing the TrustedHosts List: If you want to clear the TrustedHosts list, you can use:
Clear-Item WSMan:\localhost\Client\TrustedHosts

# Enable PowerShell Remoting on the Remote PC
Enable-PSRemoting -Force

# Set Up Credential Security
$cred = Get-Credential

# Connect to the Remote PC
Enter-PSSession -ComputerName "RemotePCName" -Credential $cred

# Disconnect from the Remote Session
Exit-PSSession


```
