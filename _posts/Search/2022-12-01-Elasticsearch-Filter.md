---
title:  "[Elasticsearch] Filter 종류"
date:   2022-12-01 00:00:00 +0900
categories:
  - Search
tags:
  - Search
  - Elasticsearch

toc: true
toc_sticky: true
toc_label: "Filter 종류"
header-mask: true
---



&nbsp;안녕하세요 마개입니다.  
Elasticsearch에서 데이터를 인덱싱할 때 Tokenizer를 통해 Token화된 이후에 필터링이 필요할 때 사용하는 것이 Filter입니다. Settings를 통해 filter를 지정할 수 있는데 몇 가지 filter에 대해 알아봅니다.
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/203911459-7435cd5d-ad5a-455b-9aad-d208812e34bd.png){: .align-center}

<br><br>

<a href="https://www.elastic.co/guide/en/elasticsearch/reference/7.10/analysis-tokenfilters.html">공식 문서(version 7.10)</a>에 있는 자료를 바탕으로 정리하였습니다.

<br><br>

## Stop

`stop` 필터의 경우 Token에서 불용어를 제거하는 역할을 합니다. 커스텀마이징을 하지 않는다면 기본적으로 영어의 불용어를 제거합니다.  
영어의 불용어는 다음과 같습니다.  
`a`, `an`, `and`, `are`, `as`, `at`, `be`, `but`, `by`, `for`, `if`, `in`, `into`, `is`, `it`, `no`, `not`, `of`, `on`, `or`, `such`, `that`, `the`, `their`, `then`, `there`, `these`, `they`, `this`, `to`, `was`, `will`, `with`

<br>

### 예시

```sh
GET /_analyze
{
  "tokenizer": "standard",
  "filter": [ "stop" ],
  "text": "a quick fox jumps over the lazy dog"
}
```

해당 필터를 통해 결과는 아래와 같이 나옵니다.

<br>

```sh
[ quick, fox, jumps, over, lazy, dog ]
```

영어의 불용어인 `a`, `the`가 빠진 것을 보실 수 있습니다.

<br>

### Analyzer에 추가

인덱스를 생성할 때에 filter를 지정할 수 있습니다. 

<br>

```sh
PUT /my-index-000001
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_analyzer": {
          "tokenizer": "whitespace",
          "filter": [ "stop" ]
        }
      }
    }
  }
}
```

filter를 지정할 때 설정할 수 있는 파라미터들이 있습니다.

* `stopwords` (옵션, string) : 불용어를 적용할 언어를 선택합니다. `_arabic_`, `_thai_`등. 기본값은 `_english_` 입니다. 각 언어는 루씬에서 사전 적용된 불용어 리스트를 이용합니다. 루씬에서 제공하는 언어들을 <a href="https://www.elastic.co/guide/en/elasticsearch/reference/7.10/analysis-stop-tokenfilter.html#analysis-stop-tokenfilter-stop-words-by-lang">여기</a>에서 확인하실 수 있습니다.
* `stopwords_path` (옵션, string) : 불용어 리스트가 담겨있는 파일의 경로를 작성합니다. 해당 경로는 절대 경로 또는 `config` 위치에서의 상대 경로로 작성해야 합니다. 파일은 `UTF-8` 로 인코딩 되어있고 각 단어는 **줄바꿈**으로 구분해야 합니다.
* `ignore_case` (옵션, Boolean) : `true` 로 설정하면 불용어는 대소문자를 가리지 않습니다. 기본 값은 `false` 입니다.
* `remove_trailing` (옵션, Boolean) : true 로 설정하면 마지막 token이 불용어이면 삭제됩니다. 기본 값은 true 입니다.

<br>

### 커스텀마이징

새로운 token filter를 만들어 `stop` 필터를 커스텀마이징합니다.

<br>

```sh
PUT /my-index-000001
{
  "settings": {
    "analysis": {
      "analyzer": {
        "default": {
          "tokenizer": "whitespace",
          "filter": [ "my_custom_stop_words_filter" ]
        }
      },
      "filter": {
        "my_custom_stop_words_filter": {
          "type": "stop",
          "ignore_case": true
        }
      }
    }
  }
}
```
기본적인 형태는 위와 같이 설정할 수 있습니다.

<br>

```sh
PUT /my-index-000001
{
  "settings": {
    "analysis": {
      "analyzer": {
        "default": {
          "tokenizer": "whitespace",
          "filter": [ "my_custom_stop_words_filter" ]
        }
      },
      "filter": {
        "my_custom_stop_words_filter": {
          "type": "stop",
          "ignore_case": true,
          "stopwords": [ "and", "is", "the" ]
        }
      }
    }
  }
}
```
또한 불용어를 `stopwords`로 직접 지정해서 사용할 수도 있습니다.

<br><br>

## Synonym

`synonym` 필터의 경우 분석 과정에서 동의어를 다룰 때 사용합니다.

<br>

### 예시

```sh
PUT /test_index
{
  "settings": {
    "index": {
      "analysis": {
        "analyzer": {
          "synonym": {
            "tokenizer": "whitespace",
            "filter": [ "synonym" ]
          }
        },
        "filter": {
          "synonym": {
            "type": "synonym",
            "synonyms_path": "analysis/synonym.txt"
          }
        }
      }
    }
  }
}
```

위의 상황에서는 `synonym` 필터를 구성하는 것인데 `analysis/synonym.txt` 로 `config` 에서 상대 경로로 지정합니다. 
추가적인 세팅은 다음과 같습니다.

