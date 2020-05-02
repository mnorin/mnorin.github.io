---
title: Как в bash сложить два числа?
date: 2014-04-20T13:09:01+03:00
author: Maxim Norin
layout: post
permalink: /kak-v-bash-slozhit-dva-chisla.html
categories:
  - Вопросы и ответы
tags:
  - bash
  - сложить два числа
---
__Вопрос:__ Как в bash сложить два числа?
<!--more-->
__Ответ:__
```bash
INT1=2
INT2=3
INT=$(($INT1+$INT2))
echo $INT
```
Или, например, так, с использованием bc:
```bash
INT1=3
INT2=2
INT=$(echo "$INT1+$INT2" | bc)
echo $INT
```
