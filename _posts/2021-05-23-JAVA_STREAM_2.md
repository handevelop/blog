---
title:  "Java Stream API 2"
excerpt: "자바 스트림 2"

categories:
  - java
  - 스터디
last_modified_at: 2020-05-23T22:06:00-05:00
---


# 스트림 필터링

- 스트림은 주어진 람다식을 이용해 특정 요소를 필터링 하는 `filter()` 메소드 및 고유 요소로 이루어진 스트림을 반환하는 `distinct()` 메소드를 지원한다.


```java
List<Integer> numbers = Arrays.asList(1, 2, 1, 3, 3, 2, 4);

numbers.stream()
        .filter(i -> i % 2 == 0)
        .distinct()
        .forEach(System.out::println);

// filiter() 로  2, 2, 4 만 남고
// distinct() 로 2, 4 만 남게 된다
```


# 스트림 슬라이싱


## (Java9) 프리디케이트를 이용한 슬라이싱

- `takeWhile()` 과 `dropWhile()` 을 통해 정렬되어 있는 요소에 대해 `프리디케이트` 를 이용해 효율적으로 작업을 진행할 수 있다.

```java
// 300 칼로리 이하의 음식을 가져오는 스트림 소스. 모든 요소에 1번씩 순회한다.
.filter(dish -> dish.getCalories() <= 300)
.collect(toList());

// takeWhile() 을 이용해 300 칼로리 이하의 음식을 가져오는 스트림 소스. getCalories() 가 300 이 넘으면 슬라이싱 하여 버린다.
.takeWhile(dish -> dish.getCalories() <= 300)
.collect(toList());

// dropWhile() 을 이용해 300 칼로리 초과 음식을 가져오는 스트림 소스. getCalories() 가 300 이 안되는 지점까지는 슬라이싱 하여 버린다.
.takeWhile(dish -> dish.getCalories() <= 300)
.collect(toList());
```

## 스트림 축소

- 스트림은 주어진 사이즈 이하의 크기를 갖는 새로운 스트림을 반환하는 `limit()` 메소드를 지원한다. 스트림이 정렬되어 있으면 정렬된 순서대로 주어진 사이즈 이하의 크기를 갖는 스트림을 반환한다.

- 스트림은 처음 n 개 요소를 제외한 스트림을 반환하는 `skip()` 메소드를 지원한다. n 개 이하의 요소를 포함하는 스트림에 `skip()` 을 호출하면 빈 스트림을 반환한다.



# 매핑

- 매핑은 기존의 값을 바탕으로 새로운 스트림을 만든다는 개념의 `transforming` 의 느낌으로 받아들이면 이해하기 쉽다.


```java
// 요리의 이름들이 몇글자인지 구해서 dishNameLengths 리스트에 반환
List<Integer> dishNameLengths = menu.stream()
                                      .map(Dish::getName)
                                      .map(String::length)
                                      .collect(toList());

```

- `flatMap()` 을 활용하여 여러개의 스트림을 하나로 묶어 연산 할 수 있다. 이를 `스트림 평면화` 라고 한다.

```java
// ["Hello", "world"] 리스트가 있을 때 고유한 캐릭터를 추출하고 싶을 때 flatMap() 을 활용해야 한다.
List<String> uniqueWords = words.stream()
                                  .map(word -> word.split(""))
                                  .distinct()
                                  .collect(toList()); // H e l l o W o r l d 가 반환됨.

List<String> uniqueWords = words.stream()
                                  .map(word -> word.split(""))
                                  .flatMap(Arrays::stream)
                                  .distinct()
                                  .collect(toList()); // H e l o W r d 가 반환됨.
```


(ref : https://12bme.tistory.com/461)