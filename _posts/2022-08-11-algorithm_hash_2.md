---
title:  "Programmers 해시 - 완주하지 못한 선수"
excerpt: "코딩 테스트 해시 문제 모음"

categories:
  - Algorithm
tags:
  - Java
last_modified_at: 2022-08-11T08:06:00-05:00
---

```java
수많은 마라톤 선수들이 마라톤에 참여하였습니다. 단 한 명의 선수를 제외하고는 모든 선수가 마라톤을 완주하였습니다.

마라톤에 참여한 선수들의 이름이 담긴 배열 participant와 완주한 선수들의 이름이 담긴 배열 completion이 주어질 때, 완주하지 못한 선수의 이름을 return 하도록 solution 함수를 작성해주세요.

제한사항
마라톤 경기에 참여한 선수의 수는 1명 이상 100,000명 이하입니다.
completion의 길이는 participant의 길이보다 1 작습니다.
참가자의 이름은 1개 이상 20개 이하의 알파벳 소문자로 이루어져 있습니다.
참가자 중에는 동명이인이 있을 수 있습니다.
입출력 예
participant	                                        completion	                                return
["leo", "kiki", "eden"]	                            ["eden", "kiki"]	                        "leo"
["marina", "josipa", "nikola", "vinko", "filipa"]	["josipa", "filipa", "marina", "nikola"]	"vinko"
["mislav", "stanko", "mislav", "ana"]	            ["stanko", "ana", "mislav"]	                "mislav"
입출력 예 설명
예제 #1
"leo"는 참여자 명단에는 있지만, 완주자 명단에는 없기 때문에 완주하지 못했습니다.

예제 #2
"vinko"는 참여자 명단에는 있지만, 완주자 명단에는 없기 때문에 완주하지 못했습니다.

예제 #3
"mislav"는 참여자 명단에는 두 명이 있지만, 완주자 명단에는 한 명밖에 없기 때문에 한명은 완주하지 못했습니다.
```


# 해결 방법
- key 를 participant 의 참가자, value 를 그 참가자 이름으로 된 개수로 된 해시맵을 만든다.
- completion 를 한바퀴 돌면서 위에서 만든 해시맵에서 카운트를 하나씩 빼준다.
- 해시맵을 한바퀴 돌면서 value 가 0 이 아닌 key 를 반환한다.

# 구현 방법
- 주어진 `participant` 를 한바퀴 돌면서 Key 를 participant 의 엘리먼트로, Value 를 개수가 되게끔 HashMap `map` 을 만든다.
- 주어진 `completion` 를 한바퀴 돌면서 해당 Key 에 해당하는 요소의 Value 를 하나씩 빼준다.
- `map` 의 KeySet 을 가져와 Key 들을 한바퀴 돌면서 Value 가 0 이 아닌 Key 를 반환한다.
- 소스 코드
```java
import java.util.Arrays;
import java.util.*;
class Solution {
    public String solution(String[] participant, String[] completion) {
        String answer = "";
        Map<String, Integer> map = new HashMap<>();
        
        Arrays.stream(participant)
            .forEach(p -> map.put(p, map.getOrDefault(p, 0) + 1));

        Arrays.stream(completion)
            .forEach(c -> map.put(c, map.get(c) - 1));
        
        for(String key : map.keySet()) {
            if(map.get(key) != 0) {
                answer = key;
            }
        }
        
        return answer;
    }
}

```