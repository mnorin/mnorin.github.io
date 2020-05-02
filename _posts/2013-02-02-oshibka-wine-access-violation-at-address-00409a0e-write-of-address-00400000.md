---
title: 'Ошибка wine: access violation at address 00409A0E. Write of address 00400000'
date: 2013-02-02T01:02:42+03:00
author: Maxim Norin
layout: post
permalink: /oshibka-wine-access-violation-at-address-00409a0e-write-of-address-00400000.html
categories:
  - Статьи
tags:
  - wine
  - ошибка
---
В гугле мало что находится на эту тему.

Тут есть как минимум два варианта.

1. Файловая система защищена от записи.
2. Запускаемый файл находится на файловой системе в сети (Samba, NFS). Помогает в таком случае копирование в домашнюю директорию пользователя и запуск оттуда.
