---
title:  "List 컬렉션"
excerpt: "ArrayList 와 LinkedList 차이 (feat.Array)"

categories:
  - Java
tags:
  - 자료구조
last_modified_at: 2021-04-03T13:06:00-05:00
---

# Array

- 하나의 변수에 여러 값을 저장하는데 쓰이는 정적 리스트.

- static list 이기 때문에 한번 생성된 배열의 길이는 변하지 않음.

- 논리적 저장 순서와 물리적 저장 순서가 일치함.

- 따라서 인덱스로 해당 원소에 바로 접근이 가능함. Big-O(1) 로 원소 접근.

- 단점으로는 삭제 또는 삽입 과정에는 shitf 를 해야하는 오버헤드가 생김. 최악의 시간복잡도는 Big-O(n).

- 배열의 크기가 변동이 없으며 삭제, 삽입 명령을 사용하지 않을때 사용하는 자료구조.


```java
int[] intArray; // 배열 선언. 메모리에 공간은 만들지 않음.
int[] num = new int[10]; // 메모리에 공간만듬.
```


# ArrayList

- List 인터페이스의 구현 클래스.

- ArrayList 는 Array 처럼 인덱스로 객체를 관리한다는 공통점이 있음.

- 또한 Array 처럼 삭제, 삽입 시 shift 오버헤드 발생.

- 하지만 Array 와 다르게 static list 가 아닌, 동적으로 크기를 늘릴 수 있는 자료구조.

- 처음 설정한 저장 공간이 있고, 설정한 저장 공간 보다 더 많은 객체가 들어오면 자동적으로 저장 공간이 늘어나게 되어있다.

- 디폴트 사이즈는 10 이며 공간은 두배씩 증가한다.

- 객체 검색, 맨 마지막 인덱스에 객체 추가에 좋은 성능을 발휘함.


```java
List<String> list = new ArrayList<>();
List<String> list100 = new ArrayList<>(100); // 크기 100
List badList = new ArrayList(); // 생성은 가능. 타입변환을 하므로 성능 안좋음.
```


# LinkedList

- List 인터페이스의 구현 클래스.

- ArrayList 와는 다른 내부구조를 갖고 있음.

- 인덱스로 관리하지 않고 인접 참조를 링크해서 체인처럼 관리.

- Array 에서 삭제, 삽입 시 생기는 오버헤드를 해결하기 위한 자료구조.

- 각 원소들은 자기 자신 다음 원소가 올지 기억하고 있다.

- 따라서 shift 없이 이 부분만 다른 값으로 바꾸는 것으로 삭제, 삽입을 Big-O(1) 으로 가능하다.

- 하지만 Array 와 달리 논리적 저장 순서와 물리적 저장 순서가 일치하지 않음

- 따라서 삭제, 삽입을 위해 원소를 찾기위해 Big-O(n) 의 시간 복잡도가 추가적으로 발생.

- 하지만 shift 오버헤드가 없다는 것만으로 삭제, 삽입하는 로직에서 좋은 성능을 보여줌.

- 객체 삭제, 삽입에 좋은 성능을 발휘함.


```java
List<String> list = new LinkedList<>(); // LinkedList 는 List 구현 클래스
```


# Vector

- List 인터페이스의 구현 클래스.

- ArrayList 와 동일한 내부 구조를 갖고 있음.

- 다만 Vector 객체를 생성하기 위해서는 타입을 지정해야 함.

- 가장 큰 차이점은 Vector 클래스는 동기화 되어있다.

- 따라서 멀티 스레드 환경에서 안전하게 객체를 추가, 삭제 가능.

- Thread Safe 하기 때문에 ArrayList 보다 추가, 삭제 과정이 느림.


```java
List<String> vector = new Vector<>(); // 생성 시 타입을 지정해야 함
```