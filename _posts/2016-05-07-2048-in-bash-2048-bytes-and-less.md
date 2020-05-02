---
title: Пишем игру 2048 на bash в 2048 байт и меньше
date: 2016-05-07T20:18:37+03:00
author: Maxim Norin
layout: post
permalink: /2048-in-bash-2048-bytes-and-less.html
categories:
  - Статьи
tags:
  - "2048"
  - bash
  - linux
  - игра
---
Вы наверняка знакомы с игрой 2048. Достаточно простая, но захватывающая игра, смысл которой в том, чтобы перемещать клетки с цифрами, которые при соединении двух клеток с одинаковым числовым значением превращаются в одну клетку с числовым значением, представляющим сумму этих двух значений. И поскольку я большой любитель bash, я решил написать эту игру на bash'е, уложив ее в 2048 байт.
<!--more-->

## Укладываемся в 2048 байт
Как оказалось, это достаточно несложно, в 2 килобайта можно уложить игру, при этом туда влезли еще и копирайты с контактами. Но тут нужно учесть кое-что. Поскольку я не вижу смысла в подсчете очков, и смысл игры все-таки в том, чтобы получить плитку с числом 2048, я не стал его делать. От этого игра хуже не стала.

Вот, собственно, текст bash-скрипта [2048.sh](https://github.com/mnorin/bash-scripts/blob/master/games/2048.sh):
```bash
#!/bin/bash

# Copyright: Maxim Norin, (c)2016
# Game "2048" written in pure bash with size 2048 (including this text)
# https://mnorin.com
# E-mail: mnorin@mnorin.com

M=()
L=()

align(){
for i in {1..3}
{
for j in {1..3}
{
[ "${L[$j]}" != "" ] && [ "${L[$j-1]}" == "" ] && L[$j-1]=${L[$j]} && L[$j]=""
}
}
}

sum(){
for i in {1..3}
{
[ "${L[$i]}" == "${L[$i-1]}" ] && [ "${L[$i]}" != "" ] && L[$i-1]=$(( ${L[$i]} * 2 )) && L[$i]=""
}
}

sumup(){
align
sum
align
}

left(){
for n in 0 4 8 12
{
L=( ${M[$n]} ${M[$n+1]} ${M[$n+2]} ${M[$n+3]} )
sumup
M[$n]=${L[0]}
M[$n+1]=${L[1]}
M[$n+2]=${L[2]}
M[$n+3]=${L[3]}
}
}

right(){
for n in 0 4 8 12
{
L=( ${M[$n+3]} ${M[$n+2]} ${M[$n+1]} ${M[$n]} )
sumup
M[$n+3]=${L[0]}
M[$n+2]=${L[1]}
M[$n+1]=${L[2]}
M[$n]=${L[3]}
}
}

up(){
for n in 0 1 2 3
{
L=( ${M[$n]} ${M[$n+4]} ${M[$n+8]} ${M[$n+12]} )
sumup
M[$n]=${L[0]}
M[$n+4]=${L[1]}
M[$n+8]=${L[2]}
M[$n+12]=${L[3]}
}
}

down(){
for n in 0 1 2 3
{
L=( ${M[$n+12]} ${M[$n+8]} ${M[$n+4]} ${M[$n]} )
sumup
M[$n+12]=${L[0]}
M[$n+8]=${L[1]}
M[$n+4]=${L[2]}
M[$n]=${L[3]}
}
}

board(){
D="---------------------"
S="%s\n|%4s|%4s|%4s|%4s|\n"
clear
p=printf
echo 2048.bash
echo
$p $S $D ${M[0]:-"."} ${M[1]:-"."} ${M[2]:-"."} ${M[3]:-"."}
$p $S $D ${M[4]:-"."} ${M[5]:-"."} ${M[6]:-"."} ${M[7]:-"."}
$p $S $D ${M[8]:-"."} ${M[9]:-"."} ${M[10]:-"."} ${M[11]:-"."}
$p $S $D ${M[12]:-"."} ${M[13]:-"."} ${M[14]:-"."} ${M[15]:-"."}
echo $D
echo
echo "Moves: w,a,s,d, Quit: q"
}

a2(){
n=$(($RANDOM % 16))
while [ "${M[$n]}" != "" ]
do
n=$(($RANDOM % 16))
done
M[$n]=2
}

setup(){
for i in {0..15}; do M[$i]=""; done
a2
a2
board
}

check(){
F=1
for i in {0..15}
{
[ "${M[$i]}" == "" ] && F=0
}
return $F
}

game.over(){
while [ "$REPLY" != "y" ] && [ "$REPLY" != "n" ]
do
read -n 1 -p "GAME OVER! Play again? (y/n)"
done
case $REPLY in
y) setup; return 1 ;;
n) exit ;;
esac
}

RANDOM=12345

setup

while :
do
read -n 1 -s
case $REPLY in
w) up ;;
a) left ;;
s) down ;;
d) right ;;
q) exit ;;
*) continue ;;
esac

check || game.over || continue
board
sleep 1
a2
board
done
```
Как видите, скрипт достаточно понятный, можно еще сократить из спортивного интереса.

## Оптимизируем
После некоторых оптимизаций у меня получился вариант размером 1174 байта. Кстати, рандомизация в нем работает несколько лучше. Вот сам скрипт

[2048_min.sh](https://github.com/mnorin/bash-scripts/blob/master/games/2048_min.sh)
```bash
#!/bin/bash
h="1 2 3"
a(){
for i in $h $h $h
{
[ -n "${L[$i]}" ] && [ -z "${L[$i-1]}" ] && L[$i-1]=${L[$i]} && L[$i]=""
}
}
s(){
for i in $h
{
[ "${L[$i]}" == "${L[$i-1]}" ] && [ -n "${L[$i]}" ] && L[$i-1]=$((${L[$i]}*2)) && L[$i]=""
}
}
_(){
for n in $5 $6 $7 $8
{
L=( ${M[$n+$1]} ${M[$n+$2]} ${M[$n+$3]} ${M[$n+$4]} )
a;s;a
M[$n+$1]=${L[0]}
M[$n+$2]=${L[1]}
M[$n+$3]=${L[2]}
M[$n+$4]=${L[3]}
}
}
S="%s\n|%4s|%4s|%4s|%4s|\n"
p=printf
D="---------------------"
e=echo
w="$p $S $D"
q(){
$w ${M[$1]:-"."} ${M[$2]:-"."} ${M[$3]:-"."} ${M[$4]:-"."}
}
m="0 4 8 12"
b(){
clear
for i in $m
{
q $i+{0,1,2,3}
}
$e $D
$e "Moves: w,a,s,d, Quit: q"
}
R(){
n=$(($RANDOM%16))
}
t(){
R
while [ -n "${M[$n]}" ]
do
R
done
M[$n]=2
}
p(){
for i in {0..15}
{
M[$i]=
}
t;t;b
}
c(){
F=1
for i in {0..15}
{
[ -z "${M[$i]}" ] && F=0
}
return $F
}
o(){
until [ "$REPLY" = "y" -o "$REPLY" = "n" ]
do
read -n 1 -p "GAME OVER! Play again? (y/n)"
done
case $REPLY in
y) p; return 1 ;;
n) exit ;;
esac
}
RANDOM=$RANDOM
g="0 1 2 3"
C=continue
p
for ((;;))
{
read -n 1 -s
case $REPLY in
w) _ $m $g ;;
a) _ $g $m ;;
s) _ 12 8 4 0 $g ;;
d) _ {3..0} $m ;;
q) exit ;;
*) $C ;;
esac
c || o || $C
b
sleep 1
t
b
}
```
Выглядит несколько иначе, менее читабельный, но уже получше оптимизирован. Если хотите, можете добавить окончание игры с выигрышем по получении плитки со значением 2048 или добавить еще какие-то оптимизации, буду рад отзывам и предложениям в комментариях.