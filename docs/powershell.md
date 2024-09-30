---
layout: default
title: powershell
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

# Количество объектов в фильтре
```powershell
(Get-ADObject -Filter * -SearchBase "OU=Users,DC=local").Count
```
