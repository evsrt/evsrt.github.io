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
