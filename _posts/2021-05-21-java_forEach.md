---
title:  "JAVA forEach 차이"
excerpt: "자바8 이전과 이후"

categories:
  - Java
  - 스터디
tags:
  - Java
last_modified_at: 2021-05-21T08:22:00-05:00
---



# for each(enhanced for loop), Collection.forEach(), Collection.stream().forEach() 차이


```java
        List<String> list = new ArrayList<> ();

        for(int i = 0; i < 11; i++) {
            list.add (Integer.toString (i));
        }

        // enhanced for loop
        for (String str : list) {
            log.info(str);
        }
        // Collection.forEach()
        list.forEach (str -> log.info (str));

        // Collection.stream().forEach()
        list.stream ().forEach (str -> log.info (str));

```

- enhanced for loop : old school for 와 다르게 아래처럼 외부의 `Iterator` 를 이용한다.

```java
// enhanced for loop
for (String str : list) {
  log.info(str);
}
// 사실은 아래와 같이 동작하는 것이다.
Iterator<String> iter = list.iterator();
while (iter.hasNext()) {
  log.info(iter.next());
}
```

- Collection.forEach() : `forEach()` 메소드는 `Iterable` 인터페이스의 디폴트 메소드를 이용한 것이다. `Collection` 인터페이스에서 `Iterable` 인터페이스를 상속받고 있기 때문에 바로 호출 할 수 있다. 또한 객체 생성없이 내부 반복자를 이용한다. 순서를 보장한다. 또한 `Thread-safe` 하다.

```java
// 다음은 Iterable 인터페이스의 일부분
// 내부 반복자가 있다.
Iterator<T> iterator();
...
// Iterable 의 디폴트 메소드
default void forEach(Consumer<? super T> action) {
  Objects.requireNonNull(action);
  for (T t : this) {
    action.accept(t);
  }
}

//다음은 Collection 인터페이스의 일부분
// forEach 메소드가 동기화 처리 되어 있어 쓰레드 세이프하다.
public interface Collection<E> extends Iterable<E> {
        // Override default methods in Collection
        @Override
        public void forEach(Consumer<? super E> consumer) {
            synchronized (mutex) {c.forEach(consumer);}
        }
}

```

- Collection.stream().forEach() : 순서가 보장되지 않을 수 있다. `parallelStream` 에서는 `순서가 항상 보장되지 않는다.` `Collection` 인터페이스의 디폴트 메소드인 `stream()` 을 이용하여 Stream 객체를 생성해야만 `forEach()` 메소드를 호출 할 수 있다. stream().forEach() 는 Stream 객체를 생성하고 버리는 오버헤드가 있기에 단순 반복일 때에는 사용을 지양하며, `map, filter` 등을 사용하여 스트림의 기능을 활용할 때에만 stream().forEach() 를 사용하는 것이 좋다.

- 또한 구현할 로직에 따라 강제 종료 로직이 있을 수도 있는데 이 때 `스트림은 강제 종료하는 방법이 없기에 비효율` 적이다.

- 이 외에도 Collection.forEach() 는 `synchronized` 로 되어 있어 쓰레드 세이프 한 반면 stream().forEach() 는 일관성 없는 동작이 발생할 수 있다.

```java
//다음은 Collection 인터페이스의 일부분
public interface Collection<E> extends Iterable<E> {
  ...
  default Stream<E> stream() {
    return StreamSupport.stream(spliterator, false);
  }
}

// Collection.parallelStream().forEach()
// 순서가 보장되지 않는다.
list.parallelStream ().forEach (str -> log.info (str)); // 4,8,9,5,3,1,7,6,2 출력

// Collection.forEach() 는 동시성이 위배되면 java.util.ConcurrentModificationException 가 발생하는데
// stream().forEach() 는 동시성이 위배되어도 끝까지 스트림이 수행되며 NullPointerException 를 던진다.
List<Integer> nums = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5, 6));
Consumer<Integer> removeIfEven = num -> {
  log.info (String.valueOf (num));
  if (num % 2 == 0) {
    nums.remove(num);
  }
};

//nums.forEach (removeIfEven); // ConcurrentModificationException

nums.stream ().forEach (removeIfEven); // NullPointerException
```