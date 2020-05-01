---
id: 114
title: Двумерный динамический массив в C/C++
date: 2013-02-02T11:41:35+03:00
author: Maxim Norin
layout: post
guid: https://mnorin.com/?p=114
permalink: /dvumerny-j-dinamicheskij-massiv-v-cc.html
categories:
  - Статьи
tags:
  - c
  - двумерный
  - динамический
  - массив
  - си
---
Пример двумерного динамического массива.
Этот пример скорее больше подходит для классического Си, но принцип создания динамического двумерного массива в этом примере достаточно прозрачен, что позволяет на его основе использовать, скажем, операторы потоков ввода-вывода cin и cout.
Функция realloc(), которая использована в листинге программы, может быть использована для увеличения уже выделенного участка памяти, а для освобождения памяти может быть использована функция free().
<!--more-->

Прототипы функций для работы с динамической памятью, расположенные в модуле stdlib:
```cpp
#include <stdlib.h>
void *malloc (size_t size);
void *calloc (size_t num, size_t size);
void *realloc(void *block, size_t size);
void *free(void *block);
```
Собственно, сам пример программы:
```cpp
#include <stdio.h>
#include <stdlib.h>
void main()
{
// *** Variables ***

int** ArrayRoot;
int i,j;
int LinesCount,NumbersCount;

// *** Program code ***

// Input size of array
printf("Enter count of lines: ");
scanf("%d",&LinesCount);
printf("Enter count of numbers in line: ");
scanf("%d",&NumbersCount);

// Print entered size
printf("Array size: %dx%d\n",LinesCount,NumbersCount);

// Create array of int* - allocate memory
ArrayRoot= (int **)malloc(sizeof(int*)*LinesCount);

// for every pointer to int
for (i=0;i<LinesCount;i++){
  // Allocate memory for array line of integers
  ArrayRoot[i]=(int *)malloc(sizeof(int)*NumbersCount);
  // Get array numbers
  for(j=0;j<NumbersCount;j++){
    printf("Enter Array [%d,%d]: ",i,j);
    // Enter all the integers in array
    scanf("%d",&ArrayRoot[i][j]);
  };
};

for (i=0;i<LinesCount;i++){
  for(j=0;j<NumbersCount;j++){
    // Print out the integers from array
    printf("Array [%d,%d]: %d\n",i,j,ArrayRoot[i][j]);
  };
};

// Free memory

for (i=0;i<LinesCount;i++){
  // Clear memory used by lines
  ArrayRoot[i]=(int *)realloc(ArrayRoot[i],0);
};

// Clear memory user by pointer to integer pointers
ArrayRoot=(int **)realloc(ArrayRoot,0);
}
```
Результат работы программы:
```
Enter count of lines: 3
Enter count of numbers in line: 2
Array size: 3x2
Enter Array [0,0]: 1
Enter Array [0,1]: 2
Enter Array [1,0]: 3
Enter Array [1,1]: 4
Enter Array [2,0]: 5
Enter Array [2,1]: 6
Array [0,0]: 1
Array [0,1]: 2
Array [1,0]: 3
Array [1,1]: 4
Array [2,0]: 5
Array [2,1]: 6
```
