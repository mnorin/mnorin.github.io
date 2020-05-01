---
id: 1738
title: Различие скобок в bash
date: 2014-11-16T01:50:16+03:00
author: Maxim Norin
layout: post
permalink: /razlichie-skobok-v-bash.html
categories:
  - Статьи
tags:
  - bash
  - linux
  - скобки
  - скрипт
---
При написании скриптов bash, как вы знаете, можно использовать круглые () и фигурные скобки {}. И те, и другие позволяют объединить несколько операторов в один составной. Давайте рассмотрим, в чем же различие скобок в bash, и когда имеет смысл использовать в скриптах круглые скобки, а когда фигурные.
<!--more-->

## Сходства
И те, и другие скобки позволяют объединять несколько команд в один составной оператор, который может получать какие-то данные на вход и выдавать какие-то данные на выход. Для перенаправления данных обычно традиционно используют пайп (вертикальную черту). Выглядит это так:
```bash
command1 | (
command2
...
commandN
) | command M
```
Выполняется первая команда, которая передает свой стандартный поток вывода составному оператору, обозначенному скобками. Обычно встречается именно такой вариант - с круглыми скобками.

Фигурные скобки более привычно наблюдать при создании функций bash:
```bash
function(){
command1
...
commandN
}
```
И в первом, и во втором случае скобки объединяют группы операторов. И работают они, в принципе, похоже. Поэтому никто нам не запрещает создать такую структуру:
```
command1 | {
command2
...
commandN
} | commandM
```

Работает она похожим образом. Несколько операторов, на вход принимается вывод команды command1, а вывод перенаправляется на поток стандартного ввода commandM.

## Различия
Чтобы понять различия давайте посмотрим два варианта скрипта. Первый вариант:
```bash
VAR1="/var"
ls | (
echo $VAR1
) | sort
```

Второй вариант:
```bash
VAR1="/var"
ls | {
echo $VAR1
} | sort
```

Вроде бы одинаковые скрипты. В чем, казалось бы, разница? Давайте посмотрим на результат выполнения. Результат одинаковый. Значит, можно использовать и тот, и другой скрипт абсолютно одинаково?

Немного поменяем скрипты.

Первый вариант:
```bash
#!/bin/bash
for i in {1..10000}
do
ls | (
ls / > /dev/null
)
done
```

Второй вариант:
```bash
#!/bin/bash
for i in {1..10000}
do
ls | {
ls / > /dev/null
}
done
```

Замерим время выполнения этих скриптов
```bash
$ time ./script1.sh

real 0m28.021s
user 0m1.187s
sys 0m5.863s

$ time ./script2.sh

real 0m33.163s
user 0m0.787s
sys 0m4.517s
```
Наглядно видно различие во времени выполнения, если несколько раз запустить скрипты, показатели будут примерно такие же. Причем абсолютное время выполнения скрипта с круглыми скобками меньше, а нагрузка на систему больше. Скрипт с фигурным скобками работает медленнее, зато меньше нагружает систему. Команда, которую мы использовали, - встроенная команда bash

Теперь посмотрим вывод другой команды time - внешней.
```
$ /usr/bin/time ./script1.sh
1.17user 5.78system 0:27.55elapsed 25%CPU (0avgtext+0avgdata 3168maxresident)k
0inputs+0outputs (0major+8557180minor)pagefaults 0swaps

$ /usr/bin/time ./script2.sh
0.71user 4.60system 0:32.66elapsed 16%CPU (0avgtext+0avgdata 3168maxresident)k
0inputs+0outputs (0major+9954334minor)pagefaults 0swaps
```

Явно видна разница в потреблении процессорного времени. Давайте посчитаем разницу в процентах по времени и по потреблению процессора.

Отношение времени работы скриптов:
```
32,66/27,55=1,18548094374
```
Таким образом, вариант с фигурными скобками работает на 18,5 процентов медленнее.

Давайте теперь посмотрим, какова разница в потреблении процессорного времени:
```
0,25/0,16=1,5625
```

Вариант с фигурными скобками использует на 56,2 процента процессорного времени меньше.

Почему так происходит? Ответ можно найти в документации по bash. То, что находится между круглыми скобками, выполняется в отдельном подпроцессе. А то, что находится между фигурными скобками - выполняется в контексте текущей оболочки.