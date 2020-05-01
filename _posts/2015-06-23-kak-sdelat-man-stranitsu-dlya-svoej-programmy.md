---
id: 2176
title: Как сделать man-страницу для своей программы
date: 2015-06-23T00:18:44+03:00
author: Maxim Norin
layout: post
permalink: /kak-sdelat-man-stranitsu-dlya-svoej-programmy.html
categories:
  - Статьи
tags:
  - man
  - linux
  - страница руководств
---
Когда вы собираете свой пакет с программой или какими-то данными, вы можете включить в состав пакета man-страницу, в которой будет содержаться информация о том, для чего ваша программа предназначена, какие у нее есть опции, описание опций работы, кодов возврата и так далее. И, конечно же, там же вы можете указать свои контакты, чтобы пользователи могли с вами связаться, если нашли какую-то ошибку или у них есть идеи о том, как программу можно было бы улучшить.
<!--more-->

В операционных системах класса Unix/Linux man является стандартной программой, которая используется достаточно часто. Согласитесь, удобно дать одну команду и посмотреть информацию об интересующем объекте операционной системы, да еще и в структурированном виде.

Давайте посмотрим, как быстро создать свою man-страничку, которую потом можно разместить в системе и посмотреть информацию при помощи команды man.

Файл man-странички имеет свой определенный формат.

Первая строка имеет такой вид:
```
'\" <строка>
```
Строка, которая здесь указывается, это символ, обозначающий препроцессор. Возможные значения:

    e - eqn
    g - grap
    p - pic
    t - tbl
    v - vgrind
    r - refer

Выберем в качестве препроцессора tbl, тогда первая строчка у нас должна выглядеть так:
```
'\" t
```

Затем нам надо создать заголовок со следующими полями:

- Названием программы и номером раздела, в котором находится страница (будут показаны в первой строке слева и справа и в последней строке справа),
- Датой создания документации (будет показана в самой последней строке посередине)
- Полным названием программы включая версию (будет показано в самой последней строке слева)
- Название, которое будет показано в первой строке man-страницы посередине
```
.TH "MYPROGRAM" "8" "01\ \&JULY\ \&2015" "MYPROGRAM 1.0.0" "MYPROGRAM"
```

Теперь можно указать имена необходимых разделов из этого списка:
- НАЗВАНИЕ (NAME), 
- СИНТАКСИС (SYNOPSIS), 
- НАСТРОЙКИ (CONFIGURATION), 
- ОПИСАНИЕ (DESCRIPTION), 
- ПАРАМЕТРЫ (OPTIONS), 
- КОД ВЫХОДА (EXIT STATUS), 
- ВОЗВРАЩАЕМОЕ ЗНАЧЕНИЕ (RETURN VALUE), 
- ОШИБКИ (ERRORS), 
- ОКРУЖЕНИЕ (ENVIRONMENT), 
- ФАЙЛЫ (FILES), 
- ВЕРСИИ (VERSIONS), 
- СОГЛАСУЕТСЯ С (CONFORMING TO), 
- ЗАМЕЧАНИЯ (NOTES), 
- ОШИБКИ (BUGS), 
- ПРИМЕР (EXAMPLE), 
- АВТОРЫ (AUTHORS),
- СМОТРИТЕ ТАКЖЕ (SEE ALSO).

Названия разделов указываться следующим образом:
```
.SH "NAME"
```
Следующее, что нужно сделать - это выделение жирным. Вот как это делается:
<pre class="lang:default decode:true ">\fB&lt;текст&gt;\fR</pre>
\fB - font bold, \fR - font regular

Еще одна команда нам понадобится для пропуска строчки
```
.PP
```
Для сдвига вправо, или для создания отступа, нам понадобится команда
```
.RS <количество-символов>
```
А для возврата обратно на ту же позицию другая команда
```
.RE
```
И теперь вы уже можете создать страницу руководств, которая будет включать информацию о вашей программе. Вот такую:
```
'\" t

.TH "MYPROGRAM" "8" "01\ \&JULY\ \&2015" "MYPROGRAM 1.0.0" "MYPROGRAM1"

.SH "NAME"
myprogram - some program that does cool stuff
.SH "SYNOPSIS"
\fBmyprogram\fR [\fB--help\fR]
\fBmyprogram\fR [\fB--version\fR]
.SH "DESCRIPTION"
\fBmyprogram\fR is a simple program that make some really cool stuff. I didn't deside what exactly yet, but it will be awesome, trust me.
.SH "OPTIONS"
\fB--help\fR
.RS 4
Show help
.RE
.PP
\fB--version\fR
.RS 4
Show program version
.RE
.SH "AUTHORS"
Cool programmer <cool.programmer@coolmail.com>
```
Теперь переименуем файл в "myprogram.8", поскольку страница будет размещаться в 8 разделе, и сожмем ее программой gzip
```
gzip myprogram.8
```
И поместим в директорию /usr/share/man/man8. 

Размещение в нужной директории можно реализовать либо при установке пакета, либо при установке из исходных кодов во время выполнения скрипта make.

И последнее, что нужно сделать - это дать команду
```
man myprogram
```
Вы увидите вот такую страничку:
```
MYPROGRAM(8)                      MYPROGRAM1                      MYPROGRAM(8)

NAME
       myprogram - some program that does cool stuff

SYNOPSIS
       myprogram [--help] myprogram [--version]

DESCRIPTION
       myprogram  is  a  simple  program  that  make some really cool stuff. I
       didn't deside what exactly yet, but it will be awesome, trust me.

OPTIONS
       --help
           Show help

       --version
           Show program version

AUTHORS
       Cool programmer &lt;cool.programmer@coolmail.com&gt;

MYPROGRAM 1.0.0                  01 JULY 2015                     MYPROGRAM(8)
```
Вот, собственно, и всё. Как видите, получилось очень быстро.