---
title: KDE 3.5, Kopete и ICQ
date: 2013-02-06T02:00:32+03:00
author: Maxim Norin
layout: post
permalink: /kde-3-5-kopete-i-icq.html
categories:
  - Статьи
tags:
  - "3.5"
  - icq
  - kde
  - kopete
---
Существует проблема с работой ICQ в старых версиях Kopete для KDE 3.5, она не соединяется. Хотя это уже старые версии программного обеспечения, проблема может быть еще для кого-то актуальной.
Файл ~/.kde/share/config/kopeterc
Открыть на редактирование, добавить следующие строчки:
```
[ICQVersion]
Build=1042
ClientId=266
ClientString=ICQ Client
Country=us
Lang=en
Major=6
Minor=5
Other=0
Point=0
```
Прямо так скопировать и вставить.
Сохранить, перезапустить kopete.
