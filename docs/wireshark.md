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
ip.src == 111.224.250.131 && http.request && http contains "UNION"
```
Search SQLi attempt
```bash
http.request.uri contains "1=1" or http.request.uri matches "(?i)and%20" or http.request.uri matches "(?i)--"
```
