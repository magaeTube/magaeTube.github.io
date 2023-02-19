---
title:  "[SQS] Queue 생성하기"
date:   2022-09-28 00:00:00 +0900
categories:
  - Cloud
tags:
  - Cloud
  - AWS
  - SQS
toc: true
toc_sticky: true
toc_label: "SQS 시작해보기"
header-img: https://user-images.githubusercontent.com/78892113/219923597-904fac66-eb44-4b0c-8a90-46360d5b2123.png
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
SQS는 Simple Queue Service로 AWS에서 제공하는 Queue 서비스입니다. SQS를 생성하기 위해서 몇 가지 설정해야 하는 부분이 있는데 이에 대해 알아보겠습니다.
{: .notice--info}

<br>

![SQS](https://user-images.githubusercontent.com/78892113/219923597-904fac66-eb44-4b0c-8a90-46360d5b2123.png){: .align-center}{: width="70%" height="70%"} 

<br><br>

# SQS 생성

![SQS](https://user-images.githubusercontent.com/78892113/219924851-735b7b2e-73d8-42fe-9632-b49ed2825e7d.png)

AWS 콘솔에 접속해서 SQS서비스로 들어오면 위와 같은 모습입니다. 여기서 우측 상단의 **`Create queue`**를 클릭합니다.

<br>

## Details

![image](https://user-images.githubusercontent.com/78892113/219925112-622d9dce-7ad7-4be1-a3af-179aff85b11f.png)

그러면 위와 같은 Details를 설정해야 합니다. 이에 대해 봅니다.

* **Type** : Queue의 타입을 결정합니다.
  * **Standard** : 데이터의 순서를 보장하지 않는 방식으로 Throughput을 극대화합니다. 
    * 장점
      * 무제한에 가까운 메시지 전송
      * 제한이 없는 TPS
      * 최소 1회 전달 보장 (At-Least-Once-Delivery)
    * 단점
      * 메시지 순서 보장 안됨
      * 반드시 1번만 읽기 보장 안됨
  * **FIFO** : 선입선출 방식으로 먼저 들어온 데이터가 먼저 나갑니다.
    * 장점
      * 메시지 순서 보장
      * 1번의 전송, 1번의 수신으로 중복 수신을 방지함 (Exactly-Once Processing)
      * 초당 300TPS 제한 존재 (Limited Throughput)
    * 단점
      * 순서를 위해 속도가 느림 (초당 300TPS)
* **Name** : Queue의 이름을 정해줍니다.

<br>

위의 Standard와 FIFO에 대해 그림으로 비교하면 아래와 같습니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/219926276-05777f61-eaa5-4bd7-bb47-b211958be957.png)

<br>

## Configuration

![image](https://user-images.githubusercontent.com/78892113/219926370-8091d0f6-cdb9-4df9-a33a-9e843ec039ef.png)

다음으로는 설정 값들에 대해 확인합니다.

* **Visibility timeout** : 하나의 consumer로부터 수신받은 메시지를 다른 consumer에게 보이지 않도록 하는 시간을 설정합니다. 0초부터 12시간까지 설정이 가능합니다. (Default : 30초)
* **Message retention period** : 메시지의 보존 시간으로 1분부터 14일까지 보존할 수 있습니다. (Default : 4일)
* **Delivery delay** : 각 메시지를 consume할 때마다의 Delay를 설정합니다. 0초에서 15분까지 설정이 가능합니다. (Default : 0초)
* **Maximum message size** : 메시지의 최대 사이즈로 1 KB에서 256 KB 사이만 설정이 가능합니다. 용량이 큰 데이터를 이용할 때에는 SQS가 맞지 않습니다. (Default : 256 KB)
* **Receive message wait time** : Polling할 때 메시지를 수신받을 수 있도록 기다리는 시간을 설정합니다. 0~20초로 설정이 가능합니다. (Default : 0초)

<br>

## Encryption

![image](https://user-images.githubusercontent.com/78892113/219928885-0159046a-7cc9-4f9e-a74b-69f555e749ae.png)

암호화에 관련된 설정을 합니다. **`SQS 키`**나 **`KMS`**와 같은 암호화 관련된 것을 이용 중이라면 설정하도록 합니다.

<br>

## Access Policy

![image](https://user-images.githubusercontent.com/78892113/219929066-7f9326ff-fe2c-4a8b-9150-305a9e3ad04a.png)

여기서는 해당 Queue에 접근하고자 하는 정책을 정의합니다. **`Basic`**을 이용해서 간단하게 지정할 수도 있고 **`Advanced`**를 이용해서 직접 권한을 상세하게 정의할 수 있습니다. **`Advanced`**를 이용해서 정의를 해봅니다.

<br>

```json
{
  "Version": "2012-10-17",
  "Id": "Policy1644481158786",
  "Statement": [
    {
      "Sid": "Stmt1644481154611",
      "Effect": "Allow",
      "Principal": {
        "AWS": "IAM 계정 Arn 정보"
      },
      "Action": [
        "sqs:DeleteMessage",
        "sqs:ChangeMessageVisibility",
        "sqs:ReceiveMessage",
        "sqs:SendMessage",
        "sqs:GetQueueAttributes"
      ],
      "Resource": "Queue Arn 정보"
    }
  ]
}
```

**`Action`**을 보면 SQS를 이용한 기능들을 제한하였습니다. 이 외에도 필요한 기능들이 있으면 이 부분에 추가하면 됩니다. **`Resource`**에는 원하는 Queue의 Arn 주소를 입력하면 됩니다.

<br>

## 그외

![image](https://user-images.githubusercontent.com/78892113/219930137-e74e15ef-b673-4389-aa0f-cb6f6390fe4f.png)

그외 설정은 선택적이기 때문에 필요하면 작업하고 Queue를 생성하면 됩니다.