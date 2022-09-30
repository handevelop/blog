---
title:  "stream findAny() VS findFirst()"
excerpt: ""

categories:
  - java
last_modified_at: 2022-08-12T15:06:00-05:00
---


# Stream API 중 findAny() 와 findFirst() 의 차이점
- 언뜻보기에 두 API 는 동일한 결과를 보여주는 것처럼 보인다.
- 하지만 findAny 는 이름에서 보이듯이 스트림 내의 모든 요소들을 찾을 수 있다.
- 반면에 findFirst 는 스트림 내에서 첫번째 찾은 요소를 반환한다.
- 둘의 공통점은 둘 다 스트림에서 찾을 요소가 없을 때 옵셔널 인스턴스를 반환한다는 점이다.



# findAny()

- findFirst() 와 동일한 요솔르 반환할 가능성이 높다.
- 하지만 무조건 보장하지는 않는다.
- 이는 병렬 처리시에 확연히 드러난다.

```java
@Test
public void createParallelStream_whenFindAnyResultIsPresent_thenCorrect()() {
    List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);
    Optional<Integer> result = list
      .stream().parallel()
      .filter(num -> num < 4).findAny();

    assertTrue(result.isPresent());
    assertThat(result.get(), anyOf(is(1), is(2), is(3)));
}

```     

- 1, 2, 3 중에 랜덤으로 결과가 반환 된다.


# findFirst()

```java
@Test
public void createStream_whenFindFirstResultIsPresent_thenCorrect() {

    List<String> list = Arrays.asList("A", "B", "C", "D");

    Optional<String> result = list.stream().findFirst();

    assertTrue(result.isPresent());
    assertThat(result.get(), is("A"));
}
```

- 시퀀스의 첫번째 요소만 반환하기에 무조건 A 만 반환된다.

 ref.https://www.baeldung.com/java-stream-findfirst-vs-findany