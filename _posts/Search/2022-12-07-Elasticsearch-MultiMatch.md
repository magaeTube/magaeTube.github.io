---
title:  "[Elasticsearch] 여러 필드에 검색하고자 할 때 - Multi Match"
date:   2022-12-07 00:00:00 +0900
categories:
  - Search
tags:
  - Search
  - Elasticsearch

toc: true
toc_sticky: true
toc_label: "Multi Match"
header-mask: true
---



&nbsp;안녕하세요 마개입니다.  
Elasticsearch에서 하나의 필드가 아니라 여러 필드에 쿼리를 하고자 할 때 match를 여러 번 작성하기 보다는 여러 필드를 한번에 쿼리할 수 있는데 이에 대해 알아봅니다. 본 내용은 7.10 버전에서 진행되었습니다.
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/203911459-7435cd5d-ad5a-455b-9aad-d208812e34bd.png){: .align-center}

<br><br>


## Multi Match 개념과 사용법

&nbsp;ES에서 여러 필드에 쿼리를 하고자 할 때 `multi_match`를 이용하여 여러 필드를 쿼리할 수 있습니다. 가장 기본적인 사용법을 먼저 보겠습니다.

```sh
GET /_search
{
  "query": {
    "multi_match": {
      "query": "this is a test",
      "fields": [ "subject", "message" ]
    }
  }
}
```
기본적인 사용법은 위와 같이 `query` 안에 `multi_match` 구문을 넣고 이 안에 `fields`를 통해 여러 필드를 지정하면 해당 필드들에 내용을 쿼리합니다. 

<br>

```sh
GET /_search
{
  "query": {
    "multi_match": {
      "query": "Will Smith",
      "fields": [ "title", "*_name" ]
    }
  }
}
```

여러 필드가 규칙적인 이름이라면 wildcard를 이용하는 방법도 있습니다. 위에서는 `*`를 이용하여 `title`, `first_name`, `last_name` 필드에서 검색하는 예제입니다.

<br>

```sh
GET /_search
{
  "query": {
    "multi_match": {
      "query": "this is a test",
      "fields": [ "subject^3", "message" ]
    }
  }
}
```

여러 필드들이 있지만 특정 필드 값에 가중치를 주고 싶을 때는 캐럿 기호(`^`)를 사용하여 값에 부스팅을 할 수 있습니다.

> 주의사항
> 한 번 쿼리할 때의 필드 개수 제한이 있습니다. settings에서 설정된 `indices.query.bool.max_clause_count` 속성인데 기본값으로 1024로 되어 있습니다.

<br><br>

## multi_match 타입

&nbsp;`multi_match`는 내부적으로 정의되는 `type` 파라미터에 따라 실행법이 달라집니다. 

* `best_fields` : 기본값으로 모든 필드에 매치되는 도큐먼트를 찾지만 가장 잘 맞는 필드의 `_score`를 이용합니다.
* `most_fields` : 모든 필드에나 매치되는 도큐먼트를 찾아서 각 필드의 `_score`를 결합합니다.
* `cross_fields` : 필드들을 동일한 분석기로 하나의 큰 필드로 다룹니다.
* `phrase` : 각 필드에 대해 `match_phrase` 쿼리를 실행하고 가장 잘 맞는 필드의 `_score`를 이용합니다. 
* `phrase_prefix` : 각 필드에 대해 `match_phrase_prefix` 쿼리를 실행하고 가장 잘 맞는 필드의 `_score`를 이용합니다.
* `bool_prefix` : 각 필드에 대해 `match_bool_prefix` 쿼리를 실행하고 각 필드의 `_score`를 결합합니다.

<br>

### best_fields
&nbsp;`best_fields`는 동일 필드에서 가장 많이 발견되는 여러 단어를 검색할 때 유용합니다.  
예를 들면 한 필드에서 “brown fox”가 한 필드의 “brown”과 다른 필드의 “fox”보다 의미있습니다.

<br>

