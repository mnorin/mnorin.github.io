---
title: Использование битовой маски для генерации параметров
date: 2018-06-17T04:59:17+03:00
author: Maxim Norin
layout: post
permalink: /bitovaya-maska-dlya-generacii-parametrov.html
categories:
  - Статьи
tags:
  - java
  - битовая маска
  - тестирование
  - тесты
---
Одна из вещей, которые периодически требуются в тестировании, особенно при тестировании различных API, - это генерация сочетаний различных параметров. Скажем, у нас есть несколько параметров, которые могут быть использованы при вызове RESTful сервиса, при этом любой параметр может присутствовать или отсутствовать. Казалось бы, можно использовать циклы для решения этой задачи, но, возможно, более удобным будет использование битовой маски. Давайте посмотрим, как.
<!--more-->

## Использование циклов для генерации строки параметров
Давайте сначала посмотрим, как это будет выглядеть с циклами. Пусть у нас есть некоторая система, хранящая данные о клиентах какого-то абстрактного сервиса (интернет-провайдер, домофонный сервис, ЖКХ и т.д.). Каждая запись о клиенте включает номер договора, фамилию, имя, отчество, дату рождения, номер паспорта, адрес и телефон. Возьмем для примера 4 параметра, по которым осуществляется поиск (естественно, при условии, что их можно комбинировать):

- номер договора (contract),
- фамилия (lastName),
- имя (firstName),
- номер паспорта (passport).

Наша задача будет очень простая: провести позитивный тест и проверить, что при указании от 1 до 4 поисковых параметров сервис возвращает какой-то ответ, если у нас есть такие данные в базе.

Для простоты, чтобы не усложнять код, предположим, что мы во время теста берем данные из базы данных, и они у нас равны следующим:

- contract: 12345678
- lastName: Иванов
- firstName: Сергей
- passport: 5101123456

Для проверки нам надо использовать один любой параметр (каждый параметр), затем любые два параметра (каждое сочетание из двух любых параметров), затем любые три параметра (все возможные сочетания), и, наконец, все 4 параметра.

Поскольку мы будем рассматривать Java 8, удобнее всего будет написать какой-то DataProvider, который будет возвращать массив строк параметров (одномерный массив) для использования в запросе, каждая из которых будет включать уникальный набор параметров.

Казалось бы, логично это организовать в виде циклов. В данном случае это может выглядеть так:

```java
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;
import java.util.*;

public class LoopTests {

  @DataProvider(name = "paramsProvider")
  public Object[] requestLineProvider(){
    // Список параметров со значениями
    String[] params = {
      "contract=12345678",
      "lastName=Иванов",
      "firstName=Сергей",
      "passport=5101123456"
    };

    List parametersLines = new LinkedList();

    for (int i = 0; i < 2; i++){
      for (int j = 0; j < 2; j++){
        for (int k = 0; k < 2; k++){
          for (int m = 0; m < 2; m++){
            String parametersLine = "";
            // Параметр 1
            if(i % 2 == 0) {parametersLine = parametersLine + ((parametersLine.equals(""))?"":"&") + params[0];}
            // Параметр 2
            if(j % 2 == 0) {parametersLine = parametersLine + ((parametersLine.equals(""))?"":"&") + params[1];}
            // Параметр 3
            if(k % 2 == 0) {parametersLine = parametersLine + ((parametersLine.equals(""))?"":"&") + params[2];}
            // Параметр 4
            if(m % 2 == 0) {parametersLine = parametersLine + ((parametersLine.equals(""))?"":"&") + params[3];}
            parametersLines.add(parametersLine);
          }
        }
      }
    }
    return parametersLines.toArray();
  }

  @Test(dataProvider = "paramsProvider")
  public void loopTest(String requestParamsLine){
  // Поскольку это просто пример, просто выведем строку, переданную в качестве параметра
  System.out.println("Request parameters: " + requestParamsLine);
  // ... Какие-то проверки
  }
}
```

