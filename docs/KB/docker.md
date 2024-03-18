---
layout: default
title: docker
parent: KB
---
## Для проверки статуса контейнеров
```
docker ps -a --format "{{.Names}}: {{.Status}}"
```
# Docker Security Cheat Sheet
<https://cheatsheetseries.owasp.org/cheatsheets/Docker_Security_Cheat_Sheet.html>

# Защита контейнеров с помощью фильтров Seccomp
<https://habr.com/ru/companies/ruvds/articles/689184/>

# Контейнеры и безопасность: seccomp
<https://habr.com/ru/companies/selectel/articles/322046/>
