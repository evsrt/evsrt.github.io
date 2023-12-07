---
layout: default
title: access
parent: KB
---
# Mutual TLS MTLS
Mutual TLS (MTLS) — это расширение TLS, которое не только подтверждает легитимность сервера, но и легитимность клиента. Это означает, что при использовании MTLS обе стороны должны представить свои сертификаты для аутентификации перед взаимодействием. Таким образом, MTLS предоставляет более высокий уровень безопасности, потому что обе стороны должны быть взаимно уверены в идентичности друг друга.

Источник: https://investim.guru/news/chem-otlichayutsya-tls-i-mtls-v-chem-zaklyuchaetsya-raznitsa

Пример конфигурации nginx
```ruby
server {
    listen 443 ssl;

    ssl_certificate     /path/to/server.crt;
    ssl_certificate_key /path/to/server.key;
    ssl_client_certificate /path/to/ca.crt;
    ssl_verify_client on;

    # Other SSL configurations...

    location / {
        # Your application configuration...
    }
}
```
```ruby
server {
    listen              443 ssl;
    server_name         myserver.internal.net;
    ssl_certificate     server.crt;
    ssl_certificate_key server.key;
    ssl_protocols       TLSv1.2 TLSv1.3;
    ssl_ciphers         HIGH:!aNULL:!MD5;
    # ...
}
```
```ruby
server {
    listen                 443 ssl;
    server_name            myserver.internal.net;
    # ...
    ssl_client_certificate /etc/nginx/client_certs/ca.crt;
    ssl_verify_client      optional;

    # ...


    location / {
      if ($ssl_client_verify != SUCCESS) {
        return 403;
      }
    # ...
}
```
<https://smallstep.com/hello-mtls/doc/combined/nginx/php>

# JWT JSON Web Token
JSON Web Token (JWT) — это открытый стандарт (RFC 7519) для создания токенов доступа, основанный на формате JSON. Как правило, используется для передачи данных для аутентификации в клиент-серверных приложениях. Токены создаются сервером, подписываются секретным ключом и передаются клиенту, который в дальнейшем использует данный токен для подтверждения подлинности аккаунта.

<https://habr.com/ru/articles/340146/>

<https://ru.wikipedia.org/wiki/JSON_Web_Token>
# Блокировка доступа к файлам bitrix в .htaccess
```ruby
# /bitrix/tools/

<Files ~ "^(html_editor_action|mail_entry|upload)\.php$>
    deny from all
</Files>

# /bitrix/tools/vote/

<Files ~ "^(uf)\.php$>
    deny from all
</Files>

# /bitrix/modules/main/include/

<Files ~ "^(virtual_file_system)\.php$>
    deny from all
</Files>

# /bitrix/components/bitrix/sender.mail.editor/

<Files ~ "^(ajax)\.php$>
    deny from all
</Files>

# /bitrix/admin/

<Files ~ "^(site_checker)\.php$>
    deny from all
</Files>

# Пример блокировки с исключением

<Files "filename.txt">
    Order Allow,Deny
    Allow 192.168.0.1
    Deny from all
</Files>

<Files "anotherfile.html">
    Order Allow,Deny
    Deny from all
</Files>
```

# Блокировка POST запросов для bitrix
```ruby
# conf.d/bitrix_block.conf

location = /bitrix/tools/upload.php {
        if ($request_method = POST) {
            return 403;
        }
    }

    location = /bitrix/tools/mail_entry.php {
        if ($request_method = POST) {
            return 403;
        }
    }

    location = /bitrix/modules/main/include/virtual_file_system.php {
        if ($request_method = POST) {
            return 403;
        }
    }

    location = /bitrix/components/bitrix/sender.mail.editor/ajax.php {
        if ($request_method = POST) {
            return 403;
        }
    }

    location = /bitrix/tools/vote/uf.php {
        if ($request_method = POST) {
            return 403;
        }
    }

    location = /bitrix/tools/html_editor_action.php {
        if ($request_method = POST) {
            return 403;
        }
    }

    location = /bitrix/admin/site_checker.php {
        if ($request_method = POST) {
            return 403;
        }
    }
```
