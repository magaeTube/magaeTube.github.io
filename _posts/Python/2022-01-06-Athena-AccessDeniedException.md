---
title:  "[Athena] AccessDeniedException 에러"
date:   2022-01-06 00:00:00 +0900
categories:
  - Python
tags:
  - Python
  - AWS
  - Athena
toc: true
toc_sticky: true
toc_label: "Athena AccessDeniedException"
header-img: https://user-images.githubusercontent.com/78892113/150356814-e44eff9f-4f60-41cf-88bc-b61a2d826b9b.png
header-mask: true
# header:
#   teaser: https://user-images.githubusercontent.com/78892113/150356814-e44eff9f-4f60-41cf-88bc-b61a2d826b9b.png

---



&nbsp;안녕하세요 마개입니다.  
Python에서 AWS의 `Athena`를 이용할 때 발생한 에러를 다뤄보고자 합니다.
{: .notice--info}

<br>

![99C825335A071CCB21](https://user-images.githubusercontent.com/78892113/150356814-e44eff9f-4f60-41cf-88bc-b61a2d826b9b.png){: .align-center}{: width="80%" height="80%"} 


<br>

# 에러 파악
&nbsp;Python에서 Athena를 이용하려고 할 때 `AccessDeniedException` 에러가 발생할 수 있습니다. 정확히는 아래 코드를 보도록 하겠습니다.

<br>

```python
import boto3

client = boto3.client('athena')
response = client.start_query_execution(
    QueryString='쿼리',
    QueryExecutionContext={
        'Database': 'DB명'
    },
    ResultConfiguration={
        'OutputLocation': 'Athena 쿼리 결과 스토리지 위치'
    }
)
...(생략)...
```

<br>

&nbsp;코드를 보면 Python에서 `Athena` 접근을 하기 위한 가장 간단한 사용 방법입니다. 그러나 이 부분을 실행하면 아래와 같은 에러 메시지가 발생할 수 있습니다.

<br>

```python
Traceback (most recent call last):
  File "/home/ksh/pythonProject/athena_test.py", line 38, in <module>
    response = client.start_query_execution(
  File "/home/ksh/pythonProject/venv/lib/python3.8/site-packages/botocore/client.py", line 386, in _api_call
    return self._make_api_call(operation_name, kwargs)
  File "/home/ksh/pythonProject/venv/lib/python3.8/site-packages/botocore/client.py", line 705, in _make_api_call
    raise error_class(parsed_response, operation_name)
botocore.exceptions.ClientError: An error occurred (AccessDeniedException) when calling the StartQueryExecution operation: User: arn:aws:iam::XXXXXXXX:user/trace is not authorized to perform: athena:StartQueryExecution on resource: arn:aws:athena:ap-northeast-2:XXXXXXXX:workgroup/primary because no identity-based policy allows the athena:StartQueryExecution action

```

<br>

&nbsp;에러 메시지를 보면 <u>IAM 유저 (trace)가 Athena의 StartQueryExecution을 사용할 권한이 없다</u>는 뜻입니다. 어찌 된 것인지 찾아보겠습니다.  

<br>

# 원인 분석 및 해결
AWS Console에 접속하여 `IAM`으로 이동합니다.  

<br>

![IAM](https://user-images.githubusercontent.com/78892113/149615149-8791db82-af7e-4900-9911-d4f5a791b388.png){: .align-center}{: width="40%" height="40%"} 
  
좌측 메뉴에서 `Users`로 이동합니다.

<br>

![Users](https://user-images.githubusercontent.com/78892113/149615233-1b8c2ba3-6f7e-432a-b249-0e306b86d03f.png){: .align-center}
  
<br>

&nbsp;Users에 유저가 여러 개라서 그중에 어떤 것인지 모르겠다면 위의 에러 메시지를 다시 살펴보면 `arn:aws:iam::XXXXXXX:user/...` 부분이 있습니다. 이 뒤에 있는 것이 해당 유저명입니다. 해당 유저를 찾았다면 해당 유저를 클릭합니다.
  
<br>

![Permissions](https://user-images.githubusercontent.com/78892113/149615369-e4b60697-23c1-44be-bf86-e2b0032f379f.png)

&nbsp;`Permissions` 탭으로 넘어와서 확인을 해보면 위와 같은 경우 S3에 대한 Access만 가능한 상태입니다. `Add permissions` 버튼을 클릭하여 <u>Athena에 대한 Access도 추가</u>합니다.

<br>

![Add](https://user-images.githubusercontent.com/78892113/149615447-aa575a5d-5f4e-4971-b947-a614dff82607.png)

&nbsp;Add permissions 버튼을 클릭하면 **Add user to group**, **Copy permissions from existing user**, **Attach existing policies directly**가 있는데 마지막 거를 선택하고 검색에 `athena`를 검색합니다. 검색 결과 중에 `AmazonAthenaFullAccess`를 선택하고 하단에 **Next:Review** 버튼을 클릭 후 내용을 확인 후 **Add permissions** 버튼을 클릭하여 마무리합니다.

<br>

![Final](https://user-images.githubusercontent.com/78892113/149615489-aae0b5eb-f84d-4558-92c6-33beb11effe3.png)

&nbsp;그러면 위와 같이 Athena 권한이 생성되었는지 확인합니다.  
(예제를 위해 FullAccess 권한으로 넣었지만 필요한 것들만 선택하여 Access 권한을 다루는 것이 더 좋습니다.) 완료가 되었으면 파이썬에서 다시 실행하여 이상 없이 진행되는지 확인합니다.

​