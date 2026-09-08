---
layout: default
title: rsync
has_children: false
permalink: /docs/kb/linux/rsync
parent: linux
grand_parent: kb
has_toc: true
---
Общие заметки про UNIX, Linux
<details close markdown="block">
  <summary>
    Содержание
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

# Rsync Shared Server Manual

Полное практическое руководство по работе с общим rsync-сервером в режиме **rsync daemon**, когда ресурс доступен по адресу вида:

```text
rsync://user@host/path
```

Пример:

```text
rsync://backup@10.10.10.20/files
```

или:

```text
rsync://backup@rsync.example.org/files
```

> Важно: `rsync://...` - это не rsync поверх SSH. Это подключение к `rsync daemon`, который обычно слушает TCP-порт `873`.

---

## 1. Основная модель работы

В режиме rsync daemon сервер публикует один или несколько **модулей**.

Пример конфигурации сервера:

```ini
[files]
    path = /srv/rsync/files
    read only = false
    auth users = backup
    secrets file = /etc/rsyncd.secrets
```

В этом случае адрес:

```text
rsync://backup@host/files
```

означает:

- `backup` - пользователь rsync daemon;
- `host` - DNS-имя или IP-адрес сервера;
- `files` - имя rsync-модуля;
- `/srv/rsync/files` - реальный путь на сервере, скрытый от клиента.

Далее внутри модуля можно обращаться к подкаталогам:

```text
rsync://backup@host/files/projects/
rsync://backup@host/files/backups/server01/
```

---

# 2. Установка rsync

## Debian / Ubuntu / Astra Linux

```bash
sudo apt update
sudo apt install rsync
```

## RHEL / Rocky / AlmaLinux / Oracle Linux

```bash
sudo dnf install rsync
```

Для старых систем:

```bash
sudo yum install rsync
```

## Fedora

```bash
sudo dnf install rsync
```

## Alpine Linux

```bash
sudo apk add rsync
```

## Проверка версии

```bash
rsync --version
```

---

# 3. Проверка доступности сервера

По умолчанию rsync daemon использует:

```text
TCP/873
```

Проверить TCP-порт:

```bash
nc -vz host 873
```

или:

```bash
telnet host 873
```

или:

```bash
nmap -Pn -p 873 host
```

Пример:

```bash
nc -vz rsync.example.org 873
```

Если сервер работает на нестандартном порту, например `8873`:

```bash
nc -vz host 8873
```

---

# 4. Получение списка опубликованных модулей

Если сервер разрешает просмотр списка модулей:

```bash
rsync rsync://host/
```

или:

```bash
rsync rsync://user@host/
```

Пример результата:

```text
files           Shared files
backup          Backup storage
software        Software repository
```

После этого модуль можно использовать как:

```text
rsync://user@host/files
```

---

# 5. Просмотр содержимого удаленного модуля

Просмотреть список файлов:

```bash
rsync rsync://user@host/files/
```

Подробный список:

```bash
rsync -av rsync://user@host/files/
```

Только просмотр без копирования:

```bash
rsync --list-only rsync://user@host/files/
```

Пример:

```bash
rsync --list-only rsync://backup@10.10.10.20/files/
```

Просмотр конкретного каталога:

```bash
rsync --list-only rsync://backup@10.10.10.20/files/projects/
```

---

# 6. Аутентификация

Rsync daemon может использовать собственную аутентификацию.

Пользователь из URI:

```text
rsync://backup@host/files
```

не обязан существовать как Linux-пользователь на сервере.

Обычно сервер содержит:

```ini
auth users = backup
secrets file = /etc/rsyncd.secrets
```

а файл `/etc/rsyncd.secrets` выглядит так:

```text
backup:StrongPassword
```

---

# 7. Интерактивный ввод пароля

Самый простой вариант:

```bash
rsync -av rsync://backup@host/files/ ./files/
```

Rsync запросит:

```text
Password:
```

Пароль при вводе не отображается.

---

# 8. Использование файла с паролем

Для автоматизации рекомендуется использовать:

```bash
--password-file=/path/to/password-file
```

Создать файл:

```bash
printf '%s\n' 'StrongPassword' > ~/.rsync-password
```

Выставить права:

```bash
chmod 600 ~/.rsync-password
```

Использование:

```bash
rsync -av \
  --password-file="$HOME/.rsync-password" \
  rsync://backup@host/files/ \
  ./files/
```

> В password-файле для клиента указывается только пароль, без имени пользователя.

Правильно:

```text
StrongPassword
```

Неправильно:

```text
backup:StrongPassword
```

---

