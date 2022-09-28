---
title:  "Programmers 해시 - 위장"
excerpt: "코딩 테스트 해시 문제 모음"

categories:
  - Algorithm
tags:
  - Java
last_modified_at: 2022-08-12T08:06:00-05:00
---

```java
스파이들은 매일 다른 옷을 조합하여 입어 자신을 위장합니다.

예를 들어 스파이가 가진 옷이 아래와 같고 오늘 스파이가 동그란 안경, 긴 코트, 파란색 티셔츠를 입었다면 다음날은 청바지를 추가로 입거나 동그란 안경 대신 검정 선글라스를 착용하거나 해야 합니다.

종류	이름
얼굴	동그란 안경, 검정 선글라스
상의	파란색 티셔츠
하의	청바지
겉옷	긴 코트
스파이가 가진 의상들이 담긴 2차원 배열 clothes가 주어질 때 서로 다른 옷의 조합의 수를 return 하도록 solution 함수를 작성해주세요.

제한사항
clothes의 각 행은 [의상의 이름, 의상의 종류]로 이루어져 있습니다.
스파이가 가진 의상의 수는 1개 이상 30개 이하입니다.
같은 이름을 가진 의상은 존재하지 않습니다.
clothes의 모든 원소는 문자열로 이루어져 있습니다.
모든 문자열의 길이는 1 이상 20 이하인 자연수이고 알파벳 소문자 또는 '_' 로만 이루어져 있습니다.
스파이는 하루에 최소 한 개의 의상은 입습니다.

입출력 예
clothes	                                                                                    return
[["yellow_hat", "headgear"], ["blue_sunglasses", "eyewear"], ["green_turban", "headgear"]]	5
[["crow_mask", "face"], ["blue_sunglasses", "face"], ["smoky_makeup", "face"]]	            3
입출력 예 설명
예제 #1
headgear에 해당하는 의상이 yellow_hat, green_turban이고 eyewear에 해당하는 의상이 blue_sunglasses이므로 아래와 같이 5개의 조합이 가능합니다.

1. yellow_hat
2. blue_sunglasses
3. green_turban
4. yellow_hat + blue_sunglasses
5. green_turban + blue_sunglasses
예제 #2
face에 해당하는 의상이 crow_mask, blue_sunglasses, smoky_makeup이므로 아래와 같이 3개의 조합이 가능합니다.

1. crow_mask
2. blue_sunglasses
3. smoky_makeup
```


# 해결 방법
- 경우의 수 문제를 해시맵으로 해결하는 문제이다.
- 예를 들어 모자 2개, 안경 1개로 경우의 수를 찾자면 모자1 + 안경, 모자2 + 안경 이 두가지 즉, 2 * 1 개 이다. 그런데 이번 문제는 모자를 안쓰고 안경만 쓸 수도 있고 안경을 안쓰고 모자를 쓸 수도 있다.
- 그러므로 (2 + 모자를 안쓰는 경우 1) * (1 + 안경을 안쓰는 경우1) = 6가지 경우의 수가 생긴다.
- 그런데 모자를 안쓰고, 안경도 안쓰는 경우의 수는 빼야 하므로 3 * 2 - 1 = 5가지 경우의 수로 계산하면 된다.


# 구현 방법
- 주어진 `clothes` 의 각 배열의 두번째 요소를 Key 로, 그 개수를 Value 로 된 해시맵을 만든다.
- 위에서 만든 해시맵의 keySet 을 가져와 각 value + 1 을 곱해서 마지막에 1 을 빼준 값을 반환한다.
- 소스 코드
```java
import java.util.*;

class Solution {
    public int solution(String[][] clothes) {
        int answer = 1;
        Map<String, Integer> map = new HashMap<>();
        
        // ex.첫번째 예제는 헤드기어는 2개, 안경은 1개인 해시맵이 만들어짐
        for(int i = 0; i < clothes.length; i++) {
            if(map.get(clothes[i][1]) != null){
                map.put(clothes[i][1], map.get(clothes[i][1]) + 1); 
            } else {
                map.put(clothes[i][1], 1);
            }
        }
        // 위에서 만든 해시맵의 keySet 을 가져와 각 value + 1 을 곱해서 마지막에 1 을 빼준 값을 반환한다.
        for(String key : map.keySet()) {
            answer = answer * (map.get(key) + 1);
        }
        answer = answer - 1;
        
        
        return answer;
    }
}

```