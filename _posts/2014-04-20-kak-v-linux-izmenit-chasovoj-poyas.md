---
title: Как в linux изменить часовой пояс?
date: 2014-04-20T12:56:45+03:00
author: Maxim Norin
layout: post
permalink: /kak-v-linux-izmenit-chasovoj-poyas.html
categories:
  - Вопросы и ответы
tags:
  - tzconfig
  - tzdata
---
__Вопрос:__ Как в linux изменить текущий часовой пояс?
<!--more-->

__Ответ:__ В зависимости от дистрибутива может потребоваться выполнить одну из следующих команд:

Для Debian/Ubuntu:
```
dpkg-reconfigure tzdata
```
Для RedHat/CentOS/Fedora:
```
tzconfig
```
