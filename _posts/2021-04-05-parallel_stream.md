---
title:  "Java 페러렐 스트림"
excerpt: "parallel stream"

categories:
  - Java
  - Stream
tags:
  - Java
last_modified_at: 2021-04-05T16:06:00-05:00
---

# Parallel Stream ?

- Java8 에서의 최대 변경사항은 람다와 스트림이라고 할 수 있는데 스트림이란 뭘까? 그리고 페러렐 스트림이란?
- 스트림. 스트림 인터페이스란 컬렉션을 파이프 식으로 처리하면서 그 구조를 추상화 하는것을 말한다.
- 페러렐 스트림은 스트림 인터페이스를 이용하여 병렬 연산을 아주 쉽게 처리해주는 메서드이다.


# 기존의 병렬 처리 방식

```java
ExecutorService executor = Executors.newFixedThreadPool(5);
for (int i = 0; i < dealmaxList.size(); i++) {
	final int index = i;
    executor.submit(() -> {
		Thread.sleep(5000);
		System.out.println(Thread.currentThread().getName() 
			+ ", index=" + index + ", ended at " + new Date()); 	 
    });
}       
executor.shutdown();
```


# Parallel Stream 을 이용한 병렬 처리 방식

```java
ForkJoinPool forkjoinPool = new ForkJoinPool(5);
forkjoinPool.submit(() -> {
	dealmaxList.parallelStream().forEach(index -> {
		System.out.printIn("Thread : " + Thread.currentThread().getName()
             + ", index + ", " + new Date());
		try{
			Thread.sleep(5000);
		} catch (InterruptedException e){
		}
	});
}).get();
```

- executor 와 for 문을 이용할 필요도 없이 바로 dealmaxList 에 parallelStream() 으로 병렬처리가 가능하다 !
- 또한 ForkJoinPool 을 이용하여 몇개의 스레드로 병렬처리 할것인지 쉽게 사용할 수 있다. 
- ForkJoinPool 을 사용하지 않고 페러렐 스트림을 사용한다면 디폴트로 1프로세서 당 1개의 스레드를 사용한다.

# 유의할 점

- Parallel Stream 은 작업을 분할하기 위해 Spliterator의 trySplit()을 사용하게 되는데 이 분할되는 작업의 단위가 균등하게 나누어져야 하며 나누어지는 작업에 대한 비용이 높지 않아야 순차적 방식보다 효율적임. 
- array, arrayList와 같이 정확한 전체 사이즈를 알 수 있는 경우에는 분할 처리가 빠르고 비용이 적게 들지만 linkedList의 경우라면 별다른 효과를 찾기가 어렵다.
- 병렬로 처리되는 작업이 독립적이지 않고 동기화 되어 있다면 수행 성능에 영향이 있음.

# 언제 사용할까?

- Parallel Stream 은 앞서 설명한 ForkJoinPool 방식을 이용하기 때문에 분할이 잘 이루어질 수 있는 데이터 구조이면서 작업이 독립적이면서 CPU사용이 높은 작업에 적합하다고 볼 수 있음.