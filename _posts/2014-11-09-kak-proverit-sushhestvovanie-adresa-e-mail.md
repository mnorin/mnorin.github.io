---
title: Как проверить существование адреса e-mail?
date: 2014-11-09T15:21:04+03:00
author: Maxim Norin
layout: post
permalink: /kak-proverit-sushhestvovanie-adresa-e-mail.html
categories:
  - Вопросы и ответы
tags:
  - bash
  - linux
  - mail
  - smtp
  - проверка
  - существование пользователя
---
__Вопрос:__ как проверить существование адреса e-mail?
<!--more-->

__Ответ:__

Это можно сделать при помощи команды SMTP VRFY и программы telnet

Подключаемся telnet'ом на сервер, на котором хотим проверить наличие адреса/пользователя.
```
$ telnet host.com 25
Trying 44.55.66.77...
Connected to host.com.
Escape character is '^]'.
220 host.com ESMTP Postfix
HELO 127.0.0.1
250 host.com
VRFY existeduser
252 2.0.0 existeduser
VRFY unknownuser
550 5.1.1 <unknownuser>: Recipient address rejected: User unknown in local recipient table
QUIT
221 2.0.0 Bye
Connection closed by foreign host.
```
Как видите, если пользователь существует, мы получим код 252, если не существует - код 550. Соответственно, можно написать скрипт на bash, который сделает проверку за нас.
```bash
#!/bin/bash

HOST=$1
PORT=25
USER=$2

if [ "$1" == "" ]
then
    echo "Usage: $0 host user"
    exit
fi

exec 3<>/dev/tcp/$HOST/$PORT
read -u 3 STATUS
echo "HELO 127.0.0.1" >&3
read -u 3 STATUS
echo "VRFY $USER" >&3
read -u 3 STATUS
RESULT=$(echo $STATUS | cut -d" " -f 1)
if [ "$RESULT" == "252" ]
then
    echo "User $USER at host $HOST exists"
else
    echo "User $USER at host $HOST does not exist"
fi
```
Но, естественно, не все почтовые сервера позволят такие действия.