# 9. Переменная окружения RSYNC_PASSWORD

Можно передать пароль через переменную:

```bash
export RSYNC_PASSWORD='StrongPassword'
rsync -av rsync://backup@host/files/ ./files/
unset RSYNC_PASSWORD
```

Одной командой:

```bash
RSYNC_PASSWORD='StrongPassword' rsync -av rsync://backup@host/files/ ./files/
```

Недостатки:

- секрет присутствует в окружении процесса;
- может попасть в shell history, если пароль указан прямо в командной строке;
- может быть доступен другим процессам в зависимости от ОС и настроек.

Для cron/systemd обычно предпочтительнее `--password-file` с правами `600`.

---

# 10. Безопасный ввод пароля в переменную

Если пароль нужно получить интерактивно без отображения:

```bash
read -rsp 'Rsync password: ' RSYNC_PASSWORD
echo
export RSYNC_PASSWORD
rsync -av rsync://backup@host/files/ ./files/
unset RSYNC_PASSWORD
```

Или:

```bash
read -rsp 'Rsync password: ' RSYNC_PASSWORD
echo
RSYNC_PASSWORD="$RSYNC_PASSWORD" rsync -av rsync://backup@host/files/ ./files/
unset RSYNC_PASSWORD
```

---

# 11. Скачивание файлов с сервера

Общий синтаксис:

```bash
rsync [options] rsync://user@host/module/path/ /local/path/
```

Пример:

```bash
rsync -av \
  --password-file="$HOME/.rsync-password" \
  rsync://backup@host/files/ \
  /data/files/
```

Это направление:

```text
SERVER -> CLIENT
```

---

# 12. Загрузка файлов на сервер

Общий синтаксис:

```bash
rsync [options] /local/path/ rsync://user@host/module/path/
```

Пример:

```bash
rsync -av \
  --password-file="$HOME/.rsync-password" \
  /data/files/ \
  rsync://backup@host/files/
```

Направление:

```text
CLIENT -> SERVER
```

Для загрузки серверный модуль должен разрешать запись:

```ini
read only = false
```

---

# 13. Критическая разница между `dir` и `dir/`

Это одна из наиболее важных особенностей rsync.

## Вариант 1

```bash
rsync -av /data/project/ rsync://user@host/files/project/
```

Копируется **содержимое** `/data/project/`.

Например:

```text
/data/project/file1
/data/project/file2
```

окажутся как:

```text
files/project/file1
files/project/file2
```

## Вариант 2

```bash
rsync -av /data/project rsync://user@host/files/
```

Копируется сам каталог `project`.

Результат:

```text
files/project/file1
files/project/file2
```

Практическое правило:

```text
source/ = содержимое каталога
source  = сам каталог
```

---

# 14. Рекомендуемый базовый набор параметров

Для обычной синхронизации:

```bash
rsync -avh --progress \
  --password-file="$HOME/.rsync-password" \
  /data/files/ \
  rsync://backup@host/files/
```

Значение параметров:

| Параметр | Назначение |
|---|---|
| `-a` | archive mode |
| `-v` | подробный вывод |
| `-h` | человекочитаемые размеры |
| `--progress` | прогресс передачи |

`-a` приблизительно соответствует:

```text
-rlptgoD
```

то есть включает:

- рекурсивную обработку;
- сохранение symbolic links;
- timestamps;
- permissions;
- group;
- owner;
- device/special files.

Некоторые атрибуты могут не применяться сервером из-за прав процесса rsync daemon или настроек модуля.

---

# 15. Более удобный прогресс

Для крупных копирований:

```bash
rsync -avh --info=progress2 \
  --password-file="$HOME/.rsync-password" \
  /data/files/ \
  rsync://backup@host/files/
```

`--info=progress2` показывает общий прогресс всей операции.

---

# 16. Dry-run

Перед опасной синхронизацией рекомендуется выполнять тестовый запуск:

```bash
rsync -avhn \
  --password-file="$HOME/.rsync-password" \
  /data/files/ \
  rsync://backup@host/files/
```

или:

```bash
rsync -avh --dry-run \
  --password-file="$HOME/.rsync-password" \
  /data/files/ \
  rsync://backup@host/files/
```

Особенно важно использовать `--dry-run` вместе с:

```text
--delete
--delete-excluded
--remove-source-files
```

---

# 17. Зеркальная синхронизация с удалением

Чтобы удалить на destination файлы, которых больше нет на source:

```bash
rsync -avh --delete \
  --password-file="$HOME/.rsync-password" \
  /data/files/ \
  rsync://backup@host/files/
```

Перед использованием:

