---
title:  "sonarqube"
excerpt: ""

categories:
  - java
  - 스터디
last_modified_at: 2020-07-01T08:06:00-05:00
---


# Sonarqube
소스코드에 정적분석을 진행하여 코드 품질을 개선하기 위한 툴



# SonarLint

코드 개발 진행 후 하단 sidebar의 sonarlint 실행
- 자신이 개발한 코드 추가 및 수정건에 대하여 개선 진행
- issue 분석 결과 중 개선 가능한 코드 수정 권장(개발 진행시 간단한 건은 수정)
- Blocker/Critical을 제외한 issue는 무시 가능
- Blocker/Critical은 각각 아이콘으로 확인 가능(Blocker : ! / Critical : ↑)
- 개선 관련 규칙 및 예시는 "Rule"에서 확인 가능
- 코드 수정 후 다시 재분석 실행하여 Blocker/Critical issue 확인

ref : https://marketplace.eclipse.org/content/sonarlint#group-screenshots