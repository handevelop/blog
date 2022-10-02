---
title:  "자바는 call by value 이다"
excerpt: ""

categories:
  - java
last_modified_at: 2022-09-23T11:06:00-05:00
---


# call by value / reference

![image](https://user-images.githubusercontent.com/25449640/193463575-3d14ef70-80ec-490c-9f28-c2288ed10fa6.png)
- JVM 에 변수들이 저장되는 위치는 위와 같다. 원시 타입 변수는 변수와 그 값이 stack 에 저장되고, 참조 타입 변수는 stack 에 heap 의 주소값을 갖고 있고 값은 heap 영역에 저장된다.
- 사람들이 가끔 자바를 call by reference 로 착각하는데, 리스트 같은 참조 타입 변수들을 void 를 반환하는 메서드의 인자로 넘기고 그 메서드에서 참조 타입 변수를 조작하면 이전 scope 의 참조 타입 변수의 값이 수정되어 있기 때문이다.
- 이는 다음 코드로 왜 자바가 call by value 로 동작하는지 설명 할 수 있다.

```java
    public static void main(String[] args) {
        User a = new User(10);
        User b = new User(20);

        System.out.println("a : " +a);
        System.out.println("b : " +b);
        System.out.println("a : " +a.age);
        System.out.println("b : " +b.age);


        modify(a, b);

        System.out.println("a : " +a);
        System.out.println("b : " +b);
        System.out.println("a : " +a.age);
        System.out.println("b : " +b.age);

    }
    static class User {
        public int age;

        public User(int age) {
            this.age = age;
        }
    }
    private static void modify(User a, User b) {
        // a, b 와 이름이 같고 같은 객체를 바라본다.
        // 하지만 test 에 있는 변수와 확실히 다른 변수다.
        System.out.println("a in mod : " +a);
        System.out.println("b in mod : " +b);
        // modify 의 a 와 test 의 a 는 같은 객체를 바라봐서 영향이 있음
        a.age++;

        // b 에 새로운 객체를 할당하면 가리키는 객체가 달라지고 원본에는 영향 없음
        b = new User(30);
        b.age++;

        System.out.println("a : " +a.age);
        System.out.println("b : " +b.age);
    }
```


# 참고

- https://bcp0109.tistory.com/360
