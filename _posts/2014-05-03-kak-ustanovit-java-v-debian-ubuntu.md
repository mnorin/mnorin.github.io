---
title: Как установить Java в Debian/Ubuntu?
date: 2014-05-03T11:46:27+03:00
author: Maxim Norin
layout: post
permalink: /kak-ustanovit-java-v-debian-ubuntu.html
categories:
  - Вопросы и ответы
tags:
  - alien
  - deb
  - debian
  - java
  - rpm
  - ubuntu
---
__Вопрос:__ На официальном сайте Java только RPM-пакеты, а мне нужно установить Java в Debian и Ubuntu. Как это сделать?
<!--more-->
__Ответ:__

Сначала необходимо скачать RPM-пакет, а затем конвертировать его в .deb при помощи программы alien. Установка выполняется командой
```
apt-get install alien
```
А конвертация командой
```
alien --to-deb --scripts ./<название пакета>.rpm
```
