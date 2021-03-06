---
title: Как определить, каким алгоритмом зашифрован пароль в /etc/shadow?
date: 2014-12-01T00:03:18+03:00
author: Maxim Norin
layout: post
permalink: /kak-opredelit-kakim-algoritmom-zashifrovan-parol-v-etc-shadow.html
categories:
  - Вопросы и ответы
tags:
  - hash
  - passwd
  - shadow
  - пароль
  - хэш
---
__Вопрос:__ Как определить, каким алгоритмом зашифрован пароль в /etc/shadow?
<!--more-->

__Ответ:__

Зашифрованный пароль в /etc/shadow выглядит следующим образом:
```
$идентификатор-алгоритма$соль$хэш
```
То есть, определить алгоритм шифрования можно по идентификатору, находящемуся между первыми двумя знаками доллара. Вот значения, которые могут там находиться:

- $1$ - MD5
- $2a$ - Blowfish
- $2y$ - Blowfish (с коррекцией обработки 8-битных символов)
- $5$ - SHA-256
- $6$ - SHA-512

В современных системах обычно встречается именно SHA-512.