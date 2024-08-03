---
title:  "[Pandas] For 문 이용 시 속도 개선 방법"
date:   2022-11-20 00:00:00 +0900
categories:
  - Python
tags:
  - Python
  - Pandas

toc: true
toc_sticky: true
toc_label: "For 문 속도 개선"
header-mask: true
---



&nbsp;안녕하세요 마개입니다.  
Pandas의 DataFrame을 이용할 때 For 문을 이용하는 것은 지양해야 하지만 어쩔 수 없이 사용해야 할 때가 있습니다. 이럴 때 For 문을 잘못 사용하면 속도 저하를 불러오는데 여러 가지 방법으로 속도를 비교해 보겠습니다.
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/184921564-bf88da6d-c519-4276-9157-8b12bbd89fcb.png)

<br><br>


## 데이터 생성
---

데이터는 간단하게 학생들의 성적에 수우미양가를 측정하는 데이터로 진행을 해보겠습니다.

```python
import numpy as np
import pandas as pd

rows = 1_000_000
df_students = pd.DataFrame([(f'student_{i}', int(p * 100)) for i, p in enumerate(np.random.rand(rows))], columns=['student', 'score'])
df_students
```

<br>

![image](https://user-images.githubusercontent.com/78892113/203900262-8ec3776a-72ec-47ac-a55d-10075fa1cb05.png){: .align-center}{: width="80%" height="80%"}


학생 수는 간단하게 100만 명으로 하여 1~100점 사이의 랜덤한 스코어로 지정합니다. 이제 여기서 점수에 맞는 등급을 나누는 작업을 하겠습니다. ​

* 91~100 : 수
* 81~90 : 우
* 71~80 : 미
* 61~70 : 양
* 51~60 : 가


위와 같은 기준으로 등급표를 나누고 이를 DataFrame에 여러 방법으로 적용해 보겠습니다.

<br><br>

## 등급 나누기
---

For 문을 이용하기 전에 먼저 위의 기준으로 된 등급표를 만듭니다.

```python
def apply_grade(score: int):
    if 90 < score <= 100:
        grade = '수'
    elif 80 < score <= 90:
        grade = '우'
    elif 70 < score <= 80:
        grade = '미'
    elif 60 < score <= 70:
        grade = '양'
    else:
        grade = '가'
    return grade
```

점수에 맞게 등급을 나누는 함수를 만들었다면 이제 For 문을 하나씩 사용해 보겠습니다.

​
<br><br>

### 단순 For Loop

첫 번째로는 단순하게 `For 문`을 이용하는 방법입니다.  
파이썬의 `range` 함수와 Pandas의 `iloc` 함수를 이용하는 방법입니다.

```python
def apply_grade_loop(df: pd.DataFrame):
    list_grade = []
    for i in range(len(df)):
        grade = apply_grade(score=df.iloc[i]['score'])
        list_grade.append(grade)
    df['grade'] = list_grade
```

<br>

### iterrows

이번에는 Pandas의 `iterrows` 메서드를 이용해서 해보겠습니다.

```python
def apply_grade_iterrows(df: pd.DataFrame):
    list_grade = []
    for _, row in df.iterrows():
        grade = apply_grade(score=row['score'])
        list_grade.append(grade)
    df['grade'] = list_grade
```

`iterrows` 메서드는 DataFrame를 (index, row) 형태의 tuple로 반환합니다. 그렇기에 row의 score을 이용합니다. 

<br>

### itertuples

iterrows에 이어 `itertuples`를 이용해보겠습니다.

```python
def apply_grade_itertuples(df: pd.DataFrame):
    list_grade = []
    for row in df.itertuples():
        grade = apply_grade(score=row.score)
        list_grade.append(grade)
    df['grade'] = list_grade
```

`itertuples`는 namedtuple 형태로 출력을 하는데 이는 **collections 모듈로부터 나오는 데이터 구조**입니다. 일반적으로 itertuples가 iterrows보다 빠르다고 합니다.

<br>

### apply

이번에는 `apply` 함수를 이용하는 방법입니다.

```python
def apply_grade_withapply(df: pd.DataFrame):
    df['grade'] = df.apply(lambda row: apply_grade(score=row['score']), axis=1)
```

`apply`는 **callable 함수**를 만들어 전체 row나 column에 적용할 수 있는 함수인데  iterrows에 비해 성능을 향상시킬 수 있습니다. 

다만 위와 같이 할 경우에는 문제가 있을 수 있습니다. `apply`는 내부적으로 Cython의 iterator를 이용하여 loop를 시도하는 데 lambda를 이용하면 Cython에서 처리되지 않고 단순히 파이썬을 부르기 때문에 결과론적으로는 엄청 빠른 것은 아닙니다.

<br>

​
### isin

위에서 사용된 것들은 하나의 값에 대한 로직이 있을 때 효율적인데 이번에는 벡터화된 작업의 예시를 알아봅니다. 이때 Pandas의 `isin` 메소드를 이용해서 하는 방법을 파악해봅니다.

```python
def apply_grade_isin(df: pd.DataFrame):
    A = df['score'].isin(range(91, 100))
    B = df['score'].isin(range(81, 91))
    C = df['score'].isin(range(71, 81))
    D = df['score'].isin(range(61, 71))
    E = df['score'].isin(range(1, 61))
    
    df.loc[A, 'grade'] = '수'
    df.loc[B, 'grade'] = '우'
    df.loc[C, 'grade'] = '미'
    df.loc[D, 'grade'] = '양'
    df.loc[E, 'grade'] = '가'
```

<br>

### pd.cut

pandas의 `cut`은 연속형 데이터를 범주형 데이터로 변환시켜주는데 같은 길이로 구간을 나눌 때 사용하는 메서드입니다.

```python
def apply_grade_cut(df: pd.DataFrame):
    df['grade'] = pd.cut(x=df['score'], bins=[1, 60, 70, 80, 90, 100], labels=['가', '양', '미', '우', '수'], include_lowest=True, right=True)
```

여기에서 bins를 통해 범위를 지정할 수 있습니다. 그 범위 안에 포함되는 것들은 labels를 통해 지정할 수 있습니다. 

<br>

### pd.digitize

`digitize`도 cut과 마찬가지로 연속형 데이터를 범주형 데이터로 변환하는 메서드입니다. 

```python
def apply_grade_digitize(df: pd.DataFrame):
    list_grade = np.array(['가', '양', '미', '우', '수'])
    bins = np.digitize(df['score'].values, bins=[60, 70, 80, 90, 100])
    df['grade'] = list_grade[bins]
```

digitize는 cut과 다른 점은 pandas가 아닌 **numpy를 이용하는 것**입니다. 마찬가지로 구간을 나눠서 매핑하는 기능을 제공합니다.

<br>

​
### np.where

numpy의 `where`은 조건에 따라 True 일 때와 False 일 때의 값들을 가져오는 메서드입니다. 사용법은 아래와 같습니다.

```python
np.where(조건, 조건이 True일 때, 조건이 False일 때)
```

```python
def apply_grade_numpy(df: pd.DataFrame):
    df["grade"] = np.where((df["score"] > 90) & (df["score"] <= 100), "수", 
                           np.where((df["score"] > 80) & (df["score"] <= 90), "우",
                           np.where((df["score"] > 70) & (df["score"] <= 80), "미",
                           np.where((df["score"] > 60) & (df["score"] <= 70), "양",
                           "가"
                          ))))
```

조건이 많을 경우 where을 사용하는 것은 위와 같이 복잡한 모습을 보일 수 있습니다.

<br>

### to_numpy

```python
def apply_grade_numpy_array(df: pd.DataFrame):
    list_grade = []
    for row in df.to_numpy():
        list_grade.append(apply_grade(row[1]))
    df["grade"] = list_grade
```

`to_numpy`는 pandas의 데이터 프레임을 numpy의 darray로 변경하는 것입니다. 변경을 하고 그것으로 for 문을 실행하는 것이기 때문에 속도 측면에서 뛰어난 효과를 볼 수 있습니다. 

다만 row의 특정 컬럼에 접근하기 위해서는 컬럼명이 아니라 인덱스 번호를 작성해야 한다는 것이 차이점입니다.

<br><br>

## 속도 측정
---

위에서 만들었던 여러 방법들에 대해 속도 측정을 해봅니다.

```python
level_all_algo = []
for algo in ['loop', 'iterrows', 'itertuples', 'withapply', 'isin', 'cut', 'digitize', 'numpy', 'numpy_array']:
    print(f'[{algo}]')
    %time globals()[f'apply_grade_{algo}'](df=df_students)
    level_all_algo.append(df_students['grade'].values)
```

위의 코드를 작성하여 실행했을 때 별다른 에러 메시지가 없으면 결과를 확인할 수 있습니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/203901102-ac9cf455-4821-4c6a-9c2d-7dadb1e88765.png){: .align-center}{: width="80%" height="80%"}

결과를 보면 가장 빠른 것은 cut이 나오고 그다음으로는 np.where과 digitize인 것을 확인할 수 있습니다. 각자의 피씨 환경에 따라 다르지만 비슷하게 나오는 것을 확인하실 수 있고 상황에 따라 맞는 메서드를 이용하면 됩니다.