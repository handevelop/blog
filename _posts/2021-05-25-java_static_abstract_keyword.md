---
title:  "JAVA static, abstract 키워드"
excerpt: ""

categories:
  - Java
  - 스터디
tags:
  - Java
last_modified_at: 2021-05-25T08:22:00-05:00
---


# final

- 앞서 `final` 키워드에 대해 짧게 공부했었다. 

- 변수에 쓰이면 더이상 바뀌지 않는 변수로 선언.

- 메소드에 쓰이면 자식 클래스가 오버라이딩 할 수 없도록 선언.

- 클래스에 쓰이면 상속받지 못하도록 선언.


# static

- 근데 `static` 키워드를 통해서도 자식 클래스가 오버라이딩 못하도록 선언 할 수 있다.

- `final` 과 `static` 의 차이는??

- `동적 바인딩` 과 `정적 바인딩` 의 차이이다. 동적 바인딩은 실행 시에 바인딩 되고 정적 바인딩은 객체가 생성되기도 전에 올라간다.

- 결론 부터 말하자면 `static` 으로 선언한 메소드는 클래스 로더가 초기화 작업 중 runtime data area 의 메소드 영역에 미리 올려 놓기 때문에 객체 생성 없이 외부 클래스에서 사용이 가능하다는 차이점이 있다.

- 반면에 `final` 메소드는 자식 클래스에서 오버라이딩만 제한을 할 뿐 객체 생성없이 사용이 불가능하다.

## 메소드 영역

- 클래스 영역, 스태틱 영역, 메소드 영역 다양하게 불리운다. 대체적으로 메소드 영역 혹은 스태틱 영역으로 부르고 있다.

- `static` 으로 선언된 변수, 메소드, 클래스의 멤버변수명, 데이터 타입, 리턴 타입, 상수 등이 저장된다.

- `heap` 영역과 함께 런타임 데이터 에어리어 중 공유되는 자원이다.


# abstract

- `abstract class` : 해당 클래스는 인스턴스화 할 수 없다. 즉 객체로 만들 수 없다. 해당 클래스를 누군가가 상속받아 구현해야만 하는 클래스이다. 해당 클래스의 메소드 중에 하나라도 abstract 키워드가 있다면 클래스 또한 반드시 abstract class 로 선언되어야 한다.

- `abstract method` : 해당 메소드는 구현부가 존재하면 안되며 자식 클래스가 해당 메소드를 구현해야 한다. 자식 클래스에게 구현을 강제하기 위해 사용한다.

```java
abstract class Parent {
  private int money = 100_000_000;

  public void eat() {
    log.info("eat something");
    money =- 100;
  }

  public abstract void study();
}

class Child extends Parent {
  Child() {
    super(); // Parent 객체 생성
  }

  @Override
  public void study() {
    log.info("스터디");
  }
}

Parent p = new Parent(); // 에러 !!
Parent child = new Child();

child.eat(); // Parent 의 eat() 호출
child.study(); // Childe 의 study() 호출
```