Каждый цикл отвечает за включение или не включение соответствующего параметра в итоговую строку. Результат вывода будет, в общем, ожидаем. 2 (состояния) в 4 (количество параметров) степени, итого 16 вариантов сочетаний параметров:

```text
Request parameters: contract=12345678&lastName=Иванов&firstName=Сергей&passport=5101123456
Request parameters: contract=12345678&lastName=Иванов&firstName=Сергей
Request parameters: contract=12345678&lastName=Иванов&passport=5101123456
Request parameters: contract=12345678&lastName=Иванов
Request parameters: contract=12345678&firstName=Сергей&passport=5101123456
Request parameters: contract=12345678&firstName=Сергей
Request parameters: contract=12345678&passport=5101123456
Request parameters: contract=12345678
Request parameters: lastName=Иванов&firstName=Сергей&passport=5101123456
Request parameters: lastName=Иванов&firstName=Сергей
Request parameters: lastName=Иванов&passport=5101123456
Request parameters: lastName=Иванов
Request parameters: firstName=Сергей&passport=5101123456
Request parameters: firstName=Сергей
Request parameters: passport=5101123456
Request parameters:
```

Но в таком случае у нас возникает следующая проблема: а что будет, если мы добавим еще два параметра? А потом удалим три? А потом снова добавим четыре? В любом из этих случаев нам придется редактировать код, добавляя или удаляя вложенные циклы. Что в любом случае плохо. Чем больше меняется код, тем потенциально больше шансы сделать ошибку и т.д. Соответственно, надо сделать так, чтобы не трогать сам код, а менять только данные, которыми мы будем оперировать. И в этом нам поможет...

## Использование битовой маски для генерации параметров

Использование битовой маски позволит нам избежать вложенных циклов и изменения кода, потому что количество параметров никак не повлияет на то, как будет сгенерирована строка параметров:
```java
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;
import java.util.LinkedList;

public class FlagsTests {

    @DataProvider(name = "paramsProvider")
    public Object[] requestLineProvider(){
        // Список параметров со значениями
        String[] params = {
                "contract=12345678",
                "lastName=Иванов",
                "firstName=Сергей",
                "passport=5101123456"
        };

        LinkedList<String> parametersLines = new LinkedList<String>();

        int paramsLength = params.length;

        int counter = (int) Math.pow(2,paramsLength);
        for (Integer mask = 0; mask < counter; mask++){
            String result = "";
            for (int bitNumber = 0; bitNumber < paramsLength; bitNumber++) {
                result = result + (((mask & (1 << bitNumber)) == 0) ? "" :
                        (result.equals("") ? "" : "&") + params[bitNumber]);
            }
            parametersLines.add(result);
        }

        return parametersLines.toArray();
    }

    @Test(dataProvider = "paramsProvider")
    public void flagsTest(String requestParamsLine){
        // Поскольку это просто пример, просто выведем строку, переданную в качестве параметра
        System.out.println("Request parameters: " + requestParamsLine);
        // ... Какие-то проверки
    }
}
```

