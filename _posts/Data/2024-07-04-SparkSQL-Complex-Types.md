---
title:  "[SparkSQL] Complex Data Types"
date:   2024-07-04 00:00:00 +0900
categories:
  - Data
  - Spark
tags:
  - Data
  - Spark
toc: true
toc_sticky: true
toc_label: "Complex Types"
header-img: https://github.com/magaeTube/magaeTube.github.io/assets/78892113/33121d67-2121-4304-8bcc-a75ef78d592d
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
&nbsp;SparkSQL을 이용할 때 사용되는 Data Type 중에 단순한 구조가 아닌 복잡한 구조인 Data Type들에 대해 알아봅니다.
{: .notice--info}

<br>

![spark](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/10d59a6c-1be0-45b8-a755-74f43c85fa91){: .align-center}

<br><br>

## Complex Types
&nbsp;SparkSQL에서는 여러 Data Type을 제공하고 있습니다. 여기에서도 Numeric이나 String type도 있지만 복잡한 형태인 Complex type도 있습니다. 여기에는 `ArrayType`, `MapType`, `StructType`이 있는데 하나씩 알아보도록 합니다.

<br>

### ArrayType
&nbsp;ArrayType은 이름에서도 알 수 있듯이 배열 형태의 Data입니다. ArrayType은 element들을 가지는데 이 element들은 동일한 Data Type을 가집니다. 컬럼을 지정할 때는 `ARRAY<element_type>` 형태로 element의 type을 지정해서 선언해주면 됩니다. Array 타입은 또한 순서가 있는 형태로서 데이터를 가져오고자 할 때는 인덱스를 통해 접근하실 수 있습니다.

<br>

```sql
CREATE TABLE tbl_a (
    col_a ARRAY<STRING>
);

INSERT INTO tbl_a VALUES (ARRAY('a', 'b', 'c'));
SELECT col_a[1] FROM tbl_a;

```

&nbsp;위 예를 통해 알 수 있듯이 col_a를 element가 StringType인 ArrayType으로 생성하고 데이터를 추가하는 예제를 만들어보았습니다. 그리고 ArrayType은 순서가 정해져있는 형태이기 때문에 원하는 데이터에 접근할 때는 index를 이용해서 접근하면 됩니다.

<br>

### MapType
&nbsp;MapType은 Key-Value 쌍으로 이루어진 데이터 형태를 말합니다. 이는 Python에서의 Dict와 비슷한 형태를 나타내고 한 컬럼에서 Key-Value 형태로 저장할 수 있도록 합니다. 각 키는 고유하다는 것이 특징입니다. 컬럼을 지정할 때는 `MAP<Key Type, Value Type>`으로 지정하면 되고 특정 키의 값을 가져올 때는 `컬럼명['키']` 형태로 접근하면 됩니다.

<br>

```sql
CREATE TABLE tbl_a (
    col_a MAP<STRING, STRING> 
);

INSERT INTO tbl_a VALUES (MAP('age', '30', 'city', 'New York'));
SELECT col_a['age'] FROM tbl_a;

```

&nbsp;위 예시처럼 MAP 형태는 Key와 Value의 타입을 지정하고 그에 맞는 데이터를 추가하면 됩니다. 맞지 않는 타입의 데이터를 추가하려면 에러가 발생합니다. 

<br>

### StructType
&nbsp;StructType은 여러 필드를 가지는 복합 데이터 타입입니다. 각 필드에는 이름과 타입을 가지고 JSON과 같은 구조적인 형태를 나타낼 때 좋습니다. 컬럼을 지정할 때는 `STRUCT<Field Name: Field Type>`으로 지정하면 되고 특정 필드의 값을 가져올 때는 `컬럼명.필드명`처럼 점(.)으로 접근하면 됩니다.

<br>

```sql
CREATE TABLE tbl_a (
    col_a STRUCT<street: STRING, city: STRING, zip: INT>
);

INSERT INTO tbl_a VALUE(STRUCT('5th Avenue', 'New York', 10001));
SELECT col_a.city FROM tbl_a;
```

&nbsp;위 예시처럼 STRUCT 안에 여러 데이터 타입을 가지는 필드들을 지정하고 점(.)을 통해 원하는 데이터에 접근하면 됩니다.  
&nbsp;SparkSQL에서 사용될 수 있는 여러 Complex Type에 대해 알아보았는데 어느 것을 써야한다는 정답이 있는 것은 아니고 그때 상황에 맞춰서 맞는 타입을 이용하면 됩니다.