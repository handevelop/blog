---
title:  "Programmers 해시 - 폰켓몬"
excerpt: "코딩 테스트 해시 문제 모음"

categories:
  - Algorithm
tags:
  - Java
last_modified_at: 2022-08-11T08:06:00-05:00
---

```java
당신은 폰켓몬을 잡기 위한 오랜 여행 끝에, 홍 박사님의 연구실에 도착했습니다. 홍 박사님은 당신에게 자신의 연구실에 있는 총 N 마리의 폰켓몬 중에서 N/2마리를 가져가도 좋다고 했습니다.
홍 박사님 연구실의 폰켓몬은 종류에 따라 번호를 붙여 구분합니다. 따라서 같은 종류의 폰켓몬은 같은 번호를 가지고 있습니다. 예를 들어 연구실에 총 4마리의 폰켓몬이 있고, 각 폰켓몬의 종류 번호가 [3번, 1번, 2번, 3번]이라면 이는 3번 폰켓몬 두 마리, 1번 폰켓몬 한 마리, 2번 폰켓몬 한 마리가 있음을 나타냅니다. 이때, 4마리의 폰켓몬 중 2마리를 고르는 방법은 다음과 같이 6가지가 있습니다.

첫 번째(3번), 두 번째(1번) 폰켓몬을 선택
첫 번째(3번), 세 번째(2번) 폰켓몬을 선택
첫 번째(3번), 네 번째(3번) 폰켓몬을 선택
두 번째(1번), 세 번째(2번) 폰켓몬을 선택
두 번째(1번), 네 번째(3번) 폰켓몬을 선택
세 번째(2번), 네 번째(3번) 폰켓몬을 선택
이때, 첫 번째(3번) 폰켓몬과 네 번째(3번) 폰켓몬을 선택하는 방법은 한 종류(3번 폰켓몬 두 마리)의 폰켓몬만 가질 수 있지만, 다른 방법들은 모두 두 종류의 폰켓몬을 가질 수 있습니다. 따라서 위 예시에서 가질 수 있는 폰켓몬 종류 수의 최댓값은 2가 됩니다.
당신은 최대한 다양한 종류의 폰켓몬을 가지길 원하기 때문에, 최대한 많은 종류의 폰켓몬을 포함해서 N/2마리를 선택하려 합니다. N마리 폰켓몬의 종류 번호가 담긴 배열 nums가 매개변수로 주어질 때, N/2마리의 폰켓몬을 선택하는 방법 중, 가장 많은 종류의 폰켓몬을 선택하는 방법을 찾아, 그때의 폰켓몬 종류 번호의 개수를 return 하도록 solution 함수를 완성해주세요.

제한사항
nums는 폰켓몬의 종류 번호가 담긴 1차원 배열입니다.
nums의 길이(N)는 1 이상 10,000 이하의 자연수이며, 항상 짝수로 주어집니다.
폰켓몬의 종류 번호는 1 이상 200,000 이하의 자연수로 나타냅니다.
가장 많은 종류의 폰켓몬을 선택하는 방법이 여러 가지인 경우에도, 선택할 수 있는 폰켓몬 종류 개수의 최댓값 하나만 return 하면 됩니다.
```


# 해결 방법
- key 를 nums 의 요소, value 를 개수로 하여 해시맵을 만들고 그 해시맵을 돌면서 아직 방문하지 않은 key 이면 카운트를 + 1 해준다.
- 이는 주어진 nums / 2 만큼 수행하면 된다.
- 이를 위해 버퍼용 해시맵(key:nums 의 요소, value:0으로 초기화, value 가 1이면 카운트를 하지 않기 위함)이 필요하다.

# 구현 방법
- 주어진 `nums` 를 한바퀴 돌면서 Key 를 nums 의 엘리먼트로, Value 를 개수가 되게끔 HashMap `map` 을 만든다.
- 이 때 `nums` 의 엘리먼트들을 Key 로, Value 를 0으로 하는 HashMap `resultMap` 도 같이 만든다. 
- `map` 의 KeySet 을 가져와 Key 들을 한바퀴 돌면서 `resultMap` 에 해당 key 의 Value 가 0 이면 1 로 바꾸어 주고 결과가 되는 answer 를 1로 바꾼다.
- nums/2 번 돌게 되면 for loop 를 멈추고 answer 를 반환한다.
- 소스 코드

```java
import java.util.*;

class Solution {
    public int solution(int[] nums) {
        int answer = 0;
        Map<Integer, Integer> map = new HashMap<Integer, Integer>();
        Map<Integer, Integer> resultMap = new HashMap<Integer, Integer>();
        int N = nums.length / 2;
        
        Arrays.stream(nums)
            .forEach(n -> {map.put(n, map.getOrDefault(n, 0) + 1);
                           resultMap.put(n, 0);});
         
        for(Integer key : map.keySet()) {
            if(N == 0) break;
            N--;
            if(resultMap.get(key) == 0) {
                resultMap.put(key, 1);
                answer++;
            }
        }
        
        return answer;
    }
}

```