```bash
rsync -avhn --delete ...
```

Варианты удаления:

```text
--delete-before
--delete-during
--delete-delay
--delete-after
```

Для большинства случаев достаточно:

```bash
--delete
```

---

# 18. Защита от случайного удаления

Можно ограничить максимальное число удаляемых файлов:

```bash
--max-delete=100
```

Пример:

```bash
rsync -avh --delete --max-delete=100 \
  --password-file="$HOME/.rsync-password" \
  /data/files/ \
  rsync://backup@host/files/
```

Для критичных backup-задач это полезная защитная мера.

---

# 19. Исключение файлов и каталогов

Пример:

```bash
rsync -av \
  --exclude='*.tmp' \
  --exclude='cache/' \
  --exclude='.git/' \
  /data/project/ \
  rsync://backup@host/files/project/
```

Несколько исключений:

```bash
--exclude='*.log' \
--exclude='*.tmp' \
--exclude='cache/'
```

---

# 20. Файл исключений

Создать:

```text
/etc/rsync/exclude.txt
```

Содержимое:

```text
*.tmp
*.log
.cache/
.git/
node_modules/
__pycache__/
```

Использование:

```bash
rsync -av \
  --exclude-from=/etc/rsync/exclude.txt \
  /data/project/ \
  rsync://backup@host/files/project/
```

---

# 21. Include / exclude

Пример - передавать только `.conf`:

```bash
rsync -av \
  --include='*/' \
  --include='*.conf' \
  --exclude='*' \
  /etc/ \
  rsync://backup@host/configs/
```

Порядок правил имеет значение.

---

# 22. Ограничение размера файлов

Не передавать файлы больше 1 ГБ:

```bash
--max-size=1G
```

Не передавать файлы меньше 1 МБ:

```bash
--min-size=1M
```

Пример:

```bash
rsync -av --max-size=1G /data/ rsync://backup@host/files/
```

---

# 23. Ограничение скорости

Например, ограничить передачу примерно до 10 МБ/с:

```bash
--bwlimit=10M
```

Пример:

```bash
rsync -avh --bwlimit=10M \
  /data/files/ \
  rsync://backup@host/files/
```

Другой пример:

```bash
--bwlimit=50M
```

---

# 24. Возобновление передачи больших файлов

Полезные параметры:

```bash
--partial
--append-verify
```

## `--partial`

Не удаляет частично переданный файл при обрыве:

```bash
rsync -avh --partial ...
```

## `--partial-dir`

Частичные файлы можно хранить отдельно:

```bash
--partial-dir=.rsync-partial
```

Пример:

```bash
rsync -avh \
  --partial \
  --partial-dir=.rsync-partial \
  /data/ \
  rsync://backup@host/files/
```

## `--append-verify`

Для очень больших файлов:

```bash
rsync -avh --append-verify bigfile.iso rsync://backup@host/files/
```

Этот режим продолжает передачу существующего файла и проверяет корректность данных.

---

# 25. Проверка содержимого по checksum

По умолчанию rsync определяет изменения в основном по размеру и времени модификации.

Чтобы сравнивать контрольные суммы:

```bash
rsync -avc ...
```

или:

```bash
rsync -av --checksum ...
```

Недостаток:

- требуется прочитать содержимое всех файлов с обеих сторон;
- значительно выше нагрузка CPU и дисков;
- операция может стать существенно медленнее.

Использовать, когда важнее максимальная точность сравнения, а не скорость.

---

# 26. Только обновление более новых файлов

```bash
--update
```

или:

```bash
-u
```

Пример:

```bash
rsync -avu /data/ rsync://backup@host/files/
```

Rsync не будет перезаписывать файлы на destination, если там находится более новый файл.

---

# 27. Игнорирование уже существующих файлов

```bash
--ignore-existing
```

Пример:

```bash
rsync -av --ignore-existing \
  /data/ \
  rsync://backup@host/files/
```

Полезно для одноразовой догрузки файлов.

---

# 28. Передавать только существующие файлы

```bash
--existing
```

Rsync будет обновлять только те файлы, которые уже есть на destination.

---

# 29. Не перезаписывать более новые destination-файлы

```bash
--update
```

Но следует учитывать, что timestamps между системами должны быть корректными.

Желательно использовать NTP/chrony.

---

# 30. Сохранение удаленных или заменяемых файлов

Для backup-сценариев полезен:

```bash
--backup
```

Можно задать каталог:

```bash
--backup-dir=/path/to/archive
```

В rsync daemon destination это зависит от доступной серверу файловой структуры и настроек модуля.

Пример:

