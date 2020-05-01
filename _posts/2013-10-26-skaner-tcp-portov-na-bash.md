---
id: 515
title: Сканер tcp-портов на bash
date: 2013-10-26T16:06:58+03:00
author: Maxim Norin
layout: post
permalink: /skaner-tcp-portov-na-bash.html
categories:
  - Статьи
tags:
  - bash
  - nmap
  - tcp
  - сканер портов
---
Когда вы работаете с серверами, периодически возникает необходимость посмотреть, какие порты открыты. Одно из лучших средств для этого - nmap, но он не всегда есть под рукой. Зато обычно под рукой есть командная оболочка bash. И ей как раз можно воспользоваться, чтобы написать собственный сканер портов. Не такой быстрый, конечно, но вполне пригодный для использования.
<!--more-->

В основе этого скрипта возможность bash отправлять на указанный хост и порт данные. Собственно, сам скрипт

portscan.sh:
```bash
#!/bin/bash

if [ "$1" == "" ]
then
    echo
    echo This script scans TCP opened ports on IP or hostname
    echo Usage : portscan.sh \<ip-or-hostname\> \[start-port\] \[end-port\]
    echo start-port equals to 1 by default
    echo end-port equals 1024 by default
    echo
    exit
fi

START_PORT=$2;[ -z "$START_PORT" ] && START_PORT=1
END_PORT=$3;[ -z "$END_PORT" ] && END_PORT=1024
echo Scanning $1 \(ports $START_PORT to $END_PORT\)

PORT_PROTOCOL="tcp"

scan_port(){
    PORT_NUMBER=$1
    PORT_SCAN_RESULT=`2>&1 echo "" > /dev/$PORT_PROTOCOL/$TARGET_NAME_OR_IP/$PORT_NUMBER | grep connect`
    [ "$PORT_SCAN_RESULT" == "" ] && echo $PORT_NUMBER\/$PORT_PROTOCOL' 'open'  '`grep $PORT_NUMBER/$PROTOCOL /etc/services | head -n1 | awk '{print $1}'`
}

TARGET_NAME_OR_IP=$1
echo 'PORT              STATE   SERVICE'

for PORT_NUMBER in `seq $START_PORT $END_PORT`
do
    scan_port $PORT_NUMBER
done
```

В качестве параметров скрипт может принимать от одного до трех параметров. Первый - имя или IP-адрес хоста, второй - начальный порт интервала сканирования, третий - конечный порт интервала сканирования. Если начальный и/или конечный порты не указаны, то выбираются 1 и 1024.

И результат работы скрипта:
```
$ ./portscan.sh 192.168.0.9
Scanning 192.168.0.9 (ports 1 to 1024)
PORT    STATE   SERVICE
21/tcp  open    ftp
25/tcp  open    smtp
53/tcp  open    domain
80/tcp  open    http
22/tcp  open    ssh
110/tcp open    pop3
111/tcp open    sunrpc
139/tcp open    netbios-ssn
445/tcp open    microsoft-ds
```

И да, сканер портов на bash, как и многое другое, написать довольно просто.
