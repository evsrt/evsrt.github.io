---
layout: default
title: docker
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

# Containers
## Stop all the containers
```bash
docker stop $(docker ps -a -q)
```
## Remove all the containers
```bash
docker rm $(docker ps -a -q)
```
## System prune
WARNING! This will remove:
  - all stopped containers
  - all networks not used by at least one container
  - all dangling images
  - unused build cache
```bash
docker system prune
```
