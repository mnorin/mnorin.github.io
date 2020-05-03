---
title: Как проверить наличие обновлений apt?
date: 2014-05-03T11:49:57+03:00
author: Maxim Norin
layout: post
permalink: /kak-proverit-nalichie-obnovlenij-apt.html
categories:
  - Вопросы и ответы
tags:
  - apt
  - apt-get
  - update
  - upgrade
  - проверить наличие обновлений
---
__Вопрос:__ Как в Debian/Ubuntu проверить наличие обновлений без их установки?
<!--more-->

__Ответ:__

Это можно сделать следующим образом:
```
apt-get update
apt-get --just-print upgrade
```