* `expand` : 기본값은 `true` 입니다. synonym이 여러개 지정되어 있는데 expand가 false이면 맨 처음 단어만 적용이 됩니다..
* `lenient` (기본값 - `false`) : `true`로 설정하면 동의어를 설정하는 과정에서 예외를 무시합니다. 파싱할 수 없는 규칙에 대해서는 무시해야하기 때문에 중요합니다. false인데 규칙을 무시한 형태가 있으면 에러를 발생합니다.

<br>

```sh
PUT /test_index
{
  "settings": {
    "index": {
      "analysis": {
        "analyzer": {
          "synonym": {
            "tokenizer": "standard",
            "filter": [ "my_stop", "synonym" ]
          }
        },
        "filter": {
          "my_stop": {
            "type": "stop",
            "stopwords": [ "bar" ]
          },
          "synonym": {
            "type": "synonym",
            "lenient": true,
            "synonyms": [ "foo, bar => baz" ]
          }
        }
      }
    }
  }
}
```
위의 예에서는 `bar`는 무시되고 `foo => baz`는 유지됩니다.

<br>

### synonym 작성 방법

2가지의 포맷으로 synonym을 작성할 수 있습니다.

<br><br>

#### Solr synonyms

```plaintext
# Blank lines and lines starting with pound are comments.

# Explicit mappings match any token sequence on the LHS of "=>"
# and replace with all alternatives on the RHS.  These types of mappings
# ignore the expand parameter in the schema.
# Examples:
i-pod, i pod => ipod
sea biscuit, sea biscit => seabiscuit

# Equivalent synonyms may be separated with commas and give
# no explicit mapping.  In this case the mapping behavior will
# be taken from the expand parameter in the schema.  This allows
# the same synonym file to be used in different synonym handling strategies.
# Examples:
ipod, i-pod, i pod
foozball , foosball
universe , cosmos
lol, laughing out loud

# If expand==true, "ipod, i-pod, i pod" is equivalent
# to the explicit mapping:
ipod, i-pod, i pod => ipod, i-pod, i pod
# If expand==false, "ipod, i-pod, i pod" is equivalent
# to the explicit mapping:
ipod, i-pod, i pod => ipod

# Multiple synonym mapping entries are merged.
foo => foo bar
foo => baz
# is equivalent to
foo => foo bar, baz
```
  
Solr 작성 방법은 위와 같습니다. 이렇게 만든 파일은 `synonyms_path`를 통해 경로를 지정할 수 있습니다.

<br>

```sh
PUT /test_index
{
  "settings": {
    "index": {
      "analysis": {
        "filter": {
          "synonym": {
            "type": "synonym",
            "synonyms": [
              "i-pod, i pod => ipod",
              "universe, cosmos"
            ]
          }
        }
      }
    }
  }
}
```
리스트가 적을 경우에는 `synonyms`에 직접 지정하는 것도 하나의 방법입니다.

<br><br>

#### WordNet synonyms

`WordNet` 기반 동의어들은 `format`을 이용하여 정의할 수 있습니다.

```sh
PUT /test_index
{
  "settings": {
    "index": {
      "analysis": {
        "filter": {
          "synonym": {
            "type": "synonym",
            "format": "wordnet",
            "synonyms": [
              "s(100000001,1,'abstain',v,1,0).",
              "s(100000001,2,'refrain',v,1,0).",
              "s(100000001,3,'desist',v,1,0)."
            ]
          }
        }
      }
    }
  }
}
```

<br><br>

## nori_part_of_speech

`nori_part_of_speech`는 nori에서 제공하는 filter 중 하나로 **품사** 태그들에 매칭되는 token을 제거합니다. 제공되는 품사들의 종류는 <a href="https://lucene.apache.org/core/8_7_0/analyzers-nori/org/apache/lucene/analysis/ko/POS.Tag.html">여기</a>에서 확인 가능합니다.  
해당 필터에서는 아래와 같은 설정을 해야 합니다.

* `stoptags` : 제거되어야 하는 품사 배열을 설정합니다.

기본적으로 `stoptags`는 다음과 같이 설정됩니다.

```json
"stoptags": [
    "E",
    "IC",
    "J",
    "MAG", "MAJ", "MM",
    "SP", "SSC", "SSO", "SC", "SE",
    "XPN", "XSA", "XSN", "XSV",
    "UNA", "NA", "VSV"
]
```

<br>

### 예시

```sh
PUT nori_sample
{
  "settings": {
    "index": {
      "analysis": {
        "analyzer": {
          "my_analyzer": {
            "tokenizer": "nori_tokenizer",
            "filter": [
              "my_posfilter"
            ]
          }
        },
        "filter": {
          "my_posfilter": {
            "type": "nori_part_of_speech",
            "stoptags": [
              "NR"   
            ]
          }
        }
      }
    }
  }
}

GET nori_sample/_analyze
{
  "analyzer": "my_analyzer",
  "text": "여섯 용이"  
}
```

위의 예시에는 숫자를 의미하는 품사인 `NR`에 해당되는 단어들이 삭제됩니다.

<br>

### 결과

```json
{
  "tokens" : [ {
    "token" : "용",
    "start_offset" : 3,
    "end_offset" : 4,
    "type" : "word",
    "position" : 1
  }, {
    "token" : "이",
    "start_offset" : 4,
    "end_offset" : 5,
    "type" : "word",
    "position" : 2
  } ]
}
```