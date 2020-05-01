---
title: Параметры командной строки в C/C++
date: 2013-02-02T11:33:11+03:00
author: Maxim Norin
layout: post
permalink: /parametry-komandnoj-stroki-v-cc.html
categories:
  - Статьи
tags:
  - argc
  - argv
  - c
  - параметр командной строки
  - си
---
Параметры командной строки в программах на C/C++ используются достаточно часто.
И делается это очень просто. Функция main() имеет параметры, которые могут использоваться или не использоваться в программе. Эти параметры - __int argc__ (ARGuments Counter) и __char** argv__ (ARGuments Values).

Вот простой пример их использования:
<!--more-->
params.c:
```c
# include 

main(int argc, char** argv){

  int i;

  for(i=0; i<=argc; i++)
  {
    printf("Command line parameter [%d] is %s\n",i,argv[i]);
  }
}
```
При запуске программы будут выведены все параметры командной строки. Если параметры при запуске программы не указывать, будет выведен нулевой параметр, то есть название файла программы.

При указании параметров:
```
$ ./params param 1 param2
Command line parameter [0] is ./params
Command line parameter [1] is param
Command line parameter [2] is 1
Command line parameter [3] is param2
```
При отсутствии параметров:
```
$ ./params
Command line parameter [0] is ./params
```
