---
title:  "Java Stream API"
excerpt: "자바 스트림"

categories:
  - java
  - 스터디
last_modified_at: 2020-05-23T08:06:00-05:00
---


# 스트림 API 란?

- 스트림 API 는 java8 부터 도입된 `java.util.stream` 패키지에서 제공하는 기능을 말한다.

- 스트림부터 설명을 하자면 `데이터 처리 연산을 지원하도록 소스에서 추출된 연속된 요소` 이다.

- 그럼 스트림 API 는? `소스에서 추출된 연속 요소로 손쉽고 멋있게 데이터 처리 연산을 가능하도록 하는 API` 이다.


# 스트림의 특징

- `선언형` : 더 간결하고 가독성이 좋아진다.

- `조립할 수 있음` : 스트림 파이프라인을 통해 조립하여 유연성이 좋아진다.

- `병렬화` : 쉽게 병렬화를 할 수 있어 성능이 좋아진다.

- 스트림은 내부 반복을 지원하며 내부 반복은 `filter, map, sorted` 등의 연산으로 반복을 추상화한다.

- 스트림은 `중간연산`, `최종연산` 이 존재한다.

- `중간연산` 은 `filter, map, sorted, limit, distinct` 가 `최종연산` 은 `collect, forEach, count` 가 존재한다.

- `중간연산` 은 `Stream<T or R>` 을 반환하면서 다른 연산과 연결되는 연산이다. 이렇게 스트림 파이프라인을 구성할 수 있지만 중간 연산으로는 결과가 생성되지 않고 `최종연산` 을 통해서만 결과가 생성된다.


# 스트림 맛보기

```java
List<String> threeHighCaloricDishNames = 
  menu.stream()
      .filter(dish -> dish.getCalories() > 300) // 칼로리가 300 이상인 필터 (중간연산)
      .map(Dish::getName) // name String 필드만 추출 (중간연산)  
      .limit(3) // 최대 3개로 중간요소 리미트 (중간연산)
      .collect(toList()); // List 자료구조로 콜렉트하여 반환 (최종연산)

```

- `filter` : 람다식을 인수로 받아 특정 요소를 필터한다.

- `map` : 람다식을 이용해서 한 요소를 다른 요소로 변환하거나 정보를 추출한다.

- `limit` : 정해진 개수 이상의 요소가 스트림에 저장되지 못하도록 스트림 크기를 축소 `truncate` 한다.

- `collect` : 스트림을 다른 형식으로 변환하는 최종연산이다.


# 컬렉션과 스트림 비교

- `데이터를 언제 계산` 하느냐가 두개의 가장 큰 차이이다.

- `컬렉션` 은 모든 값을 메모리에 저장하는 자료구조이다.

- `스트림` 은 `지연(lazy) 처리` 되어 사용자가 요청하는 값만 스트림에서 추출한다. 또한 원본을 변경하지 않고 결과를 담은 새로운 스트림을 반환한다.

- `컬렉션` 은 `외부반복`, `스트림` 은 `내부반복` 을 이용한다. `내부반복`의 이점은 `반복자를 사용할 필요가 없고` `병렬성 처리를 투명하고 쉽게` 도와준다.
- `컬렉션` 은 DVD, `스트림` 은 스트리밍 비디오에 비유 할 수 있다.

```java
// java8 이전
// 외부반복 사용
int sum = 0;
int count = 0;
for (Employee emp : emps) {
  if (emp.getSalary() > 100_000_000) {
    sum += emp.getSalary();
    count++;
  }
}
double average = (double) sum / count;

// java8 이후
// 내부반복 사용
double average = emps.stream()
                      .filter(emp -> emp.getSalary() > 100_000_000)
                      .mapToInt(Employee::getSalary)
                      .average()
                      .orElse(0);
```


# 루프 퓨전과 쇼트서킷

- 요소가 여러개 이지만 일찍이 최종연산을 통해 반환하는 것을 `쇼트서킷` 이라 한다.

- 서로 다른 연산자 이지만 한 과정으로 병합되는 것을 `루프 퓨전` 이라 한다.

```java
// menu 에는 ["탕수육", "치킨", "짜장면", "짬뽕"] 총 4개의 String 이 들어있다.
List<String> names = menu.stream()
                          .filter(dish -> {
                            System.out.println("필터 수행" + dish.getName());
                            return dish.getCalories() > 300;
                          })
                          .map(dish -> {
                            System.out.println("맵 수행" + dish.getName());
                            return dish.getName();
                          })
                          .limit(3)
                          .collect(toList());

// 결과
필터 수행 탕수육
맵 수행 탕수육
필터 수행 치킨
맵 수행 치킨
필터 수행 짜장면
맵 수행 짜장면
```

- 위 예제와 같이 총 4개의 리스트 요소에 모두 접근하지 않고 3개만을 선택된 것을 `쇼트서킷` 이라 한다.

- 또한 필터 - 맵 두개의 중간연산자가 하나로 병합된 것을 `루프 퓨전` 이라 한다.
