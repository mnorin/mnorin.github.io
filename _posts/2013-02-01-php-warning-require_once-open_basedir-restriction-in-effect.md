---
id: 80
title: 'PHP Warning:  require_once(): open_basedir restriction in effect.'
date: 2013-02-01T17:01:36+03:00
author: Maxim Norin
layout: post
guid: https://mnorin.com/?p=80
permalink: /php-warning-require_once-open_basedir-restriction-in-effect.html
categories:
  - Статьи
tags:
  - apache2
  - open_basedir
  - php
  - restriction
  - warning
---
При установке Wordpress первоначальная настройка не проходила. В логах было вот такое сообщение:
```
PHP Warning:  require_once(): open_basedir restriction in effect. File(/var/www/file1.php) is not within the allowed path(s): (.) in /var/www/file2.php on line 36
```
<!--more-->

Для исправления нужно отредактировать настройки безопасности apache. В файле /etc/apache2/conf.d/security.conf поменять
```
php_admin_value open_basedir "."
```
на те пути, которые у вас будут использоваться (например, "/var/www/site:/var/www/site/include") или поставить <em>none</em> (не рекомендуется).
Вот таким образом:
```
php_admin_value open_basedir none
```
После этого перезапустить сервис apache2.
