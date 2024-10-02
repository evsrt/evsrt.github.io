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