```bash
rsync -av --delete \
  --backup \
  --backup-dir=.deleted-$(date +%F) \
  /data/ \
  rsync://backup@host/files/
```

---

# 31. Сжатие данных

Параметр:

```bash
-z
```

Пример:

```bash
rsync -avz /data/ rsync://backup@host/files/
```

Имеет смысл для:

- текстовых файлов;
- конфигураций;
- исходного кода;
- логов.

Обычно почти бесполезно для уже сжатых форматов:

```text
.zip
.7z
.gz
.jpg
.png
.mp4
.iso
```

На быстрых LAN-сетях `-z` иногда даже снижает производительность из-за CPU overhead.

---

# 32. Права доступа

Archive mode `-a` пытается сохранить:

```text
permissions
owner
group
timestamps
symlinks
```

Однако в daemon mode конечный результат зависит от:

- UID процесса rsync daemon;
- `uid` / `gid` в `rsyncd.conf`;
- filesystem permissions;
- `read only`;
- `fake super`;
- ACL;
- SELinux/AppArmor;
- параметров модуля.

---

# 33. Не сохранять owner и group

Если это не требуется:

```bash
--no-owner --no-group
```

Пример:

```bash
rsync -av --no-owner --no-group \
  /data/ \
  rsync://backup@host/files/
```

---

# 34. Не сохранять permissions

```bash
--no-perms
```

Пример:

```bash
rsync -av --no-perms /data/ rsync://backup@host/files/
```

---

# 35. Принудительно назначить права

Можно использовать `--chmod`.

Например:

```bash
--chmod=Du=rwx,Dgo=rx,Fu=rw,Fgo=r
```

где:

- `D` - directories;
- `F` - files.

Пример:

```bash
rsync -av \
  --chmod=Du=rwx,Dgo=rx,Fu=rw,Fgo=r \
  /data/ \
  rsync://backup@host/files/
```

---

# 36. Symbolic links

Archive mode сохраняет symlink как symlink:

```bash
-a
```

Явно:

```bash
-l
```

Если нужно копировать содержимое, на которое указывает symlink:

```bash
-L
```

или:

```bash
--copy-links
```

Осторожно: это может привести к копированию данных за пределами исходного дерева.

---

# 37. Hard links

Сохранение hard links:

```bash
-H
```

Пример:

```bash
rsync -aH /data/ rsync://backup@host/files/
```

Это увеличивает потребление памяти при большом количестве файлов.

---

# 38. ACL

Для POSIX ACL:

```bash
-A
```

Пример:

```bash
rsync -aA /data/ rsync://backup@host/files/
```

Поддержка должна быть доступна и на сервере, и на файловой системе назначения.

---

# 39. Extended attributes

Для xattr:

```bash
-X
```

Пример:

```bash
rsync -aX /data/ rsync://backup@host/files/
```

Для ACL + xattr:

```bash
rsync -aAX /data/ rsync://backup@host/files/
```

---

# 40. Практический Linux backup-профиль

Для Linux-файловой системы часто используют:

```bash
rsync -aHAX --numeric-ids \
  --delete \
  --info=progress2 \
  --password-file="$HOME/.rsync-password" \
  /data/ \
  rsync://backup@host/backup/server01/
```

Однако такие параметры требуют соответствующих прав и серверной поддержки.

---

# 41. Numeric IDs

```bash
--numeric-ids
```

Rsync будет работать с UID/GID как с числовыми значениями и не пытаться преобразовывать их через локальные имена пользователей.

Полезно при backup между системами с различными `/etc/passwd` и `/etc/group`.

---

# 42. Работа с нестандартным портом

Для daemon mode:

```bash
--port=8873
```

Пример:

```bash
rsync -av --port=8873 \
  rsync://backup@host/files/ \
  ./files/
```

---

# 43. Таймаут соединения

Таймаут установки соединения:

```bash
--contimeout=10
```

Пример:

```bash
rsync -av --contimeout=10 ...
```

Таймаут отсутствия I/O:

```bash
--timeout=300
```

Пример:

```bash
rsync -av \
  --contimeout=10 \
  --timeout=300 \
  /data/ \
  rsync://backup@host/files/
```

---

# 44. IPv4 и IPv6

Принудительно IPv4:

```bash
-4
```

Пример:

```bash
rsync -4 -av rsync://backup@host/files/ ./files/
```

Принудительно IPv6:

```bash
-6
```

IPv6 URI:

```text
rsync://user@[2001:db8::10]/files/
```

---

# 45. Прокси

Rsync daemon может использовать SOCKS-прокси через сторонние инструменты, например `proxychains`, но нативная поддержка зависит от конкретного сценария.

