---
layout: default
title: powershell
parent: KB
---
## Список маршрутов
```powershell
netsh interface ip show route

Commands in this context:
show address   - Displays IP address configuration.
show config    - Displays IP address and additional information.
show dns       - Displays the DNS server addresses.
show icmp      - Displays ICMP statistics.
show interface - Displays IP interface statistics.
show ipaddress - Displays current IP addresses.
show ipnet     - Displays IP net-to-media mappings.
show ipstats   - Displays IP statistics.
show joins     - Displays multicast groups joined.
show offload   - Displays the offload information.
show tcpconn   - Displays TCP connections.
show tcpstats  - Displays TCP statistics.
show udpconn   - Displays UDP connections.
show udpstats  - Displays UDP statistics.
show wins      - Displays the WINS server addresses.
```
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
