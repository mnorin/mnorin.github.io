---
title: Домашний медиа-сервер mediatomb
date: 2013-02-06T12:32:52+03:00
author: Maxim Norin
layout: post
permalink: /domashnij-media-server-mediatomb.html
categories:
  - Статьи
tags:
  - dlna
  - media server
  - mediatomb
  - transcoding
  - windows media player
  - wmc
  - wmp
  - xbmc
---
У многих дома есть хранилище всякой медиа-информации - видео, музыки, фотографий. Но просматривать это все не всегда удобно. В то же время существуют телевизоры, например, которые имеют функцию работы со спецификацией DLNA. И их можно как раз для этого и использовать.

Для создания домашнего медиа-сервера я использовал программу [MediaTomb](http://mediatomb.cc/). Это далеко не единственный медиа-сервер, однако у меня более-менее корректно из коробки заработал только он, остальные, как оказалось, необходимо предварительно настраивать, поэтому с ними все не намного, но сложнее. В совокупности с удобным веб-интерфейсом и возможность изменять функционал при помощи скриптов, я пока не нашел чего-то такого же гибкого и с таким же функционалом.
<!--more-->

Можно собирать из исходников, а можно поставить из репозиториев. В Дебиане, например, это делается командой
```
apt-get install mediatomb-daemon
```
После установки необходимо кое-что изменить в настройках.

В секции 
```
<server>
...
</server>
``` 
включаем веб-интерфейс:
```
<ui enabled="yes" show-tooltips="yes">
<account enabled="yes" session-timeout="30">
```
Заходим в браузере по адресу http://your-server-ip:41592 и наблюдаем веб-интерфейс, через который можно добавлять фильмы, музыку, фотографии для просмотра.

Первое, с чем возникла трудность - это повторение фильмов в списке, отображаемом в Windows Media Center и WMP 12. Каждый фильм отображался три раза. Это известная проблема, и что работает некорректно - MediaTomb или WMC - неоднозначный вопрос. Однако проблема решаема.

1) Добавляем в файл /etc/mediatomb/config.xml следующие строчки:
В секцию ```<server ...> </server>```:
```
<pc-directory upnp-hide="yes">
```
2) Меняем строчку
```
<virtual-layout type="builtin">
```
на строчку
```
<virtual-layout type="js">
```
3) В файле /usr/share/mediatomb/js/import.js приводим функцию ```addVideo(obj)``` к следующему виду:
```javascript
function addVideo(obj)
{
    var chain = new Array('Video', 'All Video');
    addCdsObject(obj, createContainerChain(chain));

/*
    var last_path = getLastPath(obj.location);
    if (last_path)
    {
        chain = new Array('Video', 'Directories', last_path);
        addCdsObject(obj, createContainerChain(chain));
    }
*/
}
```
4) Останавливаем сервис:
```
/etc/init.d/mediatomb stop
```
5) Удаляем файл /var/lib/mediatomb/mediatomb.db
6) Запускаем сервис:
```
/etc/init.d/mediatomb start
```
Затем еще раз добавляем все папки через веб-интерфейс
После этих манипуляций количество каждого фильма в списке равно не больше реального.
Это не всё, что нужно поправить. Еще надо включит генерацию картинок для видеофайлов и транскодинг формата flv и иже с ним.
Изменяем файл /etc/mediatomb/config.xml
Меняем строчку
```
<ffmpegthumbnailer enabled="no">
```
на
```
<ffmpegthumbnailer enabled="yes">
```
Для транскодинга меняем
```
<transcoding enable="no">
```
на
```
<transcoding enable="yes">
```
Программы, которые можно использовать совместно с сервером DLNA:
1. Windows Media Center (Windows 7)
2. Windows Media Player 12 (Windows 7)
3. [XBMC](http://xbmc.org/) (Windows XP/7, MacOS X, Linux)
4. UPnPlay (Android)
