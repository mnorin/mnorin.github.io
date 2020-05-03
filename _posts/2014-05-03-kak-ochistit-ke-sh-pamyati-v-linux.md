---
title: Как очистить кэш памяти в linux?
date: 2014-05-03T11:54:07+03:00
author: Maxim Norin
layout: post
permalink: /kak-ochistit-ke-sh-pamyati-v-linux.html
categories:
  - Вопросы и ответы
tags:
  - cached
  - free
  - memory
  - top
  - кэш
  - освободить
  - память
  - сбросить
---
__Вопрос:__ Как освободить память, которую команда top показывает как cached, или, другими словами, как сбросить кэш памяти?
<!--more-->
__Ответ:__

```
sync; echo 3 > /proc/sys/vm/drop_caches
```