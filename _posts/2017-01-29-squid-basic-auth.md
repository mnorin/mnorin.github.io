---
title: 'Squid: Включение basic-аутентификации'
date: 2017-01-29T11:37:46+03:00
author: Maxim Norin
layout: post
guid: https://mnorin.com/?p=2563
permalink: /squid-basic-auth.html
categories:
  - Вопросы и ответы
tags:
  - auth
  - squid
---
__Вопрос:__ Как включить basic-аутентификацию для прокси-сервера Squid, чтобы при подключении прокси-сервера в браузере запрашивался логин и пароль пользователя?

__Ответ:__
<!--more-->

Сначала создадим файл с паролями для пользователей. Для этого нам понадобится утилита htpasswd из пакета apache2-utils. Создаем файл с паролями:
```
htpasswd -c /etc/squid3/squid-passwd username
```
При добавлении следующих пользователей указывать параметр -c не нужно.

Следующий шаг - добавление соответствующих строчек в конфигурационные файлы Squid. Вот что должно присутствовать в файле /etc/squid3/squid.conf:
```
auth_param basic program /usr/lib/squid3/basic_ncsa_auth /etc/squid3/squid-passwd
auth_param basic realm proxy
acl authenticated_users proxy_auth REQUIRED
http_access allow authenticated_users
```
После этого надо только перезапустить сервис командой
```
service squid3 restart
```
И подключить прокси в браузере. При попытке открыть любую страничку у вас должны запроситься логин и пароль, и после их правильного ввода должна загрузиться запрошенная страничка (если, конечно, у вас не включена фильтрация на уровне прокси).