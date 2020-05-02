---
title: "Маленькая особенность настройки сети в Solaris'е"
date: 2013-02-03T22:41:38+03:00
author: Maxim Norin
layout: post
permalink: /malen-kaya-osobennost-nastrojki-set.html
categories:
  - Статьи
tags:
  - ifconfig
  - solaris
  - сеть
  - солярис
---
В принципе, подключение сети в Солярисе не вызовет каких-то проблем, если у вас есть опыт подключения сети в Линуксе. Но есть один момент, который при незнании серьёзно стопорит работу.
Перед настройкой интерфейса нужно его подключить "физически". 
<!--more-->
Для этого используется команда
__ifconfig__ с параметром __plumb__
```
ifconfig  plumb
```
Для подключения всех интерфейсов:
```
ifconfig -a plumb
```
Далее конфигурируем интерфейс командой
```
ifconfig <address> netmask <mask>
```
И поднимаем его:
```
ifconfig  up
```
Отключение происходит в обратном порядке:
```
ifconfig down
ifconfig unplumb
```
