---
layout: default
title: terminal
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
# netstat
<https://en.wikipedia.org/wiki/Netstat>
  
```
    -a: Показывает все активные подключения и прослушивающие порты.
    -t: Показывает только TCP подключения и порты.
    -u: Показывает только UDP подключения и порты.
    -n: Выводит адреса и порты в числовом формате, а не в виде имен.
    -l: в состоянии LISTEN
    -p: Показывает имя программы или процесса, связанного с каждым подключением.
    -r: Показывает таблицу маршрутизации.
    -s: Показывает статистику по протоколам.
    -c: Показывает результаты через регулярные интервалы времени.
    -e: Выводит расширенную информацию, такую как UID и PID.
    -g: Показывает групповые политики.
    -i: Показывает информацию об интерфейсах.
    -W: Показывает широкие выводы, позволяя увидеть длинные имена и адреса без усечения.
```
 - Часто используемые ключи
```
sudo netstat -tunlp
```
# lsof
<https://en.wikipedia.org/wiki/Lsof>

Команда lsof -iTCP -sTCP:LISTEN -n -P выводит список всех открытых файлов (List Open Files) в системе, фильтруя результаты по сокетам TCP, и показывает только прослушиваемые (LISTEN) соединения. Расшифровка опций в команде:

    -iTCP: Фильтрация результатов для сокетов TCP.
    -sTCP:LISTEN: Показывает только прослушиваемые (LISTEN) соединения.
    -n: Показывает числовые адреса вместо их разрешения в имена.
    -P: Отключает разрешение портов на имена (выводит числовые значения портов).
    
 - Пример
```bash
sudo lsof -iTCP -sTCP:LISTEN -n -P
```

 - Вывод:
```bash
COMMAND      PID   USER   FD   TYPE    DEVICE SIZE/OFF NODE NAME
sshd         964   root    3u  IPv4     25055      0t0  TCP *:22 (LISTEN)
sshd         964   root    4u  IPv6     25057      0t0  TCP *:22 (LISTEN)
```
В этом примере видно, что процесс с именем "sshd" слушает порт 22 (SSH) на всех интерфейсах.

# dig
<https://en.wikipedia.org/wiki/Dig_(command)>

## Узнать свой публичный адрес
Google DNS Service
IPv4
```
dig -4 TXT +short o-o.myaddr.l.google.com @ns1.google.com
```
IPv6
```
dig -6 TXT +short o-o.myaddr.l.google.com @ns1.google.com
```
Cloudflare DNS Service
```
dig TXT CH +short whoami.cloudflare @1.0.0.1
```

OpenDNS Service
```
dig +short myip.opendns.com @resolver1.opendns.com
```
- Пример
```
dig +short myip.opendns.com @resolver1.opendns.com
77.77.7.7
```

# find
## Изменение мандатной метки для всех найдненных директорий в профиле пользователя
<https://wiki.astralinux.ru/pages/viewpage.action?pageId=48763550>
```bash
find /home/username -type d -exec pdpl-file :63:0x0:relax {} \;
```

# setuid, setgid, sticky bit
`setuid`, `setgid` и `sticky bit` — это специальные флаги в файловой системе UNIX/Linux, которые управляют поведением файлов и каталогов.

### setuid (Set User ID)

Флаг `setuid` используется для выполнения файла с привилегиями владельца, а не пользователя, который запустил файл. Это полезно для программ, которые требуют выполнения с повышенными привилегиями.

#### Пример:
1. Установим `setuid` для программы:

   ```sh
   sudo chmod u+s /path/to/program
   ```

2. Проверим права доступа:

   ```sh
   ls -l /path/to/program
   ```

   Вывод будет что-то вроде:

   ```
   -rwsr-xr-x 1 root root 12345 Jun 25 12:34 /path/to/program
   ```

   Обратите внимание на `s` в правах владельца (`rws`), что означает, что флаг `setuid` установлен.

### setgid (Set Group ID)

Флаг `setgid` работает аналогично `setuid`, но касается групп. Для файлов это означает, что программа будет выполняться с правами группы владельца файла. Для директорий это означает, что все новые файлы и каталоги, созданные в этой директории, будут наследовать группу этой директории.

#### Пример:
1. Установим `setgid` для директории:

   ```sh
   sudo chmod g+s /path/to/directory
   ```

2. Проверим права доступа:

   ```sh
   ls -ld /path/to/directory
   ```

   Вывод будет что-то вроде:

   ```
   drwxr-sr-x 2 user group 4096 Jun 25 12:34 /path/to/directory
   ```

   Обратите внимание на `s` в правах группы (`r-s`), что означает, что флаг `setgid` установлен.

### sticky bit
<https://ru.m.wikipedia.org/wiki/Sticky_bit>

Флаг `stickyvbit` используется для директорий и обеспечивает, что только владелец файла может удалить или переименовать файл внутри этой директории, даже если у других пользователей есть права на запись в эту директорию. Обычно используется для общих директорий, таких как `/tmp`.

#### Пример:
1. Установим `stickybit` для директории:

   ```sh
   sudo chmod +t /path/to/directory
   ```

2. Проверим права доступа:

   ```sh
   ls -ld /path/to/directory
   ```

   Вывод будет что-то вроде:

   ```
   drwxrwxrwt 2 user group 4096 Jun 25 12:34 /path/to/directory
   ```

   Обратите внимание на `t` в правах других (`rwt`), что означает, что флаг `stickybit` установлен.

Эти специальные флаги помогают управлять безопасностью и доступом в многопользовательских системах, обеспечивая более гибкий и контролируемый доступ к файлам и каталогам.