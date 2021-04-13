---
title:  "다이나믹 프로그래밍"
excerpt: "DP 4번 문제"

categories:
  - Algorithm
tags:
  - Java
last_modified_at: 2021-04-11T08:06:00-05:00
---

```java
LIS 란?

Longest Increasing SubSequence 의 약자로 가장 긴 증가하는 부분수열이다.
예를 들어 {4,2,5,8,4,11,15} 수열이 있을 때 LIS 는
{4,5,8,11,15} 이다
전형적인 DP 의 아이디어로 문제를 해결할 수 있다.

수열의 각 인덱스를 i 로, i 보다 작은 인덱스의 값들을 j 로 할 때
arr[i] 와 arr[j] 를 비교하여 arr[i] 가 더 크다면
max(dp[i], dp[j]+1) 을 dp[i] 에 넣으면 된다.
즉 점화식은 arr[j] < arr[i] 일 때, dp[i] = max(dp[i], dp[j] + 1) 이다.
dp[i] 는 i 까지의 부분수열 최대 길이를 구할 수 있다.

LIS 를 이용하면 다음 문제를 해결할 수 있다.

4번 문제

N = 7 일 때
15, 11, 4, 8, 5, 2, 4 이 배열의 요소 있을 때
15, 11, 8, 5, 4 로 정렬되어 제거 할 총 요소의 개수인 2 가 리턴 되도록 해야 한다.
```

```java
4번 문제 해답

입력받은 배열을 뒤집은 다음 LIS 알고리즘을 적용한다.

int solution(int[] arr) {
  List<Integer> list = Arrays.asList(arr);
  int n = arr.length;
  int[] dp = new int[2000];

  for(int i = 0; i < n; i++) {
    dp[i] = 1;
  }
  for(int i = 1; i < n; i++) {
    for(int j = 0; j < i; j++) {
      if(list.get(j) < list.get(i)) {
        dp[i] = Math.max(dp[i], dp[j] + 1);
      }
    }
  }
  int max = 0;
  for(int i = 0; i < n; i++) {
    if(max < dp[i]) {
      max = dp[i];
    }
  }
  return n - max;
}

```
