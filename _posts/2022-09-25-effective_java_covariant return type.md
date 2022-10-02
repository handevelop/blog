---
title:  "이펙티브 자바 - covariant return type"
excerpt: ""

categories:
  - java
  - 스터디
last_modified_at: 2022-09-25T22:00:00-05:00
---


# covariant return type

- 자바 1.5 부터 추가된 기능으로 오버라이딩 메서드가 이름, 매개변수, 리턴타입이 같아야 했던것을 리턴타입이 서브 클래스가 되어도 오버라이딩 가능하게 되었다.
- 메서드 오버라이딩을 할 때 반환 타입을 더 좁게 할 수 있다.
- 직접 코드로 보자.

```java
class A {

}
class B extends A {

}

class Foo {
    public A get() {
        return new A();
    }
}
class Bar extends Foo {
    @Override
    public B get() {
        return new B(); // B 가 A 를 상속받고 있기 때문에 B 로 반환해도 A 가 할 수 있는것을 할 수 있기 때문에 상관 없다.
    }
}
```

# 참조

- https://www.javatpoint.com/covariant-return-type
