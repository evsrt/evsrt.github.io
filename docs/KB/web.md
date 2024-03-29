---
layout: default
title: web
parent: KB
---

# Как проверить, что робот принадлежит Яндексу
Роботы Яндекса в логах сервера
Некоторые роботы Яндекса скачивают документы не для их последующей индексации, а для других специфичных целей. Для избежания непреднамеренной блокировки владельцами сайтов они могут не учитывать ограничивающие директивы файла robots.txt, предназначенные для произвольных роботов (User-agent: *).

Также частичное игнорирование ограничений robots.txt определенных сайтов возможно при наличии соответствующей договоренности между компанией «Яндекс» и владельцами этих сайтов.

Примечание. Если такой робот скачает документ, недоступный основному роботу Яндекса, этот документ никогда не будет проиндексирован и не попадет в поисковую выдачу.
Чтобы ограничить доступ таких роботов к сайту, используйте директивы специально для них, например:

User-agent: YandexCalendar
Disallow: /

User-agent: YandexMobileBot
Disallow: /private/*.txt$
Роботы используют автономную сеть: AS13238 и AS208722, а также IP-адреса, которые часто меняются, поэтому их список не разглашается.

При обращениях робота в логах вашего сервера может отображаться User-agent и версия браузера, которая используется при обходе сайта. Например, Mozilla/5.0 (compatible; YandexBot/3.0; +http://yandex.com/bots) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.268.

<https://yandex.com/support/webmaster/robot-workings/check-yandex-robots.html?lang=ru>
