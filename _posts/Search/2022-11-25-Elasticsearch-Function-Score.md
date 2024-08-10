---
title:  "[Elasticsearch] 유연하게 scoring하는 방법 - function_score"
date:   2022-11-25 00:00:00 +0900
categories:
  - Search
tags:
  - Search
  - Elasticsearch

toc: true
toc_sticky: true
toc_label: "function_score"
header-mask: true
---



&nbsp;안녕하세요 마개입니다.  
Elasticsearch에서 용어나 필드를 부스팅하는데 한계가 있다보니 유연하게 스코어링을 조절할 수 있는 방식으로 function_score 방식을 제공합니다. 해당 기능에 대해 알아봅니다.
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/203911459-7435cd5d-ad5a-455b-9aad-d208812e34bd.png){: .align-center}

<br><br>

해당 기능에 대해서는 <a href="https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-function-score-query.html">공식 문서</a>를 참고하였습니다. 

<br><br>

## 기본적인 쿼리 형태 

### 한 가지의 function만 사용할 때 

```json
GET /_search
{
  "query": {
    "function_score": {
      "query": { "match_all": {} },
      "boost": "5",
      "random_score": {}, 
      "boost_mode": "multiply"
    }
  }
}
```

기본적으로 하나의 function을 이용할 때는 `function_score` 안에 쿼리를 입력하면 됩니다. 

<br>

### 여러 종류의 function을 이용할 때 

```json
GET /_search
{
  "query": {
    "function_score": {
      "query": {
        "match": {
          "컬럼명": "값"
        }
      },
      # 여러 Function을 이용할 때는 functions, 아닐 때는 위에 function_score 안에 넣는다.
      "functions": [
      
      ]
    }
  }
}
```

function을 여러 개 사용할 때는 `functions`를 별도로 지정하고 그 안에 각 `function`을 지정합니다. 지정한 function들에 대해서는 `score_mode`로 여러 방법을 이용해 조합할 수 있습니다. 

<br>

```json
GET /_search
{
  "query": {
    "function_score": {
      "query": { "match_all": {} },
      "boost": "5", 
      "functions": [
        {
          "filter": { "match": { "test": "bar" } },
          "random_score": {}, 
          "weight": 23
        },
        {
          "filter": { "match": { "test": "cat" } },
          "weight": 42
        }
      ],
      "max_boost": 42,
      "score_mode": "max",
      "boost_mode": "multiply",
      "min_score": 42
    }
  }
}
```

`boost`는 전체 쿼리를 부스팅합니다. `filter`가 없는 경우에는 전체에 해당되는 경우와 같습니다. `"match_all": {}`

<br>

`score_mode`는 계산된 각각 function의 점수가 결합되는 방법을 지정합니다.

* `multiply` : score들을 곱셈합니다. (기본값)
* `sum` : score들을 더합니다.
* `avg` : score들의 평균값을 구합니다.
* `first` : filter에 매칭되는 첫번째 값을 가져옵니다.
* `max` : 가장 큰 score를 이용합니다.
* `min` : 가장 작은 score를 이용합니다.

`boost_mode`는 계산된 score와 쿼리의 score를 결합하는 방법에 대해 나타냅니다.

* `multiply` : 쿼리 score와 function score를 곱합니다. (기본값)
* `replace` : function score만 이용하고 쿼리 score는 무시됩니다.
* `sum` : 쿼리 score와 function score를 더합니다.
* `avg` : 평균
* `max` : 최대값
* `min` : 최솟값

<br><br>

## 지원되는 function 종류

function_score를 이용할 때 여러 function을 지원하고 있는데 지원되는 function에 대해 알아봅니다.

<br>

### script_score

`script_score`는 script를 이용하여 추가적인 score 계산을 할 수 있습니다.
script에는 도큐먼트에 있는 다른 숫자 필드 값을 이용할 수 있습니다. 이 때는 `doc['필드명']` 형식으로 이용합니다.

<br>

```json
GET /_search
{
  "query": {
    "function_score": {
      "query": {
        "match": { "message": "elasticsearch" }
      },
      "script_score": {
        "script": {
          "source": "Math.log(2 + doc['my-int'].value)"
        }
      }
    }
  }
}
```

* 주의사항
  * Elasticsearch에서는 모든 도큐먼트의 score는 32비트의 양수 float 형태입니다. 만약에 `script_score`가 범위를 넘어갈 경우 32비트 float값에 가까운 값으로 변환됩니다.
  * 음수의 값이 나올 경우 Elasticsearch는 에러를 반환합니다.

<br>

```json
GET /_search
{
  "query": {
    "function_score": {
      "query": {
        "match": { "message": "elasticsearch" }
      },
      "script_score": {
        "script": {
          "params": {
            "a": 5,
            "b": 1.2
          },
          "source": "params.a / Math.pow(params.b, doc['my-int'].value)"
        }
      }
    }
  }
}
```

`params`를 이용하여 원하는 값을 넣을 수 있습니다.

<br>

### weight

`weight`는 Elasticsearch에서 제공되는 `weight` 값에 특정 값을 곱할 수 있게 제공합니다.

```json
"weight": number
```

숫자에는 float 타입의 숫자를 입력합니다.

<br>

### random_score

`random_score`는 0부터 1미만 균일하게 분포된 값을 score로 제공합니다. 기본적으로 내부 루씬의 도큐먼트 id를 랜덤하게 사용하기 때문에 재사용할 수는 없습니다.  
score 값을 재사용하길 원한다면 `seed`와 `field`를 제공하고 있어 이를 이용하면 됩니다.

```json
GET /_search
{
  "query": {
    "function_score": {
      "random_score": {
        "seed": 10,
        "field": "_seq_no"
      }
    }
  }
}
```

<br>

### field_value_factor

`field_value_factor`는 score에 영향을 주기 위해서 도큐먼트의 `field`를 이용할 수 있게 제공합니다. 이는 `script_score` 기능과 유사하지만 스크립트의 과부하를 피합니다. 여러 값의 필드에 사용된다면 첫번째 값에만 적용이 됩니다.

```json
GET /_search
{
  "query": {
    "function_score": {
      "field_value_factor": {
        "field": "my-int",
        "factor": 1.2,
        "modifier": "sqrt",
        "missing": 1
      }
    }
  }
}
```

위의 내용은 아래의 산술식과 같습니다.

`sqrt(1.2 * doc['my-int'].value)`

`field_value_factor` 기능에는 아래와 같은 숫자 옵션들이 있습니다.

* `field` : 도큐먼트로부터 추출하고자 하는 필드
* `factor` : 필드 값과 곱해질 요소. 기본값은 1
* `modifier` : 필드 값에 적용될 제어자로 `none`, `log`, `log1p`, `log2p`, `ln`, `ln1p`, `ln2p`, `square`, `sqrt`, or `reciprocal`이 있고 기본값은 `none`입니다.
* `missing` : 해당 필드 값이 없을 경우 들어갈 기본값

