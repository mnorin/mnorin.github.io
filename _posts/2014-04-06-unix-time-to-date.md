---
title: Как преобразовать unix time в дату и время и обратно?
date: 2014-04-06T13:16:37+03:00
author: Maxim Norin
layout: post
permalink: /unix-time-to-date.html
categories:
  - Вопросы и ответы
tags:
  - date
  - timestamp
  - unix time
  - время
  - дата
---
__Вопрос:__ Как штатными средствами преобразовать unix time (число секунд с начала эпохи, 1 января 1970 года) в дату и время и наоборот?
<!--more-->

__Ответ:__ Текущие дата и время в формате unix time:
```
$ date '+%s'
1396713472
```
Произвольные дата и время в формате unix time:
```
$ date --date='23 Jan 1981 09:23:15' '+%s'
349078995
```
Преобразование времени в формате unix time в обычную дату:
```
$ date -d@1396713472
Сбт Апр 5 19:57:52 MSK 2014
```