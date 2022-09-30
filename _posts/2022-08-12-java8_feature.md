---
title:  "Java8 추가된 기능"
excerpt: ""

categories:
  - java
last_modified_at: 2022-08-12T15:06:00-05:00
---


# 자바8 에 추가된 기능 간단하게 훑어보자
- 자바 8의 새로운 기능들 중, 중요하다고 생각되는 몇 가지들
  - 람다식과 함수형 인터페이스
  - 스트림 API
  - 인터페이스의 default & static 메서드
  - Optional 클래스



# 람다식과 함수형 인터페이스

- 람다식은 메서드를 하나의 식으로 표현하는 방법이다.

```java
// before java8
int[] arr = new int[5];

for (int element : arr) {
    element = (int) (Math.random() * 5 + 1);
}

// after java8
int[] arr = new int[5];
Arrays.setAll(arr, (x) -> (int) (Math.random() * 5 + 1));

```     
- 이처럼 코드를 간결하게 작성가능하게 하고, 메서드를 함수의 인자로 던질 수 있게 되었다.
- 또한 람다를 보다 쉽게 사용하게끔 메서드 레퍼런스(참조) 기능도 제공하는데, 람다식이 하나의 메서드 만을 호출할 경우에 가능하다.
- 아래와 같은 표현방법을 본적이 있을 것이다.

```java
List<Integer> list = Arrays.asList(1, 2, 3);

list.stream().map(a -> new ArrayList<String>(a));
list.stream().map(ArrayList::new); // 이 둘은 동일한 표현이다.
```

```java

// Function 인터페이스
@FunctionalInterface
public interface Function<T, R> {

    /**
     * Applies this function to the given argument.
     *
     * @param t the function argument
     * @return the function result
     */
    R apply(T t);
    // . . .
}

// Consumer 인터페이스
@FunctionalInterface
public interface Consumer<T> {

    /**
     * Performs this operation on the given argument.
     *
     * @param t the input argument
     */
    void accept(T t);
    // . . . 
}

```

- 위와 같은 애노테이션을 본적이 있을 것이다. 바로 함수형 인터페이스라고 알려주는 애노테이션이다.
- 애노테이션이 없더라도 추상 메서드가 1개만 정의되어 있는 인터페이스는 함수형 인터페이스로 인식한다.


```java
// before java8
consumer = new Consumer<String>() {
    @Override
    public void accept(String a) {
        System.out.println(a);
    }
};

// after java8
Consumer<String> consumer;
consumer = a -> System.out.println(a);
consumer = (String a) -> System.out.println(a);
consumer = (a) -> {
    System.out.println(a);
};
```
- 자바8 이후에는 익명 클래스의 객체로 람다식으로 표현이 가능하다.
- 자바 진영에서는 개발자들의 함수형 인터페이스 경험을 위해 java.util.function 패키지 내에 있는 다양한 표준 함수형 인터페이스를 제공하고 있다. (Function 과 Consumer 도 저 패키지 아래에 있다.)


# 스트림 API
- 컬렉션, 배열등을 처리할 때 스트림 없이 어떻게 처리를 했지? 라는 생각이 들 정도로 자바 개발의 패러다임을 바꾼 기능 중 하나이다.

```java
// before java8
for (String a : list) {
    System.out.println(a);
}

// after java8
list.stream().forEach(a -> System.out.println(a));
```

- 아래와 같이 다양한 기능을 제공한다.

```java
List<String> list = Arrays.asList("abc", "bac", "ccc", "ccc");
boolean isExist = list.stream().anyMatch(element -> element.contains("a")); // a를 포함한 문자열이 있는지의 여부를 반환
Stream<String> stringStream = list.stream().filter(element -> element.startsWith("a"));// a로 시작하는 문자열만 필터링
Stream<Integer> integerStream = list.stream().map(element -> element.length()); // 문자열의 길이로 이루어진 스트림으로 변환
Stream<String> distinctStream = list.stream().distinct(); // 리스트 내의 중복 제거
list.parallelStream().forEach(System.out::println);  // 멀티쓰레드를 이용한 병렬 처리. 메서드 참조도 사용
```

- 또한 지연된 연산이라는 특징이 있는데 이는 중간연산때에는 아무것도 실행되지 않으며, 최종연산 때에만 호출되는 특징으로 인해 쓸모 없는 객체 생성을 줄여준다.

```java
// before java8
List<String> list = Arrays.asList("abc", "bac", "ccc", "ccc");

List<String> result = new ArrayList<>(new LinkedHashSet<>(list));

// 매 연산이 실행될때마다 새로운 객체가 생성됨
for (int i = 5; i < result.size(); i++) {
    result.remove(i);
}

result.sort(Comparator.naturalOrder());

// after java8
List<String> list = Arrays.asList("abc", "bac", "ccc", "ccc");
List<String> result = list.stream().distinct().limit(5).sorted().collect(Collectors.toList());

```


# 인터페이스의 디폴트&스태틱 메서드

- 자바8 이전에는 인터페이스에 정의된 모든 추상 메서드를 구현부에서 구현해주어야 했다. 인터페이스에 새로운 메소드가 추가된다면 그 추가된 수 만큼 구현 클래스들은 강제적으로 구현을 해야하는 사태가 벌어졌었다.
- 자바8 이후부터는 디폴트 메서드와 스태틱 메서드를 인터페이스에 구현 가능하게 되어, 하위호환이 쉽게 가능해졌다.
- 단, 인터페이스는 다중 상속이 가능하기 때문에 아래와 같이 add() 디폴트 메서드가 TestInterface 뿐 아니라 TestInterface2 에도 있고, 구현 클래스에서 TestInterface 와 TestInterface2 를 구현한다면 add 메서드를 오버라이드하여 `TestInterface.super.add()` 이런식으로 어떤 인터페이스의 디폴트 메서드를 사용할지 알려주어야 컴파일 된다.
- 디폴트 메서드 이외에도 스태틱 메서드를 인터페이스에서 지원하는데, 이로인해 `TestInterface.minus(100, 50);` 와 같이 인터페이스의 스태틱 메서드를 활용할 수 있게 되었다.

```java
public interface TestInterface {

    // method 와 multiply 는 abstract method 로서 구현부에서 오버라이드하여 구현해주어야 한다.
    public String method();

    public int multiply(int a, int b);

    // 디폴트 메서드는 기본적으로 public 이다.
    default int add(int a, int b) {
        return a + b;
    }

    public static int minus(int a, int b) {
        return a - b;
    }
}

public class TestClass implements TestInterface{

    @Override
    public String method() {
        return "method";
    }

    @Override
    public int multiply(int a, int b) {
        return a * b;
    }
}
```

- OOP 적인 관점에서 봤을때 이 인터페이스는 조금 혼란스러워 보이지만, 기존 코드들과 하위호환이 가능하고 유지보수가 쉬워지게 하는 장점이 있는것 같다.



# 옵셔널 클래스
- 개발자들에게 지긋지긋했던 `NPE` 를 조금이나마 해방시키는 녀석이다.
- 스트림과 결합하여 썼을때 보일러플레이트 코드를 확연히 줄여준다.
- 아래와 같은 코드에 사용하면 쾌감이 느껴진다.

```java
// before java8
User user = getUser();
if (user != null) {
    Address address = user.getAddress();
    if (address != null) {
        String street = address.getStreet();
        if (street != null) {
            return street;
        }
    }
}
return "not specified";

// after java8
Optional<User> user = Optional.ofNullable(getUser());
String result = user
  .map(User::getAddress)
  .map(Address::getStreet)
  .orElse("not specified");
```