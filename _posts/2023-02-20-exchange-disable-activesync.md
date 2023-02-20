---
title: Disable ActiveSync for mailbox
date: 2023-02-20 18:12:00 +0300
categories: [microsoft, msexchange, script]
tags: [msexchange, activesync, ps1]     # TAG names should always be lowercase
---

# Disable ActiveSync for mailbox
It is generally a best practice to disable ActiveSync for disabled users in a company's Active Directory environment for several reasons:

> 1. Security: Disabling ActiveSync for disabled users prevents them from accessing their Exchange mailboxes through mobile devices, which can help prevent unauthorized access to company data. This is especially important for employees who have been terminated or are no longer with the company.
> 2. Compliance: Disabling ActiveSync for disabled users can help maintain compliance with regulations such as HIPAA, GDPR, or other privacy laws, which require that organizations safeguard sensitive data and prevent unauthorized access.
> 3. Resource management: Disabling ActiveSync for disabled users can help free up resources on Exchange servers, such as CPU, memory, and storage, which can be used to support active users.
> 4. Licensing: Depending on the licensing model used by an organization, disabling ActiveSync for disabled users can help reduce licensing costs by freeing up licenses that would otherwise be used by inactive users.

In summary, disabling ActiveSync for disabled users helps improve security, compliance, resource management, and licensing in an organization's Exchange environment.
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