---
layout: default
title: wireshark
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

# Filters
Search http request wiht string "UNION"
```bash
http.request && http contains "UNION"
```
Search SQLi attempt
```bash
http.request.uri contains "1=1" or http.request.uri matches "(?i)and%20" or http.request.uri matches "(?i)--"
```
Database-specific commands 'UNION SELECT' or 'INFORMATION_SCHEMA'
```bash
http.request.uri contains "INFORMATION_SCHEMA"
```
