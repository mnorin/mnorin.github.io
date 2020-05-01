---
id: 1114
title: Аналог wget на bash
date: 2014-05-11T23:24:20+03:00
author: Maxim Norin
layout: post
permalink: /analog-wget-na-bash.html
categories:
  - Статьи
tags:
  - bash
  - curl
  - wget
---
Если вам необходимо скачать какой-то файл, а curl'а или wget'а нет, то можно воспользоваться командной оболочкой bash, написав скрипт, позволяющий скачать любой файл по протоколу http. Вот этот скрипт:
<!--more-->

download.sh:
```bash
#!/bin/bash

if [ "$1" == "" ]
then
    echo "usage: $0 URL"
    exit 0
fi

HOST=`echo $1 | sed 's/http\:\/\///' | sed -r 's/([^/])\/(.*)/\1/'`
FILENAME=`echo "$1" | sed -r 's/(.*)\/(.*)/\2/'`
PATH=`echo "$1" | sed 's/http\:\/\///' | sed -r "s/$HOST//" | sed "s/$FILENAME//"`
PORT=80
HEADERS="HTTP/1.1\r\nHost: $HOST\r\nConnection: close\r\nContent-Length: 0\r\n\r\n"
F="/tmp/dlfile"
exec 3<>/dev/tcp/$HOST/$PORT
echo -e "GET $PATH$FILENAME $HEADERS" >&3
/bin/cat <&3 > $F
/usr/bin/tail $F -n +$((`/bin/sed $F -e '/^\r$/q' | /usr/bin/wc -l`+1)) > $FILENAME
/bin/rm $F
```
