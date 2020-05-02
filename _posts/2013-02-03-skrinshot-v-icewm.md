---
title: Скриншот в IceWM
date: 2013-02-03T22:10:01+03:00
author: Maxim Norin
layout: post
permalink: /skrinshot-v-icewm.html
categories:
  - Статьи
tags:
  - icewm
  - xbindkeys
  - скриншот
---
В качестве графической оболочки я использую IceWM. Почему? Потому, что скорость работы меня очень устраивает, нет всяких ненужных красивостей и прочего-прочего-прочего...
Функции создания скриншотов тоже, к сожалению, нет. Поэтому придется настраивать самим.
В качестве примера настройки я воспользовался информацией со страницы http://wiki.lxde.org/en/How_to_make_screenshots.
На ней рассмотрен пример создания скриншота для LXDE, и я его просто адаптировал под IceWM
<!--more-->
Итак:
1) Устанавливаем необходимые пакеты:
```
apt-get install imagemagick xbindkeys
```
2) Создаем скрипт создания скриншотов
```
nano /usr/local/bin/screenshot
```
Записываем следующее:
```bash
#!/bin/bash
DATE=`date +%Y-%m-%d\ %H-%M`
import -window root "$HOME/Desktop/screenshot $DATE.png"
```
3) Создаем (редактируем) конфигурационный файл для xbindkeys
```
nano ~/.xbindkeys
```
вписываем следующие строчки:
```
"screenshot"
    Mod2 + Print
```
4) Запускаем xbindkeys

Готово.
Теперь при нажатии на клавишу PrintScreen в папку ~/Desktop запишется PNG-файл с названием, включающим текущее время и дату.
