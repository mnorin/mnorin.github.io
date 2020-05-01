---
id: 287
title: Замена traceroute командой ping
date: 2013-06-09T16:32:10+03:00
author: Maxim Norin
layout: post
permalink: /zamena-traceroute-komandoj-ping.html
categories:
  - Статьи
tags:
  - bash
  - linux
  - ping
  - script
  - traceroute
---
Скрипт, позволяющий посмотреть трассу прохождения пакетов. Программа traceroute бывает установлена не на всех компьютерах, и для установки ее может потребоваться наличие подключения к Интернету. А это может быть проблематично, если у вас какие-то проблемы с сетью. Особенность работы этого скрипта в том, что при указании IP-адреса он работает очень быстро, в связи с тем, что нет необходимости преобразовывать доменное имя в IP-адрес. Воспользуемся командой ping.
<!--more-->

pingtrace.sh:
```bash
#!/bin/bash
REMOTE_HOST=$1
REMOTE_HOST_2=`host $1 | grep 'has address' | awk '{printf $4}'`
TARGET_HOST="_"
TARGET_TTL=1

echo Tracing host $REMOTE_HOST\($REMOTE_HOST_2\)

while [ "$REMOTE_HOST" != "$TARGET_HOST" -a "$REMOTE_HOST_2" != "$TARGET_HOST" ]
do
    TARGET_HOST=`ping -c 1 -t $TARGET_TTL $REMOTE_HOST | grep 'exceeded' | awk '{printf $2}'`
    if [ "$TARGET_HOST" != "" ]
    then
        echo $TARGET_HOST, TTL=$TARGET_TTL
        TARGET_TTL=$(($TARGET_TTL+1))
    else
        echo $REMOTE_HOST, TTL=$TARGET_TTL
        exit
    fi
done
```

Необходимо, однако, помнить, что ping и traceroute используют разный тип пакетов во время работы, и если на сетевом уровне существуют соответствующие ограничения, то скрипт работать не будет.
