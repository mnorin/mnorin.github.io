---
title: 'Postfix: добавляем сервер в белый список'
date: 2017-09-02T23:58:24+03:00
author: Maxim Norin
layout: post
permalink: /postfix-add-server-to-whitelist.html
categories:
  - Вопросы и ответы
tags:
  - postfix
  - whitelist
---
__Вопрос:__ как добавить почтовый сервер в белый список для получения почты от плохо настроенного почтового сервера или рассылки через провайдера массовых рассылок?
<!--more-->

__Ответ:__

1) Создаем файл со списком, например, /etc/postfix/rbl_override

2) Добавляем название сервера в список в таком виде:
```
biscuit.nlhsolutions.com OK
```
3) Даем команду
```
postmap /etc/postfix/rbl_override
```
Таким образом будет создан файл с названием __rbl_override.db__.

4) В файл /etc/postfix/main.cf добавляем
```
smtpd_recipient_restrictions = 
  ...
  check_client_access hash:/etc/postfix/rbl_override,
  ...
```
5) Перезапускаем сервис
```
service postfix restart
```
После этого письма должны начать приходить.