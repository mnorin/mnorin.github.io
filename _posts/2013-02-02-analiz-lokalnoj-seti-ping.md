---
id: 118
title: Анализ состава локальной сети при помощи команды ping
date: 2013-02-02T12:42:20+03:00
author: Maxim Norin
layout: post
permalink: /analiz-lokalnoj-seti-ping.html
categories:
  - Статьи
tags:
  - bash
  - ping
  - анализ сети
  - локальная сеть
---
Возникла как-то необходимость выяснить, а сколько же в сети компьютеров под управлением Windows, Linux, Solaris и получить результат в каком-то повсеместно читаемом формате.
Сетевые настройки в каждой операционной системе по умолчанию, параметры сети получены по DHCP.
Ходить и считать поштучно — не вариант. Пока весь офис обойдешь, времени угробишь кучу, а его-то как раз и не хватает.

Зато есть такая команда, как ping, и такая командная оболочка, как bash.
<!--more-->

В результате был написан следующий скрипт:
```bash
#!/bin/bash

########################################################################
#     Operating System by ttl network detection script
########################################################################

logfile="./scan-ttl.htm"
pingcount=2
os_id=""

echo "<html><head><title>scan-ttl log file</title></head><body><table>" > $logfile

for network in 0 1 2
do
    for host in {1..254}
    do
        host_ip=192.168.$network.$host
        echo $host_ip
        os_id=""
        if [ "$(ping -c $pingcount $host_ip | grep "ttl=6")" != "" ]
            then
              # Linux machine detected
              os_id="Linux"
            else
              if [ "$(ping -c $pingcount $host_ip | grep "ttl=12")" != "" ]
                then
                  # Windows machine detected
                  os_id="Windows"
                else
                  if [ "$(ping -c $pingcount $host_ip | grep "ttl=25")" != "" ]
                    then
                      # Solaris/FreeBSD/Unix
                      os_id="*nix/FreeBSD"
                  fi
              fi
        fi
        if [ "$os_id" != "" ]
            then
                echo "<tr><td>"$host_ip"</td><td>"$os_id >> $logfile
                echo "</td><td>" >> $logfile
                echo "`host $host_ip`" >> $logfile
                echo "</td></tr>" >> $logfile
        fi
    done
done

echo "</table></body></html>" >> $logfile
```
На выходе имеем таблицу в виде html-файла, который можно просматривать в любом браузере, даже в текстовом режиме, и копипастить, например, в Excel.

Как вариант оптимизации по времени можно также изначально результат пинга вынести в отдельную переменную. Например, так:
```bash
PING_RESULT=$(ping -c $pingcount $host_ip)
```
И проверять таким образом:
```
if [ "`echo $PING_RESULT | grep "ttl=6"`" != "" ]
```

Таким образом можно еще немного сократить время выполнения итогового скрипта.
Но лично мне по времени было не критично, поскольку скрипт запускался в фоне.
