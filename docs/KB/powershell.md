---
layout: default
title: powershell
parent: KB
---
## Создание директории
```powershell
mkdir -p \\infosec.local\SYSVOL\infosec.local\PolicyDefinitions\ru-RU
```
## Получение информации о политиках
Список всех политик и сортировка по дате изменения
```powershell
Get-GPO -All | Select-Object DisplayName, ModificationTime | Sort-Object -Property modificationtime -descending
```
## Информация об установленных обновлениях
```powershell
get-hotfix | Sort-Object -Property InstalledOn -descending
```
## CTRL + ALT + END на удаленном рабочем столе
```powershell
(New-Object -COM Shell.Application).WindowsSecurity()
```
## Тест TCP порта
```powershell
Test-NetConnection -ComputerName 10.10.10.10 -Port 443
```
## Процессы с открытым UDP
```
Get-NetUDPEndpoint | Select-Object LocalAddress,LocalPort,OwningProcess,@{Name="Process";Expression={(Get-Process -Id $_.OwningProcess).ProcessName}} | Sort-Object -Property LocalPort | Format-Table
```
