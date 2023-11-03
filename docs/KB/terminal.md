---
layout: default
title: terminal
parent: KB
---
# Полезные команды
## date
```bash
# Показать дату в формате  MM/DD/YYYY
date '+%x'
# 07/24/2023
```
## dig
```bash
# через какой ip ты выходишь
dig +short myip.opendns.com @resolver1.opendns.com
# 77.77.7.7
```
## id
```bash
# user id
id -u git
# 1004
```
## systemctl
```bash
# Display running services
sudo systemctl list-units --type=service --state=running
```
## nmap
```bash
# Информация о сертификате домена
nmap -p 443 --script ssl-cert ya.ru
```
## unzip
```bash
# unzip .
unzip filename.zip
# unzip to directory
unzip filename.zip -d /path/
# list of files
unzip -l filename.zip
# overwrite existing files
unzip -o filename.zip
```

## ls
Отобразить имена файлов в текущей директории 
Команда	
```bash
ls -p | grep -v /
```
Вывод
```bash
installer-7.3
installer-7.3.md5
installer-7.3.sha256
```

## tar
```bash
# Добавить в архив директорию
tar -czvf archive_name.tar.gz /etc/yum.repos.d/
```
```bash
# Извлечь архив
tar -xzvf archive_name.tar.gz
```

## find
```
find / -name "*error*" -print
```
Поиск строки в директории
```bash
sudo grep -r "1.1.1.1" /etc/nginx
# /etc/nginx/conf.d/acl.conf:deny 1.1.1.1;
```
Поиск строки в файле 
```bash
sudo find /etc/nginx -type f -exec grep -l "1.1.1.1" {} \;
# /etc/nginx/conf.d/acl.conf
```

## du
Топ 10 файлов по размеру
```bash
sudo du -Sh /  --exclude=/proc --exclude=/dev --exclude=/boot --exclude=/bin --exclude=/media --exclude=/mnt --exclude=/sys | sort -rh | head -n 10
```
## egrep
```bash
# this pattern matches number 123-45-6789
egrep '(\d{3})-\d[2}-\d{4}' data.txt
```

## stat
```bash
stat -c "%y $U" /path/to/file
```
