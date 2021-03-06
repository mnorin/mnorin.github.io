---
id: 2291
title: Несколько способов ускорить bash-скрипты
date: 2015-08-24T09:04:15+03:00
author: Maxim Norin
layout: post
permalink: /neskol-ko-sposobov-uskorit-bash-skripty.html
categories:
  - Статьи
tags:
  - bash
  - linux
---
Думаю, не сделаю открытия, если скажу, что в принципе bash-скрипты штука довольно медленная. Но это обычно не критично, потому что все-таки главная задача bash-скриптов - это автоматизация тех действий, которые выполняются руками. Так что по сравнению с ручным выполнением скорость просто фантастическая. Тем не менее, бывает необходимо ускорить скрипты, если обрабатываются какие-то большие объемы данных.
<!--more-->

## Скорость выполнения скриптов

Есть определенные факторы, которые влияют на скорость выполнения скриптов. К ним можно отнести:
- Использование внешних команд
- Использование излишне сложных или просто неоптимальных регулярных выражений
- Вывод в консоль каких-то данных
- Использование лишних команд
- Использование круглых или фигурных скобок (об этом я [писал отдельно](/razlichie-skobok-v-bash.html)
- и т.д.

И, соответственно, хороший скрипт
- Не использует специфические программы, которые есть не везде (требуют установки дополнительных пакетов)
- Использует минимум внешних команд
- Использует оптимизированные регулярные выражения (которые, как ни печально, многие вообще не используют), например, с минимальным использование захватывающих квантификаторов. Регулярки - это вообще отдельная интересная тема, по поводу которой могу порекомендовать книгу Джеффри Фридла "Регулярные выражения" (если ваш английский позволяет, то лучше в оригинале) и много практики.
- Не использует переменные для хранения данных, когда можно использовать пайп и запускать два процесса параллельно вместо последовательного запуска

Кроме скорости, естественно, есть еще такой фактор как совместимость. Если вы уверены, что в других оболочках скрипт выполняться не будет, или будет работать медленно, лучше откажитесь от совместимости в пользу скорости. Какой смысл писать скрипты, которые будут в 99% случаев выполняться в bash, но работать будут в 100% случаев медленно? Тем более что bash есть буквально везде, даже на смартфонах и роутерах. В наше время написание совместимых скриптов - это чаще вопрос предпочтений, чем реальной необходимости. Не очень приятного бывает осознавать, что скрипт может работать под разными оболочками, но работает 100% в баше, и, в связи с отсутствием "заточки" под баш, работает он заметно медленнее, чем мог бы.

Поэтому давайте рассмотрим некоторые моменты, касающиеся скорости скриптов на примерах.

## Использование внешних команд

Вызов внешних программ работает медленнее, чем внутренних, соответственно, множественные вызовы, например, в цикле, будут работать медленнее, чем внутренние. Пример:
```bash
VAR1=123
VAR1=$(echo $VAR1 | sed 's/2.*/45/')
```
Переменная VAR1 примет значение "145". Но то же самое можно сделать вообще без использования sed'а. Вот так:
```bash
VAR1=123
VAR1=${VAR1/2*/45}
```
Такой вариант лучше не только тем, что не использует sed, но и тем, что не использует echo, и не используется вызов отдельного экземпляра интерпретатора, из которого вызываются echo и sed. Давайте посмотрим, как по времени отличаются 1000 выполнений первых двух команд и вторых двух команд. Первые:
```
real    0m1.136s
user    0m0.052s
sys     0m0.064s
```
Вторые:
```
real    0m0.004s
user    0m0.000s
sys     0m0.000s
```
Ну, тут даже комментировать нечего, циферки очень показательные. А, казалось бы, разница в одной строчке.

## Использование регулярных выражений
Как я уже писал выше, тема очень интересная и достаточно специфическая, но тут, я думаю, стоит привести пример. Не исключительно связанный с самой оболочкой bash, но связанный с написанием скриптов. Простой пример. У нас есть некоторый файл, содержащий строки, из которых нам надо получить определенную информацию. Строки выглядят следующим образом: некоторое числовое значение, затем пробел и какая-то строка. Надо заменить пробел на знак подчеркивания.

Первый вариант:
```bash
#!/bin/bash
sed -r 's/([0-9]) ([a-z])/\1_\2/' file.dat
```
И второй вариант:
```bash
#!/bin/bash
sed 's/ /_/' file.dat
```
Первый вариант вроде как дает более наглядное представление, какой именно пробел мы заменяем (между цифрой и буквой), но у нас по условию в данных нет другого пробела, и указывать это абсолютно не нужно, кроме того, мы вычисляем два выражения для подстановки, что тоже нам не нужно. Второй вариант делает всё по существу. Взяли пробел - тупо вставили на его место знак подчеркивания. Отлично читается, и, естественно, выполняется быстрее. При 10000 записей первый скрипт:
```
real    0m0.071s
user    0m0.068s
sys     0m0.000s
```
Второй скрипт:
```
real    0m0.019s
user    0m0.016s
sys     0m0.000s
```
Что, в общем, логично. Ненужные вычисления ненужных данных. Я уже не говорю про регулярное выражение "(.*)", при использовании которого в скриптах, где обрабатывается много данных, надо обязательно подумать, надо ли использовать звездочку. В общем, у Фридла по этому поводу хорошо написано.

## Вывод в консоль данных

Я, естественно, имею ввиду те данные, которые для работы скрипта никакой роли не играют. Сам вывод на экран большого количества данных может также тормозить работу скрипта и, просто отключив ненужный вывод, можно ускорить bash-скрипт. Первый скрипт:
```bash
#!/bin/bash
sed -r 's/(.*) (.*)/\1_\2/' file.dat
```
Второй скрипт:
```
#!/bin/bash
sed -r 's/(.*) (.*)/\1_\2/' file.dat > /dev/null
```
Как видите, разница только в том, что первый скрипт выводит строки в консоль, а второй в /dev/null. По времени они отличаются, естественно, вот первый скрипт:
```
real    0m0.155s
user    0m0.064s
sys     0m0.040s
```
А вот второй:
```
real    0m0.078s
user    0m0.064s
sys     0m0.012s
```
Просто запомните, что вывод на экран большого количества данных может сделать ваш скрипт медленнее. Особенно это заметно, если скрипт работает несколько часов, дублируя какие-то данные на консоль. Можно действительно ускорить скрипт в несколько раз, если просто подумать, что можно исключить, а что действительно нужно для понимания того, что происходит в данный момент.

## Использование лишних команд

Это достаточно распространенный вариант, который часто связан с тем, что сначала пишут некоторый скрипт, а потом или в спешке, или по невнимательности забывают убрать лишнее. Самый частый вариант - использовали команду cat для вывода какого-то файла при дебаге парсинга в консоли, и вставили в том же виде в скрипт, хотя можно напрямую grep'у указать брать данные из файла. Или еще один пример: использование лишних команд echo. Команда внутренняя, конечно, но если она часто используется, можно ее все-таки пореже использовать, если необходима максимальная скорость.

Первый скрипт:
```bash
#!/bin/bash
for i in {1..10000}
do
echo "1"
echo "2"
echo "3"
done
```
Второй скрипт:
```bash
#!/bin/bash
for i in {1..10000}
do
echo -e "1\n2\n3"
done
```
И, соответственно, время работы этих двух скриптов. Первый:
```
real    0m0.375s
user    0m0.192s
sys     0m0.092s
```
Второй:
```
real    0m0.225s
user    0m0.080s
sys     0m0.068s
```
Что, в общем, логично.

Ну, а про круглые и фигурные скобки я писал отдельную статью, которую вы можете посмотреть [здесь](/razlichie-skobok-v-bash.html).
