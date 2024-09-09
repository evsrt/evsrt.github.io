---
layout: default
title: nginx
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

# Content Security Policy (CSP)
Для самостоятельной настройки и обучения Content Security Policy (CSP) с помощью Nginx, можно следовать следующему пошаговому плану. Он включает в себя начальную настройку CSP в режиме отчёта, анализ собранных данных и постепенное введение строгой политики.

### Шаг 1: Настройка начальной политики CSP в режиме `Report-Only`

Для начала нужно внедрить политику CSP в режиме отчёта, чтобы не блокировать контент, но собирать информацию о потенциальных нарушениях. Это позволит вам понять, какие ресурсы загружаются на сайте и откуда.

#### Пример начальной политики:
1. Откройте файл конфигурации Nginx для вашего сайта:
    ```bash
    sudo nano /etc/nginx/sites-available/your-site.conf
    ```
   
2. Добавьте следующую строку в секцию `server`, чтобы включить политику CSP в режиме отчётности:

    ```nginx
    server {
        # Ваша существующая конфигурация...

        # Политика CSP в режиме отчёта
        add_header Content-Security-Policy-Report-Only "default-src 'self'; report-uri /csp-report-endpoint";
        
        # Пример директив для HTTPS и защиты от XSS
        add_header X-Content-Type-Options "nosniff";
        add_header X-Frame-Options "DENY";
        add_header X-XSS-Protection "1; mode=block";
    }
    ```

- **`Content-Security-Policy-Report-Only`**: Этот заголовок включает режим отчётов CSP. Он позволяет браузерам не блокировать контент, но отправлять отчёты о нарушениях политики на указанный URL (в данном случае `/csp-report-endpoint`).
- **`report-uri /csp-report-endpoint`**: Указывает URL, на который браузеры будут отправлять отчёты о нарушениях CSP. 

3. Создайте обработчик отчётов о нарушениях. Для этого можно использовать небольшое приложение на Python, PHP или любом другом языке, которое будет принимать POST-запросы с данными о нарушениях. Например, простое приложение на Python:

    ```python
    from flask import Flask, request
    import json

    app = Flask(__name__)

    @app.route('/csp-report-endpoint', methods=['POST'])
    def csp_report():
        report = request.get_json()
        with open('csp_reports.json', 'a') as f:
            json.dump(report, f)
            f.write('\n')
        return '', 204

    if __name__ == '__main__':
        app.run(debug=True)
    ```

Запускайте это приложение на сервере, чтобы собирать отчёты. Они будут записаны в файл `csp_reports.json`.

### Шаг 2: Мониторинг и анализ отчетов

1. **Сбор данных**: После активации режима отчётов, соберите данные в течение определённого времени (несколько дней или недель в зависимости от трафика).
   
2. **Анализ данных**: Изучите собранные отчёты. В каждом отчёте будет указано, какие ресурсы нарушили политику CSP. На основе этих данных вы сможете скорректировать политику так, чтобы она не блокировала легитимные ресурсы.

Пример отчёта:
```json
{
  "csp-report": {
    "document-uri": "https://example.com",
    "referrer": "",
    "violated-directive": "script-src",
    "blocked-uri": "https://untrusted-scripts.com/script.js",
    "source-file": "https://example.com/page",
    "line-number": 23,
    "column-number": 47
  }
}
```

### Шаг 3: Постепенное ужесточение политики CSP

После того как вы собрали и проанализировали отчёты, можно начать внедрять более строгую политику. 

1. **Обновление политики CSP**: На основе собранных данных вы можете добавить исключения для легитимных источников, таких как CDN, API и других сервисов.

Пример ужесточённой политики:
```nginx
add_header Content-Security-Policy "default-src 'self'; script-src 'self' https://trusted-cdn.com; style-src 'self' https://trusted-cdn.com; report-uri /csp-report-endpoint";
```

2. **Тестирование**: После внедрения строгой политики снова соберите отчёты в режиме `Report-Only`, чтобы убедиться, что новые правила не блокируют важные ресурсы.

3. **Переход в режим строгой блокировки**: После того как политика была тщательно протестирована, можно убрать `Report-Only` и активировать строгую блокировку:

```nginx
add_header Content-Security-Policy "default-src 'self'; script-src 'self' https://trusted-cdn.com; style-src 'self' https://trusted-cdn.com";
```

### Шаг 4: Постоянный мониторинг

Даже после внедрения строгой политики CSP, важно периодически анализировать отчёты и актуализировать политику, особенно если приложение меняется или подключаются новые внешние сервисы.

### Пример всей конфигурации Nginx с CSP:
```nginx
server {
    listen 80;
    server_name example.com;

    # Ваша существующая конфигурация...

    # Защита CSP
    add_header Content-Security-Policy "default-src 'self'; script-src 'self' https://trusted-cdn.com; style-src 'self' https://trusted-cdn.com";
    
    # Защита от XSS и кликджекинга
    add_header X-Content-Type-Options "nosniff";
    add_header X-Frame-Options "DENY";
    add_header X-XSS-Protection "1; mode=block";

    # Обработчик статических файлов
    location / {
        try_files $uri $uri/ =404;
    }

    # Обработчик для отчётов CSP
    location /csp-report-endpoint {
        proxy_pass http://localhost:5000;  # Прокси на Flask приложение для сбора отчётов
    }
}
```

### Результат

Следуя этому процессу, вы сможете самостоятельно организовать внедрение и обучение политики Content Security Policy (CSP) с помощью Nginx, что поможет защитить ваш сайт от XSS-атак и других угроз, связанных с небезопасным контентом.