`best_fields` 타입은 각 필드에 대해 `match` 쿼리를 생성하고 `dis_max` 쿼리로 감싼 것을 의미합니다.

```sh
GET /_search
{
  "query": {
    "multi_match": {
      "query": "brown fox",
      "type": "best_fields",
      "fields": ["subject", "message"],
      "tie_breaker": 0.3
    }
  }
}
```
위의 것은 아래와 같은 의미를 갖습니다.

<br>

```sh
GET /_search
{
  "query": {
    "dis_max": {
      "queries": [
        { "match": { "subject": "brown fox" }},
        { "match": { "message": "brown fox" }}
      ],
      "tie_breaker": 0.3
    }
  }
}
```
여기서 `tie_breaker`는 다음과 같이 스코어를 계산합니다.
* 최적으로 매치되는 필드로부터 스코어를 가져옵니다.
* 거기에 `다른 모든 매치되는 필드의 스코어 * tie_breaker`를 더합니다.

<br>

### most_fields

&nbsp;`most_fields` 타입은 다른 방법으로 분석된 같은 텍스트가 포함된 여러 필드를 검색할 때 유용합니다. 모든 Field에 대해 Match되는 각 field에 대해 점수를 계산하고 모두 합한다. 예시를 보도록 하겠습니다.

```sh
GET /_search
{
  "query": {
    "multi_match" : {
      "query":      "quick brown fox",
      "type":       "most_fields",
      "fields":     [ "title", "title.original", "title.shingles" ]
    }
  }
}
```

위의 쿼리는 다음과 같은 쿼리와 같습니다.

<br>

```sh
GET /_search
{
  "query": {
    "bool": {
      "should": [
        { "match": { "title":          "quick brown fox" }},
        { "match": { "title.original": "quick brown fox" }},
        { "match": { "title.shingles": "quick brown fox" }}
      ]
    }
  }
}
```

<br>

### phrase and phrase_prefix

&nbsp;`phrase`와 `phrase_prefix` 타입들은 `best_fields` 처럼 동작하지만 `match` 쿼리 대신에 `match_phrase` 또는 `match_phrase_prefix` 쿼리를 사용합니다.

<br>

```sh
GET /_search
{
  "query": {
    "multi_match": {
      "query": "quick brown f",
      "type": "phrase_prefix",
      "fields": ["subject", "message"]
    }
  }
}
```

<br>

위의 쿼리는 아래와 같은 동작을 합니다.

```sh
GET /_search
{
  "query": {
    "dis_max": {
      "queries": [
        { "match_phrase_prefix": { "subject": "quick brown f" }},
        { "match_phrase_prefix": { "message": "quick brown f" }}
      ]
    }
  }
}
```

<br>

### cross_fields

&nbsp;`cross_fields`는 여러 필드들이 매치되는 구조화된 도큐먼트에 유용합니다.  
예를 들어 `first_name` 과 `last_name` 필드들에 “Will Smith”를 검색한다면 가장 좋은 매치는 한쪽 필드에는 “Will” 다른 한쪽에는 “Smith”가 검색되도록 하는 것입니다.  
이러한 방식의 쿼리 중 하나의 방법은 `first_name` 과 `last_name` 필드들을 `full_name` 하나의 필드에 입력하는 것입니다.  

해당 type은 다음과 같이 검색을 진행합니다.
1. Query를 분석기로 분석한다
2. 분석된 각각의 Term 들을 모든 필드에 대해 질의하는데, 질의 대상이 되는 필드들을 1개의 큰 필드로 만들어 매치되는지 질의한다.

<br>

```sh
GET /_search
{
  "query": {
    "multi_match" : {
      "query":      "Will Smith",
      "type":       "cross_fields",
      "fields":     [ "first_name", "last_name" ],
      "operator":   "and"
    }
  }
}
```

위의 쿼리는 다음과 같이 실행됩니다.

```sh
+(first_name:will (OR) last_name:will) AND
+(first_name:smith (OR) last_name:smith)
```