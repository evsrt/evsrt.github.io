---
title: Disable ActiveSync for mailbox
date: 2023-02-20 12:12:00 +0300
categories: [microsoft, msexchange, script]
tags: [msexchange, activesync, ps1]     # TAG names should always be lowercase
---

# Disable ActiveSync for mailbox
```powershell
# Установка параметров скрипта
$OUName = "OU=DisableUsers,DC=Security,DC=local"
$ExchangeServer = "srv-mail-01"
$LogFilePath = "C:\Disable-ActiveSync.log"

# Логирование ошибок
$ErrorActionPreference = "Stop"
$ErrorLogPath = "C:\Disable-ActiveSync-Errors.log"

# Получение списка пользователей
$users = Get-ADUser -SearchBase $OUName -Filter * | Select-Object SamAccountName

# Обход списка пользователей
foreach ($user in $users) {
    Write-Host "Обработка пользователя $($user.SamAccountName)"
    try {
        # Отключение Exchange ActiveSync
        Set-CASMailbox -Identity $user.SamAccountName -ActiveSyncEnabled $false -ErrorAction Stop
        
        # Проверка, отключен ли Exchange ActiveSync
        $casMailbox = Get-CASMailbox -Identity $user.SamAccountName -ErrorAction Stop
        if ($casMailbox.ActiveSyncEnabled -eq $false) {
            Write-Host "Exchange ActiveSync успешно отключен для пользователя $($user.SamAccountName)"
            if ($LogFilePath) {
                Add-Content -Path $LogFilePath -Value "$($user.SamAccountName): Exchange ActiveSync успешно отключен"
            }
        } else {
            Write-Host "Exchange ActiveSync не удалось отключить для пользователя $($user.SamAccountName)"
            if ($LogFilePath) {
                Add-Content -Path $ErrorLogPath -Value "$($user.SamAccountName): Exchange ActiveSync не удалось отключить"
            }
        }
    } catch {
        Write-Host "Ошибка при отключении Exchange ActiveSync для пользователя $($user.SamAccountName): $_"
        if ($LogFilePath) {
            Add-Content -Path $ErrorLogPath -Value "$($user.SamAccountName): $_"
        }
    }
}
```