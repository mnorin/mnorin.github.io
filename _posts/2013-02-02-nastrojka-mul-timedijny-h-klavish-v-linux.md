---
title: Настройка мультимедийных клавиш в Linux
date: 2013-02-02T12:29:27+03:00
author: Maxim Norin
layout: post
permalink: /nastrojka-mul-timedijny-h-klavish-v-linux.html
categories:
  - Статьи
tags:
  - icewm
  - linux
  - xbindkeys
  - горячие клавиши
  - переключение
  - раскладка
---
В linux'е возможно использование мультимедийных клавиатур так, как вы этого пожелаете.
Я в основном работаю в KDE и IceWM, и если в первом случае система сама частично или полностью определяет, что это за клавиши, то во втором настройку приходится всегда делать самостоятельно. Однако в чем-то это даже удобно.
Что же нам для этого понадобится?
<!--more-->

1) Установить xbindkeys
2) Настроить горячие клавиши
3) Включить автоматическую загрузку xbindkeys для того, чтобы этот процесс начинал работу сразу после входа в систему

Итак, устанавливаем.
Для Debian это будет следующая строчка:
```
apt-get install xbindkeys
```
После установки пакетов нужно определить мульти-клавишные коды.
Для этого запускаем процесс в режиме чтения мульти-байтовых сочетаний клавиш.
```
xbindkeys -mk
```
При нажатии на мультимедийные клавиши в консоли будут появляться коды клавиш и их названия.
Для моей клавиатуры (Genius LuxeMate i202) это выглядит вот так:
```
$ xbindkeys -mk
Press combination of keys or/and click under the window.
You can use one of the two lines after "NoCommand"
in $HOME/.xbindkeysrc to bind a key.

--- Press "q" to stop. ---
"(Scheme function)"
    m:0x0 + c:180
    XF86HomePage
"(Scheme function)"
    m:0x0 + c:164
    XF86Favorites
"(Scheme function)"
    m:0x0 + c:225
    XF86Search
"(Scheme function)"
    m:0x0 + c:181
    XF86Reload
"(Scheme function)"
    m:0x0 + c:136
    Cancel
"(Scheme function)"
    m:0x0 + c:167
    XF86Forward
"(Scheme function)"
    m:0x0 + c:166
    XF86Back
"(Scheme function)"
    m:0x0 + c:163
    XF86Mail
"(Scheme function)"
    m:0x0 + c:179
    XF86Tools
"(Scheme function)"
    m:0x0 + c:179
    XF86Tools
"(Scheme function)"
    m:0x0 + c:152
    XF86Explorer
"(Scheme function)"
    m:0x0 + c:148
    XF86Calculator
"(Scheme function)"
    m:0x0 + c:121
    XF86AudioMute
"(Scheme function)"
    m:0x0 + c:24
    q
```
Теперь создаем файл ~/.xbindkeysrc (например так):
```
mcedit ~/.xbindkeysrc
```
Вписываем в файл значения, полученные ранее.
Значения вписываются таким образом: первая строчка - это команда в кавычках, вторая - либо название клавиши, либо ее код.
```
"kcalc"
    m:0x0 + c:148
```
либо так, либо так:
```
"kcalc"
    XF86Calculator
```
Осталось только добавить запуск процесса xbindkeys для автоматической загрузки
```bash
echo "xbindkeys" >> ~/.profile
```
Либо включить автозагрузку каким-то иным способом.
