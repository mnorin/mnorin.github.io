---
title: 'Oracle: ORA-00845: MEMORY_TARGET not supported on this system'
date: 2013-02-03T22:28:47+03:00
author: Maxim Norin
layout: post
permalink: /oracle-ora-00845-memory_target-not-supported-on-this-system.html
categories:
  - Статьи
tags:
  - 11gR2
  - MEMORY_TARGET
  - ORA-00845
  - oracle
  - SLES
---
Операционная система: SLES 11.1
При запуске Оракл 11gR2 такая ошибка:
```
SQL> startup
ORA-00845: MEMORY_TARGET not supported on this system
```
<!--more-->
Решилось следующим образом:
Добавил строчку в /etc/fstab следующего содержания:
```
shmfs    /dev/shm    tmpfs    size=1700m    0 0
```
Потом дал команду
```
mount /dev/shm
```
После этого еще раз запустил:
```
SQL> startup
ORACLE instance started.

Total System Global Area  778387456 bytes
Fixed Size                  1339344 bytes
Variable Size             461373488 bytes
Database Buffers          310378496 bytes
Redo Buffers                5296128 bytes
Database mounted.
Database opened.
```
Вот и всё.
