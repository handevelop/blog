---
title:  "JAVA final 키워드"
excerpt: "다시 돌아보기"

categories:
  - Java
  - 스터디
tags:
  - Java
last_modified_at: 2021-05-17T08:22:00-05:00
---



# final 키워드

- `final` 키워드를 떠올릴 때면 그냥 상수로만 생각할 때가 종종 있다 

- 하지만 `final` 을 클래스, 메소드, 변수에 선언하면 그에 맞게 제한되는 것이 다르다.


## final 클래스

- 상속을 제한 시켜 다른 클래스에서 상속을 할 수 없는 클래스가 된다.

```java
public final class Animal {
}
// 컴파일 에러
public class Dog extends Animal {
}
```


## final 메소드

- 상속받은 클래스의 메소드에 `final` 키워드가 붙어 있다면 해당 메소드는 오버라이딩 할 수 없다. 재 정의 할 수 없게 만들어 부모의 메소드를 그대로 사용하게 하게끔 만들때 사용한다.

```java
public class Animal {
  public final void cry() {
    log.info("WOW");
  }
}
// 컴파일 에러
public class Dog extends Animal {

  // 컴파일 에러
  // @Override
  // public final void cry() {
  // }
}
```


## final 변수

- 크게 4 군데에 `final` 을 붙일 수 있다. 프리미티브 타입, 객체 타입, 멤버 변수, 메소드의 인자.

- 프리미티브 타입 : `int, long` 같은 프리미티브 타입에 `final` 을 붙이면 상수가 된다.

- 객체 타입 : 객체 타입에 `final` 을 붙이면 이 변수에 다른 타입을 참조 할 수 없다. 단, `immutable` 객체는 아니고 객체의 속성들은 바꿀 수 있다.

```java
final String str = "final String";
final Animal animal = new Animal();
//animal = new Animal(); // 컴파일 에러. Cannot assign a value to final variable 'animal'
animal.setAge(10); // immutable 하지 않기 때문에 객체의 속성은 바꿀 수 있다.
```

- 멤버 변수 : 멤버 변수에 `final` 을 붙이면 생성자 메소드가 끝나기 전에 상수로 초기화 된다. 단 `static` 키워드의 존재 유무에 따라 초기화 시점이 달라진다.

- `인스턴스 final 변수` : 객체 생성 시마다 초기화 됨.

- `static final 변수` : 클래스 로드 시에 한번만 초기화 됨.

- 메소드의 인자 : 메소드의 인자에 `final` 을 붙이면 메소드 블록 안에서 더이상 인자의 값을 변경 할 수 없다.

