---
title: 'Ошибка "spamc: exec failed: No such file or directory"'
date: 2013-08-25T21:43:59+03:00
author: Maxim Norin
layout: post
permalink: /spamc-exec-failed.html
categories:
  - Вопросы и ответы
tags:
  - exec
  - failed
  - linux
  - postfix
  - spamc
---
__Вопрос:__ Почему у меня в файле /var/log/mail.err следующая ошибка: "spamc: exec failed: No such file or directory"?
<!--more-->

__Ответ:__

Эта ошибка может означать как минимум два варианта:

1) В системе отсутствует исполнимый файл sendmail

2) Если вы используете postfix, то, возможно, в файле /etc/postfix/master.conf неверно прописан путь к исполнимому файлу sendmail.