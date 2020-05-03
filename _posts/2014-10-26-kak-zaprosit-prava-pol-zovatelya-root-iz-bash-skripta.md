---
title: Как запросить права пользователя root из bash-скрипта?
date: 2014-10-26T20:07:22+03:00
author: Maxim Norin
layout: post
permalink: /kak-zaprosit-prava-pol-zovatelya-root-iz-bash-skripta.html
categories:
  - Вопросы и ответы
tags:
  - bash
  - gksu
  - gksudo
  - sudo
---
__Вопрос:__ я пишу bash-скрипт и мне в определенный момент необходимо получить права пользователя root в графическом режиме. Как это сделать?
<!--more-->

__Ответ:__

Для получения прав root можно использовать программы gksu и gksudo. В Debian'е gksudo является символической ссылкой на gksu. Пример:
```
gksu ls
```
Будет запрошен пароль пользователя, после чего будет выполнена команда "ls", точно так же, как при использовании программы sudo.