Пример:

```bash
proxychains4 rsync -av rsync://backup@host/files/ ./files/
```

Для production рекомендуется по возможности использовать VPN или другой защищенный сетевой канал.

---

# 46. Шифрование трафика

Критически важно понимать:

```text
rsync://
```

сам по себе **не шифрует трафик**.

Пароль rsync daemon также не превращает протокол в защищенный TLS-канал.

Для недоверенных сетей используйте один из вариантов:

1. VPN;
2. WireGuard;
3. IPsec;
4. SSH tunnel;
5. TLS-wrapper;
6. stunnel;
7. rsync поверх SSH вместо daemon mode.

---

# 47. Rsync daemon vs rsync over SSH

## Rsync daemon

```text
rsync://user@host/module/path
```

Типичный порт:

```text
873/tcp
```

Аутентификация:

```text
rsync auth users + secrets file
```

## Rsync over SSH

```bash
rsync -av /data/ user@host:/srv/data/
```

Типичный порт:

```text
22/tcp
```

Аутентификация:

```text
SSH password / SSH key / certificate
```

Это принципиально разные режимы.

---

# 48. SSH tunnel для rsync daemon

Если требуется оставить daemon mode, но передавать данные через SSH tunnel:

На клиенте:

```bash
ssh -L 8873:127.0.0.1:873 user@host
```

После этого в другом терминале:

```bash
rsync -av \
  --port=8873 \
  rsync://backup@127.0.0.1/files/ \
  ./files/
```

Тогда rsync-соединение идет через локальный SSH tunnel.

---

# 49. Передача одного файла

Upload:

```bash
rsync -av file.zip rsync://backup@host/files/
```

Download:

```bash
rsync -av rsync://backup@host/files/file.zip ./
```

---

# 50. Передача нескольких файлов

```bash
rsync -av file1 file2 file3 rsync://backup@host/files/
```

---

# 51. Передача списка файлов

Создать файл:

```text
files.txt
```

Пример содержимого:

```text
etc/app.conf
scripts/backup.sh
data/file.dat
```

Использование:

```bash
rsync -av \
  --files-from=files.txt \
  /source/root/ \
  rsync://backup@host/files/
```

---

# 52. Файлы с пробелами и специальными символами

Rsync корректно работает с именами файлов с пробелами при правильном quoting.

Например:

```bash
rsync -av '/data/My File.txt' rsync://backup@host/files/
```

Рекомендуется всегда заключать shell-пути с переменными в кавычки:

```bash
rsync -av "$SOURCE/" "$DESTINATION"
```

---

# 53. Пустые каталоги

По умолчанию rsync передает пустые каталоги при рекурсивной работе.

Исключить пустые каталоги:

```bash
-m
```

или:

```bash
--prune-empty-dirs
```

---

# 54. Только каталоги

Пример создания структуры каталогов без передачи файлов:

```bash
rsync -avf '+ */' -f '- *' \
  /data/ \
  rsync://backup@host/files/
```

---

# 55. Удаление исходных файлов после передачи

```bash
--remove-source-files
```

Пример:

```bash
rsync -av --remove-source-files \
  /data/outgoing/ \
  rsync://backup@host/incoming/
```

Rsync удаляет успешно переданные **файлы**, но обычно не удаляет пустые каталоги автоматически.

Удалить пустые каталоги отдельно:

```bash
find /data/outgoing -type d -empty -delete
```

Использовать этот режим осторожно.

---

# 56. Проверка результата без повторной передачи

Например:

```bash
rsync -avnc \
  --password-file="$HOME/.rsync-password" \
  /data/ \
  rsync://backup@host/files/
```

Если вывода изменений нет, дерево файлов совпадает по checksum.

---

# 57. Статистика

```bash
--stats
```

Пример:

```bash
rsync -av --stats /data/ rsync://backup@host/files/
```

Показывает:

- количество файлов;
- общий размер;
- реально переданный объем;
- скорость;
- эффективность delta-transfer.

---

# 58. Itemized changes

Очень полезный режим диагностики:

```bash
-i
```

или:

```bash
--itemize-changes
```

Пример:

```bash
rsync -avi /data/ rsync://backup@host/files/
```

Позволяет понять, почему конкретный файл был обновлен.

---

# 59. Формат itemized output

Пример:

```text
>f.st...... file.txt
```

Приблизительно:

- `>` - файл передается на destination;
- `<` - файл передается с destination;
- `f` - обычный файл;
- `s` - изменился размер;
- `t` - изменился timestamp;
- `p` - permissions;
- `o` - own