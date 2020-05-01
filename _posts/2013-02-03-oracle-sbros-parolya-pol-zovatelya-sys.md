---
title: 'Oracle: сброс пароля пользователя SYS в Oracle'
date: 2013-02-03T22:32:02+03:00
author: Maxim Norin
layout: post
permalink: /oracle-sbros-parolya-pol-zovatelya-sys.html
categories:
  - Статьи
tags:
  - oracle
  - SYS
  - пароль
  - сброс
---
Был потерян пароль пользователя SYS. Соответственно, попасть под ним на сервер не представляется возможным. При использовании passwd SYS нужно знать старый пароль, который, в общем-то и был потерян.
Решилось так:
```
> sqlplus / as sysdba
SQL> alter user SYS identified by "NEWPASSWORD";
```
