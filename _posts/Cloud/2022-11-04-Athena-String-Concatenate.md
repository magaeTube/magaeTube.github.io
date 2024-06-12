---
title:  "[Athena] String 이어붙이기 사용 시 유의 사항"
date:   2022-11-04 00:00:00 +0900
categories:
  - AWS
tags:
  - Cloud
  - AWS
  - Athena
toc: true
toc_sticky: true
toc_label: "String 이어붙이기"
header-mask: true
---

&nbsp;안녕하세요 마개입니다.  
AWS의 Athena나 MySQL 또는 다른 데이터베이스의 SQL을 이용하여 String 값을 이어붙이기할 때 유의해야할 사항에 대해 알아봅니다.
{: .notice--info}

<br><br>

Query에서 String 값을 가진 여러 컬럼을 이어붙일 때 여러 방법을 이용할 수 있습니다. 이때 조심해야 하는 것에 대해 알아보겠습니다. 여기서는 `Athena`를 이용하여 진행해보았습니다.

<br>

```sql
SELECT 'a', 'b', 'c'
```

![image](https://user-images.githubusercontent.com/78892113/200019308-f6289175-2cd9-4ad8-bedc-a5b0a0aa63c9.png){: .align-center}

예를 들어 a, b, c라는 값을 가진 컬럼이 3개가 있다고 가정합니다.  
이 3가지 컬럼에 콤마(,)까지 추가하여 이어붙여보겠습니다.

<br><br>

# 이어붙이기 3가지 방법
---

## 연산자 `||` 활용 

첫 번째로는 `||` 연산자를 이용하는 방법입니다. MySQL에서 `||` 연산자는 논리연산자로 결괏값이 0, 1, NULL 중에 출력이 되지만 Athena는 `Presto` 기반에 **ANSI SQL**을 지원하고 있습니다. 이때의 `||` 연산자는 문자열이나 배열을 연결하는 역할을 합니다.

<br>

```sql
SELECT 'a' || ', ' || 'b' || ', ' || 'c'
```

![image](https://user-images.githubusercontent.com/78892113/200019797-dc45b125-7e22-428b-9d80-fb7e15a53556.png){: .align-center}

이 `||` 연산자를 이용해서 합치려고 하는 칼럼들 사이에 콤마를 기입하면 결과가 위와 같이 "a, b, c"로 잘 합쳐진 것을 확인할 수 있습니다.

<br>

## concat 활용

다음으로는 `concat` 함수를 이용하는 방법입니다. `concat`은 `||` 연산자와 마찬가지로 **여러 문자열 또는 배열을 연결**하는 역할을 합니다. concat 함수 안에 , (콤마)를 기준으로 값들을 나열하면 됩니다.

<br>

```sql
SELECT CONCAT('a', ', ', 'b', ', ', 'c')
```

![image](https://user-images.githubusercontent.com/78892113/200020324-bd97bd13-a031-428f-93df-1e372bbe0c7e.png){: .align-center}

`concat` 안에는 합치고자 하는 문자열과 칼럼 값을 넣어주면 됩니다. 

<br>

## concat_ws 활용

마지막으로는 `concat_ws` 함수를 이용하는 방법입니다. `concat_ws`는 concat과 다른 점은 concat은 단순히 () 안에 있는 값들을 연결하는 방법이지만 `concat_ws`는 **여러 값들을 구분자와 함께 연결**하는 방법을 제공합니다.

<br>

```sql
SELECT CONCAT_WS(', ', 'a', 'b', 'c')
```

![image](https://user-images.githubusercontent.com/78892113/200020668-78a0d4e9-bfdd-4f5b-a1a7-351d97915728.png){: .align-center}

`concat_ws` 안에 첫 번째 인수로는 `구분자`, 그 뒤로는 `배열` 또는 `값`들을 입력하면 됩니다. 마찬가지로 결과는 제대로 나오는 것을 확인할 수 있습니다. 

<br><br>

# 유의사항
---

정상적인 데이터들에 대해서는 위의 방법들이 큰 문제 없이 사용될 수 있는 것을 볼 수 있습니다. 하지만 만약에 `NULL` 값이 같이 있는 경우에는 어떻게 될까요??

NULL 값을 같이 넣어서 위의 3가지 방법을 이용해 보겠습니다.

<br>

## || 연산자 활용

<br>

```sql
SELECT 'a' || ', ' || null || ', ' || 'c'
```

![image](https://user-images.githubusercontent.com/78892113/200021091-6b7d8317-99ed-4c2c-be40-14304ad6e349.png){: .align-center}

중간 값에 `NULL` 값을 넣으면 위와 같이 에러가 발생되거나 

<br>

```sql
SELECT 'a' || NULL || 'b'
```

![image](https://user-images.githubusercontent.com/78892113/200021224-53a50c11-a632-4a2d-b589-3aaa7bd6244e.png){: .align-center}

위와 같이 분명히 'a'와 'b'값이 있음에도 불구하고 빈값이 출력되는 것을 확인할 수 있습니다.

<br>
​

## concat 활용

다음으로 concat을 이용해서 보겠습니다.

<br>

```sql
SELECT CONCAT('a', ', ', NULL, ', ', 'c')
```

![image](https://user-images.githubusercontent.com/78892113/200021436-69330ea9-7ce5-4bed-bd35-f7f1114a3d33.png){: .align-center}

`concat`의 경우에도 중간에 `NULL` 값이 있으면 결과가 빈값으로 나오는 것을 볼 수 있습니다.

<br>

## concat_ws 활용

마지막으로 concat_ws 함수를 이용해 봅니다.

<br>

```sql
SELECT CONCAT_WS(', ', 'a', NULL, 'c')
```

![image](https://user-images.githubusercontent.com/78892113/200021760-1621711e-41c9-4f3b-89e8-7432a56f5a5d.png){: .align-center}

`concat_ws`의 경우 여러 값들을 구분자 기준으로 합치기 때문에 NULL 값은 무시하고 제대로 출력된 것을 보실 수 있습니다.

​

위에서 예제로 본 것처럼 어떤 함수를 어떻게 사용하느냐에 따라 값은 다르게 나옵니다. 그렇기 때문에 상황에 맞춰서 알맞은 방법을 이용해서 개발해야 합니다.