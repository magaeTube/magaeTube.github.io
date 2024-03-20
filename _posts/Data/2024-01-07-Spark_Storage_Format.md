---
title:  "[Apache Spark] Storage Format"
date:   2024-01-07 00:00:00 +0900
categories:
  - Data
tags:
  - Data
  - Spark
toc: true
toc_sticky: true
toc_label: "Deep Dive into Storage Format"
header-img: https://github.com/magaeTube/magaeTube.github.io/assets/78892113/10d59a6c-1be0-45b8-a755-74f43c85fa91
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
&nbsp;Apache Spark는 RDD부터 시작하여 DataSet, DataFrame, Parquet 등 여러 포맷을 거쳐왔는데 이번에 Apache Spark의 스토리지 포맷에 대해 정리합니다. <a href="https://spoddutur.github.io/spark-notes/deep_dive_into_storage_formats.html">참고 문서</a>를 보고 정리했습니다.
{: .notice--info}

<br>

![Spark](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/10d59a6c-1be0-45b8-a755-74f43c85fa91){: .align-center} 

<br><br>

&nbsp;Apache Spark는 인메모리 빅데이터 처리 시스템으로서 메모리는 필수불가결한 요소이고 메모리를 효율적으로 사용하는 것이 매우 중요합니다. 관련 내용을 살펴봅니다.

<br><br>

# Spark는 어떠한 스토리지 포맷을 사용했을까?
* **Spark 1.0 to 1.3:** Java 객체로 표현되는 `RDD`
* **Spark 1.4 to 1.6:** 행 기반 포맷으로 저장되는 `DatsSet`과 `DataFrame`
* **Spark 2.x:** 인메모리 데이터가 저장되는 열 기반의 구조화된 `Parquet` 지원

<br><br>

# 데이터 스토리지 포맷의 변화
&nbsp;위에서 봤듯이 데이터 스토리지의 포맷은 변화하고 발전했는데 두 파트로 나누어 살펴봅니다.

<br>

## Part1. RDD에서 행 기반 Dataset으로의 발전

&nbsp;이 부분에서 Tungsten 프로젝트 이야기가 빠질 수 없습니다. Tungsten은 Spark 실행 엔진의 가장 큰 변화가 되어 왔고 많은 변화를 불러왔습니다.


### Tungsten 프로젝트의 목적

&nbsp;Tungsten 프로젝트의 목적은 메모리와 CPU 효율을 올리고 퍼포먼스를 한계로 올리는 것입니다. Spark 워크 로드가 IO와 네트워크 이슈보다 CPU와 메모리로 인해 병목 현상이 늘고 있기 때문에 이러한 목적으로 설정했습니다. 

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/c183ba9c-8557-4b75-b0cd-0e26e7f2d80d)

&nbsp;자세하게는 첫 번째는 매년 지날수록 하드웨어 (Storage, Network, CPU)의 성능이 향상되었다는 점, 두 번째로는 Spark의 셔플 시스템인 직렬화와 해싱이 병목현상을 야기하는 요소였다는 점입니다. 

<br>

**첫 번째 문제**

&nbsp;옛날 실행 엔진의 문제점을 파악해 봐야 합니다. 간단한 예로 Spark1.x이 정수의 스트림 데이터를 필터링하는 작업을 어떻게 처리하는지 봅니다.

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/b0e06a57-3729-4390-9075-0ea236a884f6)

&nbsp;옛날 실행 엔진을 통해 보면 Spark의 실행 계획은 단순히 Integer를 Boolean 값으로 변환하는 불투명한 로직으로 판단하기 때문에 사용자가 어떤 작업을 하고 싶어 하는지 그리고 데이터 타입은 무엇인지 알 수 없는 문제가 발생합니다. 이러한 불투명성 때문에 최적화에 한계가 있습니다. 그래서 Spark는 다음의 투명성이 필요합니다.
* Data Schema
  * 각 데이터에 얼마나 그리고 어떤 필드들이 있는지
  * 각 필드는 어떤 데이터 타입을 가지고 있는지
* User Operation
  * 이러한 데이터로 어떤 작업을 하려고 하는지
  
<br>

&nbsp;위의 두 가지 측면에서 투명성이 보장되면 다음과 같이 Spark의 성능을 향상시킬 수 있습니다.  

* 실행 계획을 명확하게 할 수 있습니다.

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/1a9fab8b-66aa-4c61-ba9f-379cfe77be30)

<br>

&nbsp;위의 예제와 같이 수행되는 것이 명확하면 실행 계획을 수행할 수 있고 n^2의 수행 속도를 판단할 수 있습니다.  

* 데이터 타입을 이용하여 속도를 향상할 수 있습니다.

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/11a9621f-f826-423b-b1c5-e5342d4ee0a1)
데이터 타입을 안다면 x와 y 컬럼을 정렬한 후에 비교하여 속도를 더 향상시킬 수 있습니다.
  
<br>

**첫 번째 문제에 대한 결론**
* Data Schema를 정의하라
* 수행하고자 하는 작업을 Spark에게 투명하게 공유하라

<br>

