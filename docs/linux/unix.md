---
layout: default
title: unix
has_children: false
permalink: /docs/kb/linux/unix
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

# auditd
## Chapitre 12. Auditing the system
<https://docs.redhat.com/fr/documentation/red_hat_enterprise_linux/9/html/security_hardening/auditing-the-system_security-hardening>
## Linux audit userspace repository
<https://github.com/linux-audit/audit-userspace>

# /etc/skel

:robot:

`/etc/skel` — это каталог в UNIX-подобных операционных системах, который используется для создания начальных (скелетных) файлов в домашнем каталоге нового пользователя. Когда создается новый пользователь, файлы и каталоги из `/etc/skel` копируются в его домашний каталог.

### Основные характеристики `/etc/skel`:

1. **Предназначение**:
   - Содержит шаблоны файлов, которые должны быть скопированы в домашний каталог каждого нового пользователя.
   - Обычно включает файлы конфигурации, такие как `.bashrc`, `.profile`, `.bash_profile`, `.bash_logout` и другие.

2. **Содержание**:
   - Файлы и каталоги, которые автоматически создаются для каждого нового пользователя.
   - Могут включать пользовательские настройки, скрипты и другие файлы, которые должны быть доступны пользователю с самого начала.

3. **Использование**:
   - Администраторы могут настраивать содержимое `/etc/skel` для всех новых пользователей, добавляя или изменяя файлы по мере необходимости.
   - Например, можно добавить общие настройки среды, скрипты начальной настройки, шаблоны документов и т.д.

### Пример содержимого `/etc/skel`:

```bash
/etc/skel
|-- .bash_logout
|-- .bashrc
|-- .profile
|-- .config/
|-- Documents/
|-- Downloads/
```

### Пример использования:

1. **Создание нового пользователя**:
   При создании нового пользователя с помощью команды `useradd` или `adduser`, файлы из `/etc/skel` копируются в домашний каталог нового пользователя.

   ```bash
   sudo useradd -m newuser
   ```

   В результате в домашнем каталоге `/home/newuser` появятся файлы и каталоги из `/etc/skel`.

2. **Настройка `/etc/skel`**:
   Администратор может добавить или изменить файлы в `/etc/skel` по своему усмотрению. Например:

   ```bash
   sudo cp /path/to/custom/.bashrc /etc/skel/.bashrc
   sudo mkdir /etc/skel/Templates
   sudo cp /path/to/template.doc /etc/skel/Templates/
   ```

Таким образом, `/etc/skel` предоставляет централизованный способ настройки начальной среды новых пользователей, что упрощает управление и стандартизацию пользовательских настроек в системе.

# Добавление сервтификата в Debian
/usr/local/share/ca-certificates/ Directory of local CA certificates (with .crt extension).
```
sudo cp trusted_root_ca.crt /usr/local/share/ca-certificates/
```
update-ca-certificates is a program that updates the directory /etc/ssl/certs to hold SSL certificates and generates ca-certificates.crt, a concatenated single-file list of certificates.
```
sudo update-ca-certificates -v
```

