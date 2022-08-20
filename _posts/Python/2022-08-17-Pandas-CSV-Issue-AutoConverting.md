---
title:  "[Pandas] CSV 파일 다룰 때 숫자 타입 이슈 문제"
date:   2022-08-17 00:00:00 +0900
categories:
  - Python
tags:
  - Python
  - Pandas

header-mask: true
---



&nbsp;안녕하세요 마개입니다.  
Pandas를 이용해서 CSV파일을 다룰 때 str 타입으로 저장했던 데이터가 int 타입으로 변경이 되는 경우가 있습니다. 이러한 상황에 대해 알아보고 원인과 해결 방안을 보겠습니다.
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/184921564-bf88da6d-c519-4276-9157-8b12bbd89fcb.png)

<br>

바로 예를 들어보겠습니다.

```python
import pandas as pd

df = pd.DataFrame({
    "code": ["01234", "02345", "03456"],
    "name": ["a", "b", "c"]
})

print(df)
print(df.info())

df.to_csv("test.csv", index=False)


# 결과
'''
    code name
0  01234    a
1  02345    b
2  03456    c
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 3 entries, 0 to 2
Data columns (total 2 columns):
 #   Column  Non-Null Count  Dtype 
---  ------  --------------  ----- 
 0   code    3 non-null      object
 1   name    3 non-null      object
dtypes: object(2)
memory usage: 176.0+ bytes
None
'''
```

<br>

위와 같은 코드를 작성합니다. 코드의 내용은 간단하게 "01234", "02345", "03456"라는 값을 가진 **`code 컬럼`**이 있고 code컬럼에 해당하는 이름값을 가진 **`name 컬럼`**으로 데이터 프레임을 만든 후에 CSV 파일로 저장하는 코드입니다. 

중간에 `print`를 통해 데이터 프레임의 데이터와 정보를 출력했는데 **`code`** 컬럼의 값은 맨 앞에 '0'이 붙어있고 `object` 타입인 것을 확인할 수 있습니다.

<br>

이제 반대로 이 CSV 파일을 읽어서 데이터 프레임을 만들어보겠습니다.

```python
import pandas as pd

df = pd.read_csv("test.csv")
print(df)
print(df.info())


# 결과
'''
   code name
0  1234    a
1  2345    b
2  3456    c
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 3 entries, 0 to 2
Data columns (total 2 columns):
 #   Column  Non-Null Count  Dtype 
---  ------  --------------  ----- 
 0   code    3 non-null      int64 
 1   name    3 non-null      object
dtypes: int64(1), object(1)
memory usage: 176.0+ bytes
None
'''
```

기본적인 방법으로 **`read_csv`**를 이용해서 데이터를 읽어오고 출력하였습니다.

결과를 보면 code 컬럼들의 값은 앞의 '0'이 날라가면서 int 타입으로 변경이 된 것을 확인하실 수 있습니다. 이는 비록 str 타입의 데이터로 저장이 되었지만 데이터를 불러올 때 code 칼럼에는 Digit Character만 있기 때문에 int 타입으로 인식해서 발생한 문제입니다. 이러한 문제를 방지하기 위해 두 가지 방법을 보겠습니다.

<br><br>

# Converts 이용

Pandas의 **`read_csv`** 파라미터를 보면 **`converters`**라는 파라미터가 있습니다. **`converters`**는 특정 컬럼의 값들을 변환시키는 함수를 적용하는 파라미터입니다. read_csv 파라미터들에 대한 상세한 설명은 <a href="https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html">URL</a>을 참고해 주세요.

이 파라미터를 이용해서 code 컬럼의 값들을 **`str`** 타입으로 변환시키는 것입니다. 예제로 보겠습니다.

```python
import pandas as pd

df = pd.read_csv("test.csv", converters={"code": lambda x: str(x)})
print(df)
print(df.info())


# 결과
'''
    code name
0  01234    a
1  02345    b
2  03456    c
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 3 entries, 0 to 2
Data columns (total 2 columns):
 #   Column  Non-Null Count  Dtype 
---  ------  --------------  ----- 
 0   code    3 non-null      object
 1   name    3 non-null      object
dtypes: object(2)
memory usage: 176.0+ bytes
None
'''
```

위의 코드와 같이 **`read_csv`** 메서드 안에 **`converters`** 파라미터를 추가하고 lambda 함수를 이용해서 code 컬럼의 값들을 **`str`** 타입으로 변환하였습니다. 결과를 보면 code 값에는 앞에 0 이 보존되어 있고 dtype도 **`object`** 타입으로 나타나는 것을 보실 수 있습니다.

<br><br>

# dtype 이용

두 번째 방법으로는 **`read_csv`** 메서드의 **`dtype`** 파라미터를 이용하는 방법입니다. **`dtype`** 파라미터는 read_csv 메서드를 통해 데이터를 불러올 때 컬럼들의 dtype을 미리 정의하는 방법입니다. 미리 정의한 후에 CSV 파일을 불러오기 때문에 지정한 타입들로 데이터를 불러올 수 있는 것입니다. 예제로 보겠습니다.

```python
import pandas as pd

dict_dtype = {
    "code": str,
    "name": str
}
df = pd.read_csv("test.csv", dtype=dict_dtype)
print(df)
print(df.info())


# 결과
'''
    code name
0  01234    a
1  02345    b
2  03456    c
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 3 entries, 0 to 2
Data columns (total 2 columns):
 #   Column  Non-Null Count  Dtype 
---  ------  --------------  ----- 
 0   code    3 non-null      object
 1   name    3 non-null      object
dtypes: object(2)
memory usage: 176.0+ bytes
None
'''
```

dtype은 **`dict`** 자료형으로 각 컬럼마다 타입을 설정합니다. 그 후에 read_csv에서 **`dtype`** 파라미터를 통해 호출하면 데이터가 원하는 형태로 불러오는 것을 확인하실 수 있습니다.

CSV 파일을 불러올 때 '0'이 사라지는 문제로 꽤나 골치 아팠는데 위와 같이 두 가지의 방법을 상황에 맞게 이용해서 해결할 수 있었습니다.

{% if page.comments %}
<div id="post-disqus" class="container">
{% include disqus.html %}
</div>
{% endif %}