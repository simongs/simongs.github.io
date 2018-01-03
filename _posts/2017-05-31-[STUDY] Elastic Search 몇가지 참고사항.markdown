---
layout: post
title: "[ELASTIC_SEARCH] Elastic Search 몇가지 참고사항"
date:   2017-05-31 09:00:00 +0900
categories: ELASTIC_SEARCH
---

- Table of Contents
{:toc}

# Elastic Search

## Tips

### 1. Parent-Child 구성

#### ▶ 성능상 고려사항
 - Parent-Child 쿼리는 Nested 쿼리보다 5~10배 정도 느리다.
 - 각 Parent에 많은 Children이 있는 경우가 더 적합하다.
    (적은 children에 많은 parent 구성보다)

#### ▶ 충고사항
 - 심사숙고하여 결정하길 바랍니다. (parent 보다 children 이 많을때 사용하세요)
 - 단일 쿼리에서 여러개의 parent, child 조합은 피하세요
 - has_children 쿼리에서는 Scoring을 피하세요. (score_mode를 none으로 세팅하세요)
 - parent의 _id 정보를 짧게 정하세요. (메모리 사용과 Doc Value 압축효율 높임)
 - parent-child 모델에 이르기전에 other relationship techniques 가 있는지 생각해보세요.

#### ▶ 유의사항
 - 조회시에 Parent의 상세 정보는 결과에 포함되지 않습니다. (별도의 질의 필요)

#### ▶ 샘플 URL
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

### 2. Elastic Date Type

JSON 에서는 별도의 datetype을 가지고 있지 않다.
Elastic Search 에서 Date 타입을 표현하기 위한 방법은 아래와 같다.

1. 포맷팅된 문자열을 사용한다. (ex. 2015-01-01, 2015/01/01 12:10:31)
2. epoch (1970-01-01) 이후의 시간을 milliseconds 단위로 표현한 Long 타입 숫자 (ex. 1483341642046)
3. epoch (1970-01-01) 이후의 시간을 seconds 단위로 표현한 Integer 타입 숫자 (ex. 1483341642046)

내부적으로 날짜형식은 UTC 로 변경되고, 두번째 포맷으로 저장된다.

Date 포맷은 커스터마이징 될 수 있다.
만약 포맷이 지정되지 않는다면 아래와 같이 default로 동작한다.

~~~
"strict_date_optional_time||epoch_millis"
~~~

예를 들어 아래와 같은 코드가 있다고 가정한다.
~~~
0) The date field uses the default format.
PUT my_index
{
  "mappings": {
    "my_type": {
      "properties": {
        "date": {
          "type": "date" 
        }
      }
    }
  }
}

1) This document uses a plain date.
PUT my_index/my_type/1
{ "date": "2015-01-01" } 

2) This document includes a time.
PUT my_index/my_type/2
{ "date": "2015-01-01T12:10:30Z" } 

3) This document uses milliseconds-since-the-epoch
PUT my_index/my_type/3
{ "date": 1420070400001 } 

4) Note that the sort values that are returned are all in milliseconds-since-the-epoch.
GET my_index/_search
{
  "sort": { "date": "asc"} 
}
~~~

### 3. store 개념
기본적으로 필드 밸류들은 검색이 가능하도록 인덱싱이 된다.그러나 저장이 되진 않는다.
이것은 쿼리할 때 사용될 수 있어도 원 필드 값을 가져오는 것은 불가능하다는 것을 의미한다.

일반적으로 이건 문제가 되지 않는다.
필드 값은 _source 필드안에 이미 포함되어 있기 때문이다.
만약 당신이 개별 필드 혹은 일부의 값을 조회하길 원한다면 (_source의 전체 값 대신)
_source 관련한 설정을 [필터링](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-source-filtering.html)할 수 있다. (ex. _source=false)

이러한 상황에 특정 필드의 store 하는 것은 합당하다.
예를 들어 당신이 만든 Document가 title, date, 큰 content 필드를 가지고 있다면
너는 큰 content 필드 빼고 title과 date 만 가져오길 원할 수 있다.

~~~

0) The title and date fields are stored.
PUT my_index
{
  "mappings": {
    "my_type": {
      "properties": {
        "title": {
          "type": "text",
          "store": true 
        },
        "date": {
          "type": "date",
          "store": true 
        },
        "content": {
          "type": "text"
        }
      }
    }
  }
}

2) 데이터 입력
PUT my_index/my_type/1
{
  "title":   "Some short title",
  "date":    "2015-01-01",
  "content": "A very long content field..."
}

3) title과 date 필드 정보만 가져온다.
GET my_index/_search
{
  "stored_fields": [ "title", "date" ] 
}
~~~

### 4. doc_values 개념

대부분의 필드는 기본적으로 검색가능하도록 인덱싱된다.

이런 인덱싱은 소팅이나, 집합 같은 연산에는 최적화 되어 있지 않다,

Doc Value는 디스크 기반은 자료구조이고, 인덱스 생성 시간에 만들어진다.
그 것은 같은 값을 _source에 저장한다. 

만약 특정 필드에 대해서 소팅이나, 집합 연산이나  스크립트를 통한 접근이 필요 없다면,
디스크 공간을 아끼기 위하여 doc values를 disable 시킬수 있다.

### 5. Index Module 개념

인덱스 모듈은 인덱스 하나당 생성되는 모듈이고 인덱스와 관련한 속성을 컨트롤한다.

인덱스 세팅 하나에 관련하여 세팅할 수 있다.
static - Close 상태나 생성시점에만 설정할 수 있다.
dynamic - update-index-settings API를 통해서 변경할 수 있다.

index.max_result_window
from + size 의 maximum value를 의미하고 default로는 10000입니다.
검색 요청은 from+size 크기에 비례해서 힙메모리를 증가시킵니다.

## FAQ

### 1. primary shard 와 replica shard의 차이점
primary shard는 원본 데이터가 쪼개진 shard를 의미하고 
replica shard는 primary shard 데이터 기준으로 복제된다.

### 2. 운영과정에서의 Trouble Shooting
해당 정보는 SKPlanet의 동영상을 보고 정리하였습니다.

<figure class="video_container">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/y0fSe5nLeMs" frameborder="0" allowfullscreen></iframe>
</figure>

## Reference
- [Parent-Child Relationship Reference DOC](https://www.elastic.co/guide/en/elasticsearch/guide/master/parent-child.html )