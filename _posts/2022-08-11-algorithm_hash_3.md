---
title:  "Programmers 해시 - 전화번호 목록"
excerpt: "코딩 테스트 해시 문제 모음"

categories:
  - Algorithm
tags:
  - Java
last_modified_at: 2022-08-12T08:06:00-05:00
---

```java
전화번호부에 적힌 전화번호 중, 한 번호가 다른 번호의 접두어인 경우가 있는지 확인하려 합니다.
전화번호가 다음과 같을 경우, 구조대 전화번호는 영석이의 전화번호의 접두사입니다.

구조대 : 119
박준영 : 97 674 223
지영석 : 11 9552 4421
전화번호부에 적힌 전화번호를 담은 배열 phone_book 이 solution 함수의 매개변수로 주어질 때, 어떤 번호가 다른 번호의 접두어인 경우가 있으면 false를 그렇지 않으면 true를 return 하도록 solution 함수를 작성해주세요.

제한 사항
phone_book의 길이는 1 이상 1,000,000 이하입니다.
각 전화번호의 길이는 1 이상 20 이하입니다.
같은 전화번호가 중복해서 들어있지 않습니다.

입출력 예제
phone_book	                        return
["119", "97674223", "1195524421"]	false
["123","456","789"]	                true
["12","123","1235","567","88"]	    false
입출력 예 설명
입출력 예 #1
앞에서 설명한 예와 같습니다.

입출력 예 #2
한 번호가 다른 번호의 접두사인 경우가 없으므로, 답은 true입니다.

입출력 예 #3
첫 번째 전화번호, “12”가 두 번째 전화번호 “123”의 접두사입니다. 따라서 답은 false입니다.
```


# 해결 방법
- phone_book 을 오름차순으로 정렬한다.
- key 를 phone_book 의 요소, value 를 의미가 없는 임의의 값으로 된 해시맵을 만든다.
- 위에서 만든 해시맵에 phone_book 의 모든 요소에 대해 subString(0, 1) ~ 요소에서 하나 뺀 문자열에 대해서 key 가 있는지 검사한다.
- 있으면 false 를 반환한다.

# 구현 방법
- 주어진 `phone_book` 를 정렬한다.
- 주어진 `phone_book` 를 한바퀴 돌면서 Key 를 phone_book 의 엘리먼트로, Value 의미없는 문자열 value 로 된 HashMap `map` 을 만든다.
- 주어진 `phone_book` 를 한바퀴 돌면서 `map` 에 `phone_book` 의 요소의 subString 들이 있는지 2중 for loop 으로 확인한다.
- `map` 에 키가 있으면 false 를 반환한다.
- 소스 코드
```java
import java.util.Arrays;
import java.util.*;

class Solution {
    public boolean solution(String[] phone_book) {
        boolean answer = true;
        Map<String, String> map = new HashMap<>();
        Arrays.sort(phone_book);
        
        for(String phone : phone_book) {
            map.put(phone, "value");
        }
        for(int i = 0; i < phone_book.length; i++) {
            for(int j = 1; j < phone_book[i].length(); j++) {
                if(map.containsKey(phone_book[i].substring(0, j))) {
                    return false;
                }
            }
        }

        return answer;
    }
}

```