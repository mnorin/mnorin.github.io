---
title: 'LXDE: недоступен пункт Suspend'
date: 2014-10-05T15:18:43+03:00
author: Maxim Norin
layout: post
permalink: /lxde-nedostupen-punkt-suspend.html
categories:
  - Статьи
tags:
  - lxde
  - not authorized
  - suspend
---
Возникла небольшая, но неприятная проблема. В графической оболочке LXDE невозможно перейти в спящий режим (он же Suspend). Пункт в меню присутствует, но недоступен для использования. Ошибка содержит текст "not authorized".

Проблема решается следующим образом:

<!--more-->
1) Создаем группу powerdev, если ее еще нет
```
groupadd powerdev
```
2) Добавляем пользователя в эту группу
```
usermod -G powerdev user
```
3) Создаем файл /etc/polkit-1/localauthority/50-local.d/custom-power.pkla следующего содержания:
```
[Power Management Permissions]
Identity=unix-group:powerdev
Action=org.freedesktop.upower.*;org.freedesktop.consolekit.system.*;org.freedesktop.udisks.*
ResultAny=yes
ResultInactive=yes
ResultActive=yes
```
После этого может потребоваться перезагрузиться, либо перезапустить polkitd и перелогиниться в систему. В дальнейшем достаточно добавить пользователя в группу powerdev, чтобы разрешить ему переводить компьютер в спящий режим.