Организация процесса внедрения **Content Security Policy (CSP)** с постепенной настройкой и обучением на базе Nginx может быть реализована в несколько этапов. Это позволит создать и отточить политику CSP, минимизируя риск того, что легитимные ресурсы будут заблокированы. Вот подробное руководство по реализации этого процесса:

### Этап 1: Включение политики CSP в режиме отчётности (Report-Only)

Первым шагом будет внедрение CSP в режиме `Report-Only`. В этом режиме политика не блокирует ресурсы, а только отправляет отчёты о нарушениях CSP в лог или на специальный сервер.

1. **Редактирование конфигурации Nginx**:  
   Откройте конфигурационный файл вашего сайта:

   ```bash
   sudo nano /etc/nginx/sites-available/your-site.conf
   ```

2. **Добавление CSP заголовка в режиме отчётности**:  
   Добавьте следующий заголовок в блок `server`:

   ```nginx
   server {
       # Ваши текущие настройки...

       # CSP в режиме отчётности
       add_header Content-Security-Policy-Report-Only "default-src 'self'; script-src 'self'; style-src 'self'; report-uri /csp-report-endpoint;";
   }
   ```

   Этот заголовок указывает, что браузеру разрешено загружать ресурсы только с текущего домена (`'self'`), а все нарушения будут отправляться на путь `/csp-report-endpoint` для анализа.

3. **Создание обработчика для приёма отчётов CSP**:  
   Чтобы обработать отчёты о нарушениях CSP, можно создать простой endpoint, который будет принимать POST-запросы с отчётами:

   ```nginx
   location /csp-report-endpoint {
       access_log /var/log/nginx/csp_reports.log;
       return 204;
   }
   ```

   В этом примере все отчёты будут записываться в файл `/var/log/nginx/csp_reports.log`.

4. **Применение изменений и перезапуск Nginx**:

   После редактирования конфигурации проверьте синтаксис и перезапустите Nginx:

   ```bash
   sudo nginx -t
   sudo systemctl reload nginx
   ```

### Этап 2: Сбор и анализ отчётов

1. **Просмотр отчётов о нарушениях CSP**:  
   Через некоторое время можно начать анализировать файл логов с отчётами о нарушениях:

   ```bash
   sudo tail -f /var/log/nginx/csp_reports.log
   ```

   В логе будут содержаться JSON-запросы с описанием того, какие ресурсы нарушили политику. Это поможет вам понять, какие ресурсы необходимо разрешить, и скорректировать политику CSP.

2. **Анализ легитимных ресурсов**:  
   Важно отследить, какие домены и ресурсы действительно необходимы для корректной работы вашего приложения. Например, если ваше приложение использует сторонние сервисы (например, Google Fonts, аналитические скрипты и т.д.), их нужно будет добавить в политику.

### Этап 3: Корректировка политики CSP

На основе собранных данных от корректных и заблокированных ресурсов можно корректировать политику:

1. **Добавление разрешённых доменов**:  
   Обновите политику CSP, чтобы разрешить необходимые внешние домены. Например:

   ```nginx
   add_header Content-Security-Policy-Report-Only "default-src 'self'; script-src 'self' https://trusted-scripts.example.com; style-src 'self' https://fonts.googleapis.com; report-uri /csp-report-endpoint;";
   ```

   В этом примере разрешены внешние скрипты только с `https://trusted-scripts.example.com` и стили с `https://fonts.googleapis.com`.

2. **Постепенное ужесточение политики**:  
   Можно постепенно ограничивать источники и типы контента. Например, можно запретить inline-скрипты, используя политику:

   ```nginx
   add_header Content-Security-Policy-Report-Only "default-src 'self'; script-src 'self' https://trusted-scripts.example.com; style-src 'self' https://fonts.googleapis.com; object-src 'none'; report-uri /csp-report-endpoint;";
   ```

   Политика `object-src 'none'` запрещает использование объектов (например, Flash или плагины), что может помочь снизить риск XSS.

### Этап 4: Внедрение строгого CSP

После того, как вы убедились, что собранные отчёты и скорректированные политики не блокируют легитимные ресурсы, можно включить строгую политику CSP, которая будет блокировать нарушения.

1. **Изменение режима на строгий**:  
   Вместо заголовка `Content-Security-Policy-Report-Only`, замените его на:

   ```nginx
   add_header Content-Security-Policy "default-src 'self'; script-src 'self' https://trusted-scripts.example.com; style-src 'self' https://fonts.googleapis.com; object-src 'none';";
   ```

2. **Перезагрузка Nginx**:  
   Примените изменения и перезагрузите Nginx:

   ```bash
   sudo nginx -t
   sudo systemctl reload nginx
   ```

### Этап 5: Мониторинг и обновление политики

После включения строгой политики важно продолжать мониторить отчёты о нарушениях (если вы оставили механизм отправки отчётов) и корректировать политику по мере необходимости, добавляя новые разрешённые источники или ужесточая ограничения.

### Важные моменты:

- **Минимизируйте разрешённые источники**: Разрешайте только те источники, которые действительно необходимы.
- **Избегайте inline-скриптов**: CSP позволяет заблокировать inline-скрипты, что значительно снижает риск XSS.
- **Тестируйте политику на разных страницах**: Убедитесь, что политика работает на всех страницах сайта.

Таким образом, с помощью Nginx вы можете поэтапно внедрить и обучить политику CSP, которая будет обеспечивать надёжную защиту вашего приложения от XSS и других угроз, связанных с контентом.