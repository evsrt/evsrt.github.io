---
layout: default
title: ssh
parent: KB
---

# Настройки подключения ssh для пользователя
> Если вы регулярно подключаетесь к нескольким удаленным системам через SSH, вы обнаружите, что запомнить все удаленные IP-адреса, разные имена пользователей,
> нестандартные порты и различные параметры командной строки сложно, если не невозможно.
> OpenSSH позволяет вам настроить файл конфигурации для каждого пользователя, в котором вы можете хранить различные параметры SSH для каждого удаленного компьютера, к которому вы подключаетесь.

#### Создать файл и назначить права rw для владельца

```bash
touch ~/.ssh/config && chmod 600 ~/.ssh/config
```

#### Пример файла конфигурации:

```bash
# User-specific SSH configuration file (usually ~/.ssh/config)

# Define a custom host entry with a specific identity file
Host myserver
  HostName example.com   # Remote server's address or domain name
  Port 22                # Specify the SSH port (default is 22)
  User myuser            # Your username on the remote server
  IdentityFile ~/.ssh/my_private_key    # Path to your private key

# Enable strict host key checking
Host *
  StrictHostKeyChecking no       # This requires valid host keys
  UserKnownHostsFile /dev/null   # Don`t create file known_hosts

# Дополнительные опции
# Для всех хостов заканчивающихся на local.com подключаться под пользователем myuser
Host *.local.com
    user myuser
    IdentityFile ~/.ssh/my_private_key    # Path to your private key
```

#### Подключение к серверу

```bash
ssh myserver
```
# Добавление открытых ключей для пользвателей
```bash
#!/bin/bash

# Associative array to store the public keys for each user
declare -A public_keys

# Add the public keys for each user
public_keys["user1"]="ssh-rsa ABCDE"
public_keys["user2"]="ssh-rsa EFIJW"

for user in "${!public_keys[@]}"; do
    # Get the public key for the current user
    public_key="${public_keys[$user]}"

    # Check if the authorized_keys file exists for the current user
    if [ -f "/home/$user/.ssh/authorized_keys" ]; then
        # Check if the public key is already present in the authorized_keys file
        if ! grep -qF "$public_key" "/home/$user/.ssh/authorized_keys"; then
            # Append the public key to the authorized_keys file
            echo "$public_key" >> "/home/$user/.ssh/authorized_keys"
            echo "Public key added for $user."
        else
            echo "Public key is already present for $user. Nothing to do."
        fi
    else
        # If the authorized_keys file doesn't exist, create it and add the public key
        mkdir -p "/home/$user/.ssh"
        echo "$public_key" > "/home/$user/.ssh/authorized_keys"
        chmod 600 "/home/$user/.ssh/authorized_keys"
        chown "$user:$user" "/home/$user/.ssh/authorized_keys"
        echo "Public key added for $user."
    fi
done
```
