---
layout: default
title: RHEL
parent: KB
---
# How to customize crypto policies in RHEL 8.2
<https://www.redhat.com/en/blog/how-customize-crypto-policies-rhel-82>

Просмотр текущей политики
```ruby
update-crypto-policies --show
```
Просмотр политики DEFAULT
```ruby
cat /usr/share/crypto-policies/policies/DEFAULT.pol
```
Создать свою политику
```ruby
/etc/crypto-policies/policies/MYPOLICY.pol
```
Применить политику 
```ruby
sudo update-crypto-policies --set MYPOLICY
```
Перезагрузить сервер
```ruby
sudo reboot
```
