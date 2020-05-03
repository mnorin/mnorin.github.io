---
title: Как посмотреть открытые порты?
date: 2014-12-01T00:02:47+03:00
author: Maxim Norin
layout: post
permalink: /kak-v-linux-posmotret-otkry-ty-e-porty.html
categories:
  - Вопросы и ответы
tags:
  - linux
  - netstat
  - nmap
  - посмотреть открытые порты
---
__Вопрос:__ Как в linux посмотреть открытые порты?
<!--more-->

__Ответ:__

1) Командой
```
netstat -npl
```
2) Командой
```
nmap <IP-адрес>
```
Предварительно может потребоваться установить пакет nmap

3) Воспользоваться [этим](/skaner-tcp-portov-na-bash.html) скриптом на bash