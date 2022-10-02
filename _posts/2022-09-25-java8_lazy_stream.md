---
title:  "Java8 의 lazy evaluation 과 Stream"
excerpt: ""

categories:
  - java
  - 스터디
last_modified_at: 2022-09-25T15:00:00-05:00
---


# Lazy evaluation 란?

- 느린 평가란 실제로 필요해진 경우 연산을 시작하는 것을 말한다.
- 자바의 기조는 느린 평가의 반대인 Eager evaluation(조급한 평가) 이다. 물론 일부 느린 평가도 있었다.
- 자바8 이후 느린 평가를 더욱 유연하게 사용할 수 있게 되었다.


# 자바7의 즉시 평가

```java
static boolean compute(String str) {
  System.out.println("executing...");
  try {
    Thread.sleep(1000);
  } catch (InterruptedException ignore) {
  }
  return str.contains("a");
}
```

- 위와 같이 1초 sleep 후 입력받은 파라미터가 a 를 포함하는지 여부를 반환하는 메서드가 있다.

```java
public void solution_1() {
    boolean b1 = compute("Hello_1");
    boolean b2 = compute("Hello_2");
    eagerMatch(b1, b2);
}

static String eagerMatch(boolean b1, boolean b2) {
    return b1 && b2 ? "match" : "incompatible!";
}   
```

- 위 코드는 반드시 2초 이상이 걸릴 것이다. Eager evaluation 하는 자바의 특성상 b1, b2 는 그 값을 갖고 있기 때문이다.


# 자바8의 느린 평가

```java
public void solution_2() {
    Supplier<Boolean> a = () -> compute("Hello_1");
    Supplier<Boolean> b = () -> compute("Hello_2"); 
    lazyMatch(a, b);
}

static String lazyMatch(Supplier<Boolean> a, Supplier<Boolean> b) {
    return a.get() && b.get() ? "match" : "incompatible!";
}
```

- 함수형 인터페이스 `Supplier<T>` 를 활용(파라미터를 입력받지 않고 객체 T 를 만드는 인터페이스)해서 a, b 객체를 만들었다.
- 그러나 이 객체에서 compute() 는 즉시 실행되지 않고 a.get() 을 하는 시점에 실행된다. 
- 그러므로 이 코드는 반드시 2초 이상 걸리는 코드가 아니라 a.get() 이 false 인 경우에 b 의 compute() 는 수행이 되지 않기에 1초 이상 2초 이하의 시간이 걸릴 것이다.
- 필요하지 않은 연산은 하지 않는 것, 이를 Lazy Evaluation 라 한다.


# 스트림과 Lazy Evaluation

```java
        final List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        System.out.println(list.stream()
                               .filter(i -> {
                                   System.out.println("i < 6");
                                   return i < 6;
                               })
                               .filter(i -> {
                                   System.out.println("i%2 == 0");
                                   return i % 2 == 0;
                               })
                               .map(i -> {
                                   System.out.println("i = i*10");
                                   return i * 10;
                               })
                               .collect(Collectors.toList()));
```

- 간단히 스트림을 활용해서 스트림의 동작하는 모습을 볼 수 있다.
- 첫번째 filter `i < 6` 이하인 녀석들만 다음 연산인 filter `i % 2 == 0` 연산을 하고 (i % 2 == 0 를 안하면 당연히 i * 10 도 안함) `i % 2 == 0` 를 통과한 녀석들만 `i * 10` 연산을 한다.
- 이렇게 stream 은 Lazy evaluation 로 인해 불필요한 연산을 피하는 특징을 지니고 있다.


# 참조

- https://www.codemotion.com/magazine/backend/lazy-java/