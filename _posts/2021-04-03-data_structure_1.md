---
title:  "String Immutability"
excerpt: "immutable"

categories:
  - Java
tags:
  - String
last_modified_at: 2021-04-04T16:06:00-05:00
---

# Immutability

- 임뮤타블 이란 불변. 즉, 한 번 생성될 때 그 값은 더이상 변하지 않는다는 의미.

- 대표적인 예로 String 객체가 있다. (+ Integer, Boolean 등)

- String 객체는 한번 생성되면 객체의 값이 변하지 않는다.

- 하지만 우리는 String 타입의 변수를 바꾸면서 사용하는데 사실 이는 바꿀때마다 새로운 객체를 생성하는 것이다.

- 즉 불변객체는 생성할 때 heap 영역에 값을 생성하고, stack 영역의 변수에 그 객체를 참조하도록 하는것이다.


```java
String str = "string";
str = "string is immuable"; // String 객체가 새로 생성 됨. str 은 새로 생성된 객체를 참조함.
str = str + "!"; // concat 또한 새로운 String 객체를 생성 함.
```

- 장점 : 한번 생성되면 값이 변하지 않기에 신뢰성 있다 -> 멀티스레드 환경에서 Thread safe 하며 동기화 처리가 필요없다.

- 단점 : 새로운 객체를 계속 생성해야 하기에 성능저하와 메모리 누수가 발생할 수 있음.

# 불변 객체 만들기

- 멤버 변수가 모두 원시 타입이라면 멤버 변수를 final 로 하고 setter 메소드를 구현하지 않는것으로 불변 객체 생성 가능.

- 멤버 변수에 참조 타입(객체)이 있다면 참조 변수의 객체 도한 불변 객체여야 한다.

- 멤버 변수에 참조 타입(List, Array 등)이 있다면 생성자 메소드에서 특별한 처리를 해주어야 한다.

```java
public class ArrayObject {

    private final int[] array;

    public ArrayObject(final int[] array) {
        this.array = Arrays.copyOf(array,array.length); // 참조 타입을 바로 할당하지 않고 copyOf 메소드로 할당
    }


    public int[] getArray() {
        return (array == null) ? null : array.clone();
    }
}

public class ListObject {

    private final List<Animal> animals;

    public ListObject(final List<Animal> animals) {
        this.animals = new ArrayList<>(animals); // 새로운 객체를 생성하여 할당
    }

    public List<Animal> getAnimals() {
        return Collections.unmodifiableList(animals);
    }
}
```

# 결론

- 불변객체는 한번 할당하면 필드 값을 변경할 수 없는 객체.

- 하지만 재할당은 가능.

- 필드가 원시 타입일 경우엔 final 사용으로 불변객체를 만들 수 있고, 참조 타입일 경우엔 추가적인 작업이 필요함.

- 그 값이 변경되지 않기에 신뢰성이 있어 멀티 스레드 환경에서 Thread safe 하며, 단점으로는 메모리 누수와 성능저하가 발생할 수 있다.