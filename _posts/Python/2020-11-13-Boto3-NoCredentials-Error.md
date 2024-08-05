---
title:  "[AWS] 파이썬 - ec2 연동 에러 (botocore.exceptions.NoCredentialsError: Unable to locate credentials)"
date:   2020-11-13 00:00:00 +0900
categories:
  - Python
tags:
  - Python
  - AWS
  - Boto3
toc: true
toc_sticky: true
toc_label: "Boto3 NoCredentialError"
header-img: https://user-images.githubusercontent.com/78892113/208301254-de81f938-1a15-4cae-a45e-74f87861000f.png
header-mask: true

---



&nbsp;안녕하세요 마개입니다.  
Python에서 AWS SDK를 통해 EC2를 제어하려고 합니다. 이때 boto3 라이브러리를 이용하는데 이를 이용하여 EC2를 제어하고 IP를 가져오려고 하는 과정에서 발생한 에러에 대해 보겠습니다.
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/208301254-de81f938-1a15-4cae-a45e-74f87861000f.png){: .align-center}{: width="60%" height="60%"} 


<br>

## 개발 내용

EC2 인스턴스의 경우 AWS Console에서 미리 생성을 해놓은 상태입니다. 샘플 코드이기 때문에 간단하게 작성해봅니다.

```python
import boto3
import os

if __name__ == "__main__":
    os.environ["AWS_DEFAULT_REGION"] = "ap-northeast-2" . # 서울 리전

    ec2 = boto3.resource("ec2")
    instance = ec2.Instance("제어하고자 하는 인스턴스ID")

    print(instance)
    print(instance.instance_id)
    print(instance.private_ip_address)
    print(instance.public_ip_address)
```

코드를 보면 간단합니다.  
EC2 인스턴스가 서울 리전에 존재하기 때문에 환경 변수에 리전을 설정해 줍니다. `boto3.resource("ec2")`를 통해 EC2 인스턴스를 보겠다는 것이고 초기화를 할 때 인스턴스 ID를 넣어 인스턴스를 가져옵니다.  
출력 부분에는 인스턴스에 대한 정보와 ID, private IP, public IP를 가져오는 것입니다.

<br><br>

## 에러

![image](https://user-images.githubusercontent.com/78892113/208301648-dcd0230a-c40c-4f49-b8d4-cd685f71463a.png)

코드를 실행시켜보면 위와 같이 에러가 발생한 것을 확인할 수가 있습니다.  
에러 내용을 봤을 때는 자격 증명이 되지 않았기 때문에 발생한 에러인 것을 확인할 수 있습니다. 

<br><br>

## 해결 방안

개발 환경은 Ubuntu에서 진행하고 있습니다. CLI를 설치하지 않았기에 따로 `credentials`를 지정해줘야 합니다. AWS에서는 해당 `credentials`를 `~/.aws/credentials` 경로로 바라봅니다. 그렇기에 해당 경로에 파일을 만들어줍니다.

```sh
vi ~/.aws/credentials
```

![image](https://user-images.githubusercontent.com/78892113/208301994-1c459601-d0b6-4444-b060-fedf10cd244f.png)

안에 들어갈 내용은 아래와 같습니다.

```
[Profile1]
aws_access_key_id = ACCESS KEY
aws_secret_access_key = SECRET ACCESS KEY
```

여기서 `Profile1`은 임의로 제가 지정한 이름입니다. 다른 것으로 변경해도 됩니다.  
아래에 `ACCESS KEY`부분과 `SECRET ACCESS KEY`의 경우 AWS `IAM`에서 만든 사용자에 대한 **KEY 정보**입니다. (사용자에게는 EC2에 대한 권한이 있는 사용자여야 합니다.) 이렇게 하고 저장을 합니다. 

<br>

```python
import boto3
import os

if __name__ == "__main__":
    os.environ["AWS_DEFAULT_REGION"] = "ap-northeast-2" . # 서울 리전
    os.environ["AWS_PROFILE"] = "Profile1"

    ec2 = boto3.resource("ec2")
    instance = ec2.Instance("제어하고자 하는 인스턴스ID")

    print(instance)
    print(instance.instance_id)
    print(instance.private_ip_address)
    print(instance.public_ip_address)
```

그런 후에 위의 코드를 수정합니다. 중간에 `os.environ["AWS_PROFILE"] = "Profile1"`를 추가하여 Profile을 credentials의 `Profile1`으로 바라보겠다는 뜻입니다.  
위와 같이 수정을 하고 실행해보면 에러 없이 실행이 잘 되는 것을 확인하실 수 있습니다.