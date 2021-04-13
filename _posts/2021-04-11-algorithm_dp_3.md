---
title:  "다이나믹 프로그래밍"
excerpt: "DP 3번 문제"

categories:
  - Algorithm
tags:
  - Java
last_modified_at: 2021-04-11T08:06:00-05:00
---

```java
3번 문제

n x m 크기의 금광이 있다.
금광은 1 x 1 크기의 칸으로 나누어져 있으며
각 칸에는 특정한 크기의 금광이 있다.

채굴자는 첫번째 열부터 출발하여 (행은 상관없음) m - 1 번을 걸쳐
매번 오른쪽 위, 오른쪽, 오른쪽 아래 3가지 중 하나의 위치로 이동한다.
이 때 채굴자가 얻을 수 있는 금의 최대 크기를 구하시오.

입력 : n x m 의 배열 int[][] arr
출력 : 얻을 수 있는 금의 최대 크기 int answer

---------------------
|  1 |  3 |  3 |  2 |
---------------------
|  2 |  1 |  4 |  1 |
---------------------
|  0 |  6 |  4 |  7 |
---------------------

일 때 2 -> 6 -> 4 -> 7 
19 가 최대 크기이다.

1 <= n 
m <= 20
1 <= 금강배열에 들어가는 금의 개수 <= 100

```


```java
3번 해답

금광의 모든 위치에 대해 3가지만 고려하면 된다
1) 왼쪽 위에서 오는 경우
2) 왼쪽에서 오는 경우
3) 왼쪽 아래에서 오는 경우

세가지를 비교하며 현재 금강과 더해서
가장 많음 금을 갖고 있는 경우를 dp 테이블에 갱신한다.

dp[i][j] = dp[i][j] + max(dp[i-1][j-1], dp[i][j-1], dp[i+1][j-1])

매번 테이블에 접근 할 때 배열의 범위를 벗어나지 않는지 체크해야 한다.

    int solution(int[][] arr) {
        int[][] dp = arr;
        int n = arr[0].length; // 행
        int m = arr.length; // 열
        log.info ("행 : " + n + "열 : " + m);

        for (int j = 1; j < m; j++) {
            for (int i = 0; i < n; i++) {
                log.info ("dp : " + dp[i][j]);
                int leftUp = 0;
                int left = 0;
                int leftDown = 0;
                // 왼쪽 위에서 오는 경우
                if (i == 0) {
                    leftUp = 0;
                } else {
                    leftUp = dp[i - 1][j - 1];
                }

                // 왼쪽 아래에서 오는 경우
                if (i == (m - 1)) {
                    leftDown = 0;
                } else {
                    leftDown = dp[i + 1][j - 1];
                }

                // 왼쪽에서 오는 경우
                left = dp[i][j - 1];
                dp[i][j] = dp[i][j] + Math.max (leftUp, Math.max (left, leftDown));
            }
        }
        int max = 0;
        for (int i = 0; i < n; i++) {
            max = Math.max (max, dp[i][m - 1]);
        }
        return max;
    }

```