Результат будет следующий:
```
Request parameters: 
Request parameters: contract=12345678
Request parameters: lastName=Иванов
Request parameters: contract=12345678&lastName=Иванов
Request parameters: firstName=Сергей
Request parameters: contract=12345678&firstName=Сергей
Request parameters: lastName=Иванов&firstName=Сергей
Request parameters: contract=12345678&lastName=Иванов&firstName=Сергей
Request parameters: passport=5101123456
Request parameters: contract=12345678&passport=5101123456
Request parameters: lastName=Иванов&passport=5101123456
Request parameters: contract=12345678&lastName=Иванов&passport=5101123456
Request parameters: firstName=Сергей&passport=5101123456
Request parameters: contract=12345678&firstName=Сергей&passport=5101123456
Request parameters: lastName=Иванов&firstName=Сергей&passport=5101123456
Request parameters: contract=12345678&lastName=Иванов&firstName=Сергей&passport=5101123456
```
Теперь давайте просто добавим еще один параметр в массив params не изменяя больше ничего:
```java
String[] params = {
  "contract=12345678",
  "lastName=Иванов",
  "firstName=Сергей",
  "passport=5101123456",
  "birthDate=10.10.1976"
};
```
Теперь результат будет такой:
```
Request parameters: 
Request parameters: contract=12345678
Request parameters: lastName=Иванов
Request parameters: contract=12345678&lastName=Иванов
Request parameters: firstName=Сергей
Request parameters: contract=12345678&firstName=Сергей
Request parameters: lastName=Иванов&firstName=Сергей
Request parameters: contract=12345678&lastName=Иванов&firstName=Сергей
Request parameters: passport=5101123456
Request parameters: contract=12345678&passport=5101123456
Request parameters: lastName=Иванов&passport=5101123456
Request parameters: contract=12345678&lastName=Иванов&passport=5101123456
Request parameters: firstName=Сергей&passport=5101123456
Request parameters: contract=12345678&firstName=Сергей&passport=5101123456
Request parameters: lastName=Иванов&firstName=Сергей&passport=5101123456
Request parameters: contract=12345678&lastName=Иванов&firstName=Сергей&passport=5101123456
Request parameters: birthDate=10.10.1976
Request parameters: contract=12345678&birthDate=10.10.1976
Request parameters: lastName=Иванов&birthDate=10.10.1976
Request parameters: contract=12345678&lastName=Иванов&birthDate=10.10.1976
Request parameters: firstName=Сергей&birthDate=10.10.1976
Request parameters: contract=12345678&firstName=Сергей&birthDate=10.10.1976
Request parameters: lastName=Иванов&firstName=Сергей&birthDate=10.10.1976
Request parameters: contract=12345678&lastName=Иванов&firstName=Сергей&birthDate=10.10.1976
Request parameters: passport=5101123456&birthDate=10.10.1976
Request parameters: contract=12345678&passport=5101123456&birthDate=10.10.1976
Request parameters: lastName=Иванов&passport=5101123456&birthDate=10.10.1976
Request parameters: contract=12345678&lastName=Иванов&passport=5101123456&birthDate=10.10.1976
Request parameters: firstName=Сергей&passport=5101123456&birthDate=10.10.1976
Request parameters: contract=12345678&firstName=Сергей&passport=5101123456&birthDate=10.10.1976
Request parameters: lastName=Иванов&firstName=Сергей&passport=5101123456&birthDate=10.10.1976
Request parameters: contract=12345678&lastName=Иванов&firstName=Сергей&passport=5101123456&birthDate=10.10.1976
```
Вот, собственно, и всё. Это ровно то, что и нужно было. На объем выходных данных влияет только объем входных данных без необходимости изменения кода.

Теперь давайте подробнее посмотрим вот на эти две строчки:
```java
result = result + (((mask & (1 << bitNumber)) == 0) ? "" :
    (result.equals("") ? "" : "&") + params[bitNumber]);
```
Здесь мы делаем вот что:

1. Сдвигаем число 1 побитово влево на bitNumber битов.
2. Выполняем побитовое AND с числом, содержащимся в переменной mask.
3. Если результат побитового AND равен 0, то добавляем к строке result пустую строку. Результат будет равен нулю только в том случае, если в маске не установлен соответствующий определенному параметру бит. Поскольку (1 AND 1) даёт 1, а (0 AND 1) даёт 0.
4. Если результат побитового AND не равен 0, то добавляем к строке result параметр, индекс которого соответствует номеру бита. Если строка result не пустая, вставляем перед параметром амперсанд.

## В заключение
Использование битовой маски может быть очень удобным, если вам надо перебрать все варианты сочетаний каких-то параметров, которые могут либо присутствовать, либо отсутствовать, порядок следования которых вам не важен. Это позволит избежать большого количества вложенных циклов и изменения кода теста после изменения требований, связанных с количеством параметров.