**두 번째 문제**  
&nbsp;Spark는 모든 것을 인메모리에서 하려고 한다는 것입니다.  
&nbsp;이 부분에서는 데이터가 메모리에 어떻게 배치되는지 봐야 합니다. Java 객체로 저장되는 RDD 데이터는 우리가 수행을 하려고 할 때마다 직렬화, 역 직렬화, 해싱과 객체 생성의 작업들이 발생하게 되고 이러한 것들은 오버헤드를 불러옵니다.

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/876c3fb3-1bf8-4d9c-b48b-5cceea76e2cc)

&nbsp;Java 객체의 특성 때문에 실제로는 더 많은 오버헤드를 가지고 행 기반의 포맷을 가지고 있습니다.

<br>

**두 번째 문제에 대한 결론**
* Java 객체 대신 더 압축적이고 더 적은 오버헤드를 가지는 Data Format을 사용하자.

&nbsp;위의 두 가지 문제로 인해서 나온 것이 Dataframe와 Dataset입니다.

<br><br>

### DataSet/DataFrame은 무엇인가?

&nbsp;DataSet은 강력한 형식의 불변 객체이고 위에서 다루었던 2가지 문제에 대한 변화가 있습니다.
* 행 기반의 새로운 바이너리 포맷
* 데이터 스키마 등록

<br>

**행 기반의 새로운 바이너리 포맷**  
&nbsp;Java 객체의 오버헤드를 피하기 위해서 Spark는 행 기반의 새로운 바이너리 스토리지를 적용했습니다.

* 필드가 Primitive 타입이고 고정 길이라면 데이터는 특정 주소에 저장됩니다.
* 필드가 객체이고 가변 길이를 가지고 있다면 데이터의 길이와 데이터를 저장하고 해당 주소 (Offset)을 저장합니다.

<br>

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/953bcfdf-e8ae-4237-9ccd-1e42528028a5)

* 123 (빨간색): 고정 길이의 Primitive 타입이라 데이터 그대로 저장됨
* data (초록색): 가변 길이의 string으로 length가 4이고 data라는 데이터를 가졌으며 해당 위치(offset, 32L)을 알려주는 데이터로 구성됨
* bricks (하늘색): 가변 길이의 string으로 length가 6이고 bricks라는 데이터를 가졌으며 해당 위치(offset, 48L)을 알려주는 데이터로 구성됨.

<br>

**데이터 스키마 등록**  
&nbsp;DataSet은 스칼라에서 사용 가능한데 스키마를 등록해서 사용하기 위해 `케이스 클래스`를 이용하여 정의합니다.

```scala
// 스칼라 예제
// 정의한 케이스 클래스를 spark에서 "as"를 이용하여 적용
case class Student(id: Long, name: String, yearOfJoining: Long, depId: Long)
val students = spark.read.json(“/students.json").as[Student]
```

<br>

**성능 비교**

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/9a2e7890-30d6-4f25-9d08-4f89d4cdb264)
* Dataset이 RDD보다 메모리 사용량에서 75% 절감
* Dataset의 인코더가 Java 직렬화보다 20배 빠름

<br>

**결론. DataSet과 DataFrame은 무엇인가?**  
* 스키마와 함께 구조화된 객체
* 행 기반의 tungsten에 저장
* Encoder라고 불리는 DataSet API
* DataSet은 구조화된 객체지만 DataFrame은 DataSet[GenericRowObject]

<br>

## Part2. 행 기반의 Dataset에서 열 기반의 Parquet으로의 발전

* Spark 2.x에서는 컬럼 기반의 스토리지 포맷을 제공함.

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/944cc3eb-22f6-431f-999b-0261ad28644f)

### Columnar (열 기반) 스토리지
&nbsp;2세대 Tungsten 엔진에서는 런타임 속도를 향상시키기 위해 optimisation 기술들을 이용하려고 했고 그로 인해 `WholeStageCodeGeneration`과 `Vectorization`이라는 기술이 나왔습니다. 열 기반 스토리지로 인해 얻을 수 있는 것은 다음과 같습니다.

1. 열 기반 형식에서 데이터 접근이 더 규칙적입니다. 예를 들어 열이 integer라면 우리는 4바이트 간격으로 접근하면 됩니다.
2. 데이터의 성격이 동일하기 때문에 더 좋은 압축 방식을 이용하여 밀도 있는 스토리지로 사용할 수 있습니다.
3. Numpy나 Tensorflow처럼 고성능 기술을 보유한 시스템들도 열 기반을 사용하기 때문에 이러한 시스템과 호환이 잘됩니다.

<br>

# 결론
&nbsp;Spark는 RDD라는 Java 객체로 시작을 했지만 CPU와 메모리로 인한 병목현상이 늘어났고 이를 해결하기 위한 Tungsten 프로젝트를 진행했습니다. 이 프로젝트는 CPU와 메모리 사용에 대한 효율을 올려 성능을 이끌어내는 것이 목적이었고 RDD 객체를 사용했을 때의 두 가지 문제점 (불투명성, 스키마 추론)을 보완하기 위해 DataSet과 DataFrame을 이용했습니다. 이를 통해 투명성을 확보하고 스키마를 직관적으로 표현하여 성능을 향상시킬 수 있었습니다. Spark는 여기서 끝나지 않고 행 기반의 DataSet과 DataFrame을 열 기반의 구조화된 Parquet 방식으로 업그레이드했습니다. 열 기반의 스토리지는 데이터 접근이나 압축 측면에서 용이하기 때문에 이를 통해 성능을 더 향상시킬 수 있었고 현재로 오게 되었습니다. 