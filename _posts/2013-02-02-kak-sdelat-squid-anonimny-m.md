---
id: 96
title: Как сделать squid анонимным
date: 2013-02-02T01:05:06+03:00
author: Maxim Norin
layout: post
permalink: /kak-sdelat-squid-anonimny-m.html
categories:
  - Статьи
tags:
  - proxy
  - squid
  - анонимность
  - прокси
---
При установке прокси-сервер squid может понадобиться сделать его анонимным.
<!--more-->

Для этого надо добавить следующие строчки в squid.conf:
```
header_access X-Forwarded-For deny all
header_access Via deny all
header_access Cache-Control deny all
header_access Server deny all
header_access From deny all
header_access Client-IP deny all
```
Тогда на сервисах проверки на наличие прокси не будет определяться IP-адрес клиента и что запрос пришел от прокси-сервера. Все запросы будут выглядеть для серверов так, как будто с IP-адреса прокси работает конечный клиент. Если это так, поздравляю. Вам удалось сделать squid анонимным.
