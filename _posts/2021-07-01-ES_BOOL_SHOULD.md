---
title:  "ES BOOL SHOULD"
excerpt: ""

categories:
  - ES
  - 스터디
last_modified_at: 2020-07-01T08:06:00-05:00
---


원하는 결과


아래 terms 쿼리와 같이 app_dvs_cd 라는 필드에 여러가지 조건을 달아서 조회하고 싶다.

GET /fc_fc/_search
{ 
  "query": {
    "terms": {
      "app_dvs_cd": [
        "ALL",
        "ELLT"
      ]
    }
 }, "size" : 100
}

==> app_dvs_cd 가 ALL 이거나 ELLT 인 데이터가 조회.





bool should 쿼리를 이용하여 쿼리


DSL

GET /fc_fc/_search
{ 
  "query": {
    "bool": {
      "must": [
         {
           "bool": {
              "should": [
              {
                "match": {
                  "app_dvs_cd": {
                    "query": "ALL",
                    "operator": "OR",
                    "prefix_length": 0,
                    "max_expansions": 50,
                    "fuzzy_transpositions": true,
                    "lenient": false,
                    "zero_terms_query": "NONE",
                    "auto_generate_synonyms_phrase_query": true,
                    "boost": 1.0
                  }
                }
              },
              {
                "match": {
                  "app_dvs_cd": {
                    "query": "ELLT",
                    "operator": "OR",
                    "prefix_length": 0,
                    "max_expansions": 50,
                    "fuzzy_transpositions": true,
                    "lenient": false,
                    "zero_terms_query": "NONE",
                    "auto_generate_synonyms_phrase_query": true,
                    "boost": 1.0
                  }
                }
              }
            ],
            "adjust_pure_negative": true,
            "boost": 1.0
           }
         }
      ]
    }
 }, "size" : 100
}
==> app_dvs_cd 가 ALL 이거나 ELLT 인 데이터가 조회



JAVA 소스

기존 BoolQueryBuilder에 sub BoolQueryBuilder를 add() 해준다.

BoolQueryBuilder subQuery = new BoolQueryBuilder();
 
subQuery.should ().add (QueryBuilders.matchQuery (AppVersionConstant.APP_ES_APP_DVS_CD, AppVersionConstant.APP_DVS_CD_ALL).operator (Operator.OR));
subQuery.should ().add (QueryBuilders.matchQuery (AppVersionConstant.APP_ES_APP_DVS_CD, appDvsCd).operator (Operator.OR));
 
query.must ().add (subQuery);
==> app_dvs_cd 가 ALL 이거나 입력받은 appDvsCd 인 데이터가 조회.