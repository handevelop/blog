---
title:  "모던 자바 인 액션 스터디 1장"
excerpt: "자바 8,9,10,11 : 무슨 일이 일어나고 있는가?"

categories:
  - Java
tags:
  - Java
last_modified_at: 2021-03-23T00:00:00-05:00
---

모던 자바 인 액션을 읽으며 공부하고 기억이 가물 가물해지면 
나중에 다시 보고 싶은 내용들을 정리했다.

# 스트림 처리

스트림 : 한 번에 한 개씩 만들어지는 연속적인 데이터 항목들의 모임

스트림 API 의 등장으로 기존에는 한 번에 한 항목을 처리 했지만
자바8 에서는 일련의 스트림으로 만들어 처리 할 수 있다.
또한 스트림 파이프라인을 이용하여 여러 CPU 코어에 쉽게 할당할 수 있다.
즉, 스레드라는 복잡한 작업을 사용하지 않으면서도 공짜로 병렬성을 얻을 수 있다.


# 동적 파라미터화로 메서드에 코드 전달

동적 파라미터화 : 메서드를 다른 메서드의 인수로 넘겨주는 기능

기존에는 Comparator 객체를 만들어서 sort 에 넘겨주는 방식을 사용했다면,
자바8 에서는 메서드 자체를 sort 의 인수로 전달 할 수 있다.


# 병렬성과 공유 가변 데이터

다른 코드와 동시에 실행 하더라도 안전하게 실행할 수 있는 코드를 만들려면
공유 가변 데이터에 접근하지 않아야 한다.
기존에는 시스템 성능에 악영향을 미치는 synchronized 를 사용하며 
병렬이라는 목적을 무력화시키면서 공유 가변 데이터를 보호했다면,
자바8 스트림을 이용하면 스레드 API 보다 쉽게 병렬성을 활용할 수 있다.


# 일급시민이 된 자바 함수

일급시민 : 값의 구조를 표현하는 구조체이자 자유롭게 전달할 수 있는 구조체.


```java
// 현재 디렉터리(".") 에서 숨겨진(isHidden) 파일들을 찾는 코드 1
File[] hiddenFiles = new File(".").listFiles(new FileFilter() {
  public boolean accept(File file) {
    return file.isHidden();
  }
})
```

new 로 객체 참조를 생성하는 객체 참조를 이용해 숨겨진 파일을 찾는 코드이다.
FileFilter 를 인스턴스화 하는 과정으로 복잡해보인다.

```java
// 현재 디렉터리(".") 에서 숨겨진(isHidden) 파일들을 찾는 코드 2
File[] hiddenFiles = new File(".").listFiles(File::isHidden);
```

File 의 isHidden 함수를 값으로 사용하라는 File::isHidden 과 같은
메소드 참조의 등장으로 코드가 짧아지고 가독성도 높아졌다.

