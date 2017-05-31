---
layout: post
title: "[STUDY] Elastic Search"
date:   2017-05-31 09:00:00 +0900
categories: STUDY
---

# Elastic Search

## Elastic Search란

## 클러스터 구성

## Parent-Child 구성

### ▶ 성능상 고려사항
 - Parent-Child 쿼리는 Nested 쿼리보다 5~10배 정도 느리다.
 - 각 Parent에 많은 Children이 있는 경우가 더 적합하다.
    (적은 children에 많은 parent 구성보다)

### ▶ 충고사항
 - 심사숙고하여 결정하길 바랍니다. (parent 보다 children 이 많을때 사용하세요)
 - 단일 쿼리에서 여러개의 parent, child 조합은 피하세요
 - has_children 쿼리에서는 Scoring을 피하세요. (score_mode를 none으로 세팅하세요)
 - parent의 _id 정보를 짧게 정하세요. (메모리 사용과 Doc Value 압축효율 높임)
 - parent-child 모델에 이르기전에 other relationship techniques 가 있는지 생각해보세요.

### ▶ 유의사항
 - 조회시에 Parent의 상세 정보는 결과에 포함되지 않습니다. (별도의 질의 필요)

### ▶ 샘플 URL
- Parent 전체 조회 : http://10.162.4.44:9200/get-together/group/_search
- Child 전체 조회 : http://10.162.4.44:9200/get-together/event/_search
- Child 단건 조회 : http://10.162.4.44:9200/get-together/event/102?parent=1
- Child 복수 조회 (특정 부모를 가지고 있는) : 10.162.4.44:9200/get-together/event/_search
    - request body 부분
    ~~~
    {
    "query" : {
        "has_parent" : {
        "type" : "group",
        "query" : {
            "match" : {          
                "_id" : "1"
            }
        }
        }
    }
    }
    ~~~

## FAQ

### 1. primary shard 와 replica shard의 차이점

### 2. 운영과정에서의 Trouble Shooting
해당 정보는 SKPlanet의 동영상을 보고 정리하였습니다.



## Reference
- [Parent-Child Relationship Reference DOC](https://www.elastic.co/guide/en/elasticsearch/guide/master/parent-child.html )