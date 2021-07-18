---
title:  "elasticsearch search_after"
excerpt: ""

categories:
  - elasticsearch
last_modified_at: 2020-07-18T08:06:00-05:00
---




```java
By default, you cannot use from and size to page through more than 10,000 hits.

This limit is a safeguard set by the index.max_result_window index setting.

If you need to page through more than 10,000 hits, use the search_after parameter instead.


엘라스틱서치 공식문서에서 1만건 이상의 히트가 필요할 때 search_after 를 사용하라고 권장하고 있다.
(ref : https://www.elastic.co/guide/en/elasticsearch/reference/7.10/paginate-search-results.html#search-after)
```


```java
How to deal with lists larger than 10 000 items?
After some research, here are the different options we have:

Limit lists to 10 000 articles. Most of the time, this is the right answer. Indeed, if you need pagination over more than 10 000 results, maybe your initial request is not precise enough. But in our context, Elasticsearch is not just a search engine. For SEO purposes, some lists of articles have to display the whole history which is above 10 000 articles.
Increase the value of index.max-result-window. This value exists in order to preserve Elasticsearch cluster memory from large queries. Increasing this value can lead to cluster latency and worst… crashes. Knowing that articles are the main service of the website, we cannot let that happen.
Use scroll API of Elasticsearch. When we have large requests and when latency is not a major cause, it is a good practice to use scroll API. Despite multiple warnings found on the web, we implemented this solution. It was as expected very slow, taking tens of minutes in addition to being costly because of the state kept by Elasticsearch between each iteration. In summary, this solution is not acceptable for real-time user requests.
Use search_after search request. This is the suitable solution for real-time user requests but we need to know the last result from the previous page.
```



# search_after 란?

- RDBMS 의 커서처럼 elasticsearch 에서 라이브 커서를 제공하여 다음 페이지를 조회 할 수 있게 해준다.
- 페이지네이션을 하기 위해 sort 값을 필수로 추가하여 조회해야 한다.
- 이때 sort 된 결과값이 unique 해야 한다. 만약 unique 하지 않게 정렬이 된다면 다음 페이지 조회 값이 검증되지 않는다.



# search_after 를 위해 sort search 하기

```javascript
GET /dp_dp/_search?
{  
  "query": {
    "bool": {
      "must": [
         {"match_phrase": {"hk":  "DSHOP$12905"}}
      ]
    }
 },
 "size" : 50,
 "sort": [
   {
     "reg_dttm": {
       "order": "asc"
     }, "sk.keyword": {
       "order": "asc"
     }
   }
 ]
} 
```


- 쿼리에서 sort 부분을 살펴보면 2개의 복합키를 사용하여` unique` 한 결과를 얻어내도록 하고 있다.
- 위 쿼리의 결과 중 마지막 데이터는 아래와 같다.
- 마지막 데이터의 결과에서 눈여겨 봐야 할 것은 `"sort"`.
- 이 내용을 바탕으로 `search_after` 를 날려야 한다.

```javascript
{
        "_index" : "dp_dp_20200416",
        "_type" : "_doc",
        "_id" : "DSHOP$12905+TMPL_SEQ$403+5469",
        "_score" : null,
        "_source" : {
          "regr_no" : "I0000001143",
          "hk" : "DSHOP$12905",
          "modr_st_dvs_cd" : "BO",
          "samp_img_rte_nm" : "display/conrner/M000010/",
          "gsi01_hk" : "DCORN$M000010",
          "dcorn_lnk_seq" : 5469,
          "dcorn_id" : "image_banner_05",
          "dcorn_dmdul_dvs_cd" : "M",
          "dset_nm" : "이미지배너",
          "samp_img_file_nm" : "f8e597250168f98724488a786cb65018-21.jpg",
          "gsi01_sk" : "DSHOP$12905+2",
          "modr_no" : "I0000001234",
          "sk" : "TMPL_SEQ$403+5469",
          "tmpl_seq" : 403,
          "reg_dttm" : "2020-03-09 19:16:55",
          "tgt_lnk_typ_cd" : "02",
          "dp_yn" : "Y",
          "mod_dttm" : "2020-07-23 19:20:19",
          "modr_st_cd" : "BO_CC",
          "regr_st_cd" : "BO_CC",
          "tmpl_no" : 2,
          "regr_st_dvs_cd" : "BO",
          "dshop_no" : 12905,
          "dp_strt_dttm" : "2020-03-09 19:21:45",
          "del_yn" : "N",
          "dp_end_dttm" : "9999-12-31 23:59:59",
          "dcorn_nm" : "이미지 텍스트 N개 배너 텍스트 중형",
          "rnd_epsr_cd" : "02",
          "prir_rnkg" : 18,
          "dcorn_dmdul_typ_cd" : "M_GNRL",
          "dcorn_no" : "M000010",
          "blnk_cd" : "04",
          "samp_img_file_id" : "P3449913415FDF972988B7E337D168F3DEE192DFBE97F4557578E096C02BA9D14"
        },
        "sort" : [
          1583781415000,
          "TMPL_SEQ$403+5469"
        ]
      }
```



# 콘솔에서 search_after 쿼리 날리기


