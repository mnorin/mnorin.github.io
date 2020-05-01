---
title: 'Oracle: ORA-01092: ORACLE instance terminated. Disconnection forced'
date: 2013-02-03T22:37:14+03:00
author: Maxim Norin
layout: post
permalink: /oracle-ora-01092-oracle-instance-terminated-disconnection-forced.html
categories:
  - Статьи
tags:
  - 10.2.0.4
  - 10.2.0.1
  - disconnection forced
  - instance terminated
  - oracle
---
После обновления Oracle Database с версии 10.2.0.1 до версии 10.2.0.4 возникает ошибка "ORA-01092: Oracle instance terminated. Disconnection forced."
```
$ sqlplus /nolog
SQL> conn / as sysdba
Connected to an idle instance.
SQL> startup
ORACLE instance started.

Total System Global Area 188743680 bytes
Fixed Size 778036 bytes
Variable Size 162537676 bytes
Database Buffers 25165824 bytes
Redo Buffers 262144 bytes
Database mounted.
ORA-01092: ORACLE instance terminated. Disconnection forced
```
<!--more-->
Исправить можно так:
```
cd $ORACLE_HOME/rdbms/admin
sqlplus / as sysdba
SQL> startup upgrade;
SQL> @catupgrd.sql
SQL> shutdown immediate;
SQL> startup;
SQL> @utlrp.sql
```
