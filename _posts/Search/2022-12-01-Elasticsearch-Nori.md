---
title:  "[Elasticsearch] 한글을 분석하는 방법 - Nori 분석기"
date:   2022-12-01 00:00:00 +0900
categories:
  - Search
tags:
  - Search
  - Elasticsearch

toc: true
toc_sticky: true
toc_label: "Nori 분석기"
header-mask: true
---



&nbsp;안녕하세요 마개입니다.  
Elasticsearch에서 한글 형태소 분석기는 몇 가지가 있는데 그 중 하나인 Nori에 대해 알아봅니다.
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/203911459-7435cd5d-ad5a-455b-9aad-d208812e34bd.png){: .align-center}

<br><br>


# Nori란

`Nori` (노리)는 한글 형태소 분석기 중 하나입니다. 프랑스 엔지니어인 Jim Ferenczi에 의해 처음 개발되었고 **Elasticsearch 6.6** 버전부터 공식적으로 개발해서 지원하기 시작했습니다. AWS의 ES에는 Nori or 은전한닢이 내장되어있다.

`Nori`는 `은전한닢`에서 사용하는 mecab-ko-dic 사전을 재 가공하여 사용하고 있습니다. 루씬의 기능으로 개발되었으며 루씬 소스에 반영되었습니다.

* <a href="https://github.com/apache/lucene/tree/main/lucene/analysis/nori">깃헙 소스</a>

<br><br>

# Nori 설치

Nori를 사용하기 위해서는 Elasticsearch에 `analysis-nori` 플러그인을 설치해야 합니다. 

```sh
# nori 플러그인 설치
$ bin/elasticsearch-plugin install analysis-nori

# nori 플러그인 제거
$ bin/elasticsearch-plugin remove analysis-nori
```

<br><br>

# nori_tokenizer

Nori는 `nori_tokenizer` 토크나이저와 `nori_part_of_speech`, `nori_readingform` 토큰 필터를 제공합니다.  
먼저 standard와 nori_tokenizer를 비교해보겠습니다.

<br>

```sh
# Standard
GET _analyze
{
  "tokenizer": "standard",
  "text": [
    "동해물과 백두산이"
  ]
}

# Standard 결과
{
  "tokens" : [
    {
      "token" : "동해물과",
      "start_offset" : 0,
      "end_offset" : 4,
      "type" : "<HANGUL>",
      "position" : 0
    },
    {
      "token" : "백두산이",
      "start_offset" : 5,
      "end_offset" : 9,
      "type" : "<HANGUL>",
      "position" : 1
    }
  ]
}
```
위는 `standard` 토크나이저를 이용한 것인데 `공백`을 기준으로 분리하고 있습니다.

이번에는 `nori_tokenizer` 토크나이저를 이용해봅니다.

<br>

```sh
# Nori
GET _analyze
{
  "tokenizer": "nori_tokenizer",
  "text": [
    "동해물과 백두산이"
  ]
}

# Nori 결과
{
  "tokens" : [
    {
      "token" : "동해",
      "start_offset" : 0,
      "end_offset" : 2,
      "type" : "word",
      "position" : 0
    },
    {
      "token" : "물",
      "start_offset" : 2,
      "end_offset" : 3,
      "type" : "word",
      "position" : 1
    },
    {
      "token" : "과",
      "start_offset" : 3,
      "end_offset" : 4,
      "type" : "word",
      "position" : 2
    },
    {
      "token" : "백두",
      "start_offset" : 5,
      "end_offset" : 7,
      "type" : "word",
      "position" : 3
    },
    {
      "token" : "산",
      "start_offset" : 7,
      "end_offset" : 8,
      "type" : "word",
      "position" : 4
    },
    {
      "token" : "이",
      "start_offset" : 8,
      "end_offset" : 9,
      "type" : "word",
      "position" : 5
    }
  ]
}
```
그와 반면에 nori_tokenizer는 한국어 사전 정보를 이용해 `동해`, `산` 같은 단어를 분리하였습니다.  
nori_tokenizer에는 다음과 같은 옵션들이 있습니다.

* user_dictionary : 사용자 사전이 저장된 파일의 경로를 입력합니다.
* user_dictionary_rules : 사용자 정의 사전을 배열로 입력합니다. 텍스트 파일이 아니라 매핑할 때 지정해서 키 값을 저렇게 지정함.
* decompound_mode : 합성어의 저장 방식을 결정합니다.
  * none : 어근을 분리하지 않고 완성된 합성어만 저장합니다.
  * discard (기본값) : 합성어를 분리하여 각 어근만 저장합니다.
  * mixed : 어근과 합성어를 모두 저장합니다.

`user_dictionary`의 경우 config 디렉토리의 상대 경로를 입력하며 변경시 인덱스를 close / open 하면 반영됩니다. 또, Update API를 날려도 반영이 됩니다. (<a href="https://www.elastic.co/guide/en/elasticsearch/reference/master/indices-reload-analyzers.html">URL</a>)

index의 filter에 `updateable`을 설정해놓으면 사용 가능합니다.

```sh
POST /my-index-000001/_reload_search_analyzers
```

<br>

사전의 단어들에는 우선순위가 있으며 `user_dictionary` 경로에 있는 사전 파일이나 `user_dictionary_rules` 설정값에 단어만 나열해주면 이 단어들을 우선으로 추출합니다.

```sh
# my_nori 인덱스에 "해물" 사전을 추가한 my_nori_tokenizer 생성
PUT my_nori
{
  "settings": {
    "analysis": {
      "tokenizer": {
        "my_nori_tokenizer": {
          "type": "nori_tokenizer",
          "user_dictionary_rules": [
            "해물"
          ]
        }
      }
    }
  }
}
```

```sh
# "동해물과" 분석
GET my_nori/_analyze
{
  "tokenizer": "my_nori_tokenizer",
  "text": [
    "동해물과"
  ]
}

# 결과
{
  "tokens" : [
    {
      "token" : "동",
      "start_offset" : 0,
      "end_offset" : 1,
      "type" : "word",
      "position" : 0
    },
    {
      "token" : "해물",
      "start_offset" : 1,
      "end_offset" : 3,
      "type" : "word",
      "position" : 1
    },
    {
      "token" : "과",
      "start_offset" : 3,
      "end_offset" : 4,
      "type" : "word",
      "position" : 2
    }
  ]
}
```