- search_after 는 아주 간단하다. 어떤 시점 이후의 정렬된 데이터부터 조회할 지 이전 데이터에서 얻은 sort 를 가지고 조회하기만 하면 된다.

```javascript
GET /dp_dp/_search?
{ 
  "search_after" : [
          1583781415000,
          "TMPL_SEQ$403+5469"
        ]
  ,"query": {
    "bool": {
      "must": [
         {"match_phrase": {"hk":  "DSHOP$12905"}}
      ]
    }
 },
 "size" : 50,
 "sort": [
   {
     "reg_dttm": {
       "order": "asc"
     }, "sk.keyword": {
       "order": "asc"
     }
   }
 ]
} 
```

- 아까 조회했던 50개의 데이터 이후의 데이터부터 50개가 조회된다.


# 소스코드에서의 search_after 


- sort 필드가 unique 해야 함. 현재 sort 필드는 reg_dttm 으로 unique 하지 않음 → sort 필드를 추가해서 unique 한 sort 결과를 만들기 (적절한 sort 필드 선정 필요)
- hits 의 length 가 10,000 미만이면 search_after 수행하지 않음
- 10,000 개 조회 후 hits 의 length 가 10,000 이면 search_after 수행
- search_after 이후에 hits 객체 2개에 대해 esDataLists 에 add() 수행


```java
// AS-IS 소스코드

				SearchSourceBuilder sourceBuilder = new SearchSourceBuilder(); 
				sourceBuilder.query(queryBuilder);
				sourceBuilder.from(0);
				sourceBuilder.size(10000);
				sourceBuilder.timeout(new TimeValue(BffConstant.ES_TIME_OUT, TimeUnit.SECONDS));
				sourceBuilder.sort(new FieldSortBuilder("reg_dttm").order(SortOrder.ASC));
				

				SearchRequest searchRequest = new SearchRequest();
				searchRequest.indices(BffConstant.ES_DP_DP_INDEX);
				searchRequest.source(sourceBuilder);
				
				/* Sync */
				SearchResponse searchResponse = restHighLevelClient.search(searchRequest, RequestOptions.DEFAULT);
				
				Gson gson = new Gson();
				
				searchResponse.getHits().forEach(item -> {
					EsDataBase esData = gson.fromJson(item.getSourceAsString(), EsDataBase.class);
					esDataList.add(esData);
		        });
```


```java
// TO-BE 소스코드

				SearchSourceBuilder sourceBuilder = new SearchSourceBuilder(); 
				sourceBuilder.query(queryBuilder);
				sourceBuilder.from(0);
				sourceBuilder.size(ES_SIZE);
				sourceBuilder.timeout(new TimeValue(BffConstant.ES_TIME_OUT, TimeUnit.SECONDS));
				sourceBuilder.sort(new FieldSortBuilder("reg_dttm").order(SortOrder.ASC));
				sourceBuilder.sort (new FieldSortBuilder ("sk.keyword").order (SortOrder.ASC)); // 기존 sort 값은 unique 하지 않기에 sk.keyword 로 unique 하게 수정

				SearchRequest searchRequest = new SearchRequest();
				searchRequest.indices(BffConstant.ES_DP_DP_INDEX);
				searchRequest.source(sourceBuilder);
				
				/* Sync */
				SearchResponse searchResponse = restHighLevelClient.search(searchRequest, RequestOptions.DEFAULT);
				SearchHits hits = searchResponse.getHits ();
				Gson gson = new Gson();

				if (hits != null) {
					hits.forEach(item -> {
						EsDataBase esData = gson.fromJson(item.getSourceAsString(), EsDataBase.class);
						esDataList.add(esData);
					});

					if (hits.getHits ().length == ES_SIZE) { // hits 사이즈가 1회 조회 시 최대치를 조회하면 search_after 설정
						Object[] sortValues = hits.getHits ()[hits.getHits ().length - 1].getSortValues ();
						sourceBuilder.searchAfter (sortValues);
						searchRequest.source (sourceBuilder);
						searchResponse = restHighLevelClient.search (searchRequest, RequestOptions.DEFAULT);
						searchResponse.getHits ().forEach (item -> {
							EsDataBase esDataBase = gson.fromJson (item.getSourceAsString (), EsDataBase.class);
							esDataList.add (esDataBase);
						});
					}
				}
```



# 변경사항

- sort value 를 unique 하게 하기위해 식별자 sk 를 sort 조건으로 추가
- response 된 hits 가 ES 조회 사이즈와 같다면 searchAfter 의 인자로 sortValue 를 가지고 한번 더 조회




# 결과


- Size 와 내용이 모두 동일한것을 확인할 수 있었다.





# 주의할 점

- 현재는 최대 20,000 개 미만의 데이터만 조회된다 가정하에 추가로 한번 더 조회하는 로직인데, 이를 넘어서는 사이즈의 데이터를 조회한다면 반복문을 활용하도록 수정해야 함
- reg_dttm 처럼 설정된 정렬값이 중복이 존재하는 데이터 일 때, unique 하게 sort value 를 얻기 위해 새로운 sort 조건을 추가한다면 기존의 중복된 sort value 에 대해서는 정렬 순서가 조금은 달라질 수 있다. 
