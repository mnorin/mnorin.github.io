---
title: Менеджер пространства подкачки swapspace
date: 2015-10-17T22:45:08+03:00
author: Maxim Norin
layout: post
permalink: /menedzher-prostranstva-podkachki-swapspace.html
categories:
  - Статьи
tags:
  - debian
  - linux
  - swapspace
---
Вообще более привычный вариант использования пространства подкачки - это либо swap-раздел, либо swap-файл. Но есть еще один вариант, который также имеет право на жизнь. При этом он в определенных ситуациях более удобен в использовании. Это swapspace, менеджер динамического управления пространством подкачки. Его задача заключается в использовании дискового пространства в качестве пространства подкачки, когда это необходимо, и освобождении места, когда оно больше не нужно.
<!--more-->

## Установка
В данный момент во многих дистрибутивах уже присутствует соответствующий пакет, и его, соответственно, можно установить при помощи менеджера пакета.
```
apt-get install swapspace
```
После установки он сразу же запустится и в системе появится такой процесс:
```
 1011 ?        Ss     0:00 /usr/sbin/swapspace -d -p
```
Если же есть необходимость собрать программное обеспечение из исходных кодов, то их можно найти на официальном сайте: http://pqxx.org/development/swapspace.

## Использование
Пользоваться менеджером пространства подкачки swapspace очень просто. После запуска он работает самостоятельно, автоматически выделяя при необходимости дисковое пространство для сохранения страниц памяти из оперативной памяти. Если быть более точным, то он создает своп-файлы на диске при необходимости и динамически их подключает. Вот как выглядит вывод программы swapon при использовании менеджера пространства подкачки:
```
# swapon -s
Filename                   Type            Size    Used    Priority
/var/lib/swapspace/1       file            71192   63716   -1
/var/lib/swapspace/2       file            46856   28296   -2
/var/lib/swapspace/3       file            86244   0       -3
```

То есть, как видите, программа делает то же самое, что можно сделать руками, но делает она это автоматически. Такой способ несколько медленнее, чем своп-раздел, но он хорош тем, что не нужно заранее создавать раздел или своп-файл руками.

## Настройка
Самое первое, что делаем - это редактируем файл /etc/swapspace.conf. В нем необходимо раскомментировать несколько строчек и файл после этого должен выглядеть примерно так:
```
# Swap path: location where swapspace may create and delete swapfiles.  For
# security reasons this directory must be accessible to root and to root only.
swappath="/var/lib/swapspace"

# Lower free-space threshold: if the percentage of free space drops below this
# number, additional swapspace is allocated
lower_freelimit=20

# Upper free-space threshold: if the percentage of free space exceeds this
# number, swapspace will attempt to free up swapspace
upper_freelimit=60

# Percentage of free space swapspace should aim for when adding swapspace.  This
# should fall somewhere between lower_freelimit and upper_freelimit.
freetarget=30

# Smallest allowed size for individual swapfiles
min_swapsize=4m

# Greatest allowed size for individual swapfiles
max_swapsize=2t

# Duration (roughly in seconds) of the moratorium on swap allocation that is
# instated if disk space runs out, or the cooldown time after a new swapfile is
# successfully allocated before swapspace will consider deallocating swap space
# again.  The default cooldown period is about 10 minutes.
cooldown=600
```

После этого надо рестартовать сервис
```
service swapspace restart
```
И теперь можно приступать к запуску программ, для которых нужны большие объемы оперативной памяти.

## Особенности работы
Для работы необходимо наличие свободного места на диске, чтобы можно было создавать файлы подкачки. Это, естественно, возможно только в тех системах, где создание файла подкачки и включение свопа разрешено, поэтому скорее всего этот вариант не будет работать на недорогом хостинге на базе OpenVZ, на таких хостингах обычно отключают возможность использования свопа. Поэтому пробуйте, возможно этот вариант вам подойдет.