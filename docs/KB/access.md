---
layout: default
title: access
parent: KB
---
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
