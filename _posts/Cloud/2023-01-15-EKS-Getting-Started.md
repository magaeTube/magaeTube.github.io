---
title:  "[EKS] 시작해보기"
date:   2023-01-15 00:00:00 +0900
categories:
  - Cloud
tags:
  - Cloud
  - AWS
  - EKS
toc: true
toc_sticky: true
toc_label: "EKS 시작해보기"
header-img: https://user-images.githubusercontent.com/78892113/212542390-43b73348-b551-421b-af80-7a0cfdcfa7dc.png
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
AWS의 수많은 서비스들 중에서 EKS를 이용해서 Kubernetes Cluster를 구성해보고자 합니다. AWS EKS의 <a href="https://docs.aws.amazon.com/ko_kr/eks/latest/userguide/getting-started.html">공식 문서</a>를 보고 따라해보았습니다.
{: .notice--info}

<br>

![EKS](https://user-images.githubusercontent.com/78892113/212542390-43b73348-b551-421b-af80-7a0cfdcfa7dc.png){: .align-center}{: width="70%" height="70%"} 

<br><br>

# 개발 환경

이번 과정에서의 개발 환경은 아래와 같습니다.
* MacBook Pro 2021년 모델, Apple M1 Pro
* OS : macOS Monterey
* 메모리 : 32 GB

<br><br>

# kubectl 설치

저는 맥북에서 진행을 했는데 Docker Desktop이 있기 때문에 여기에서 **`Kubernetes`**를 실행시켰습니다. 맥북에서 실행시키는 방법은 <a href="https://magaetube.github.io/devops/Kubernetes-for-Mac/">링크</a>를 통해 확인하시길 바랍니다.

<br><br>

# eksctl 설치

EKS에서 **`Kubernetes`** 클러스터를 생성 및 관리하기 위해서는 **`eksctl`**이라는 command line tool을 이용합니다. 맥북에 eksctl을 설치하는데 Homebrew를 이용해서 설치합니다.

```sh
# Weaveworks Homebrew 탭 설치
$ brew tap weaveworks/tap
$ brew install weaveworks/tap/eksctl

# 버전 확인
$ eksctl version
0.125.0
```

<br><br>

# AWS CLI 구성

다음으로는 AWS 서비스를 사용하는데 필요한 command line tool인 **`AWS CLI`**를 설치하고 구성합니다.

<br>

## 설치

AWS에서 제공해주는 <a href="https://awscli.amazonaws.com/AWSCLIV2.pkg">링크</a>를 통해 파일을 다운로드하고 설치합니다.

```sh
# aws cli 위치 확인
$ which aws
/usr/local/bin/aws

# 버전 확인
$ aws --version
aws-cli/2.9.15 Python/3.9.11 Darwin/21.6.0 exe/x86_64 prompt/off
```

<br>

## 구성

구성을 하기 전에 앞서 IAM 계정에 **`AmazonEKSClusterPolicy`**가 존재해야 합니다. 이 정책이 설정되어있는지 먼저 확인한 후에 다음을 진행합니다.

구성을 할 때에는 2가지 방법이 있습니다.

<br>

### aws configure

명령어를 통해 설정합니다.

```sh
$ aws configure
AWS Access Key ID : 
AWS Secret Access Key :
Default region name : 
Default output format :
```

위와 같은 순서로 configuration을 설정하라고 나오는데 본인 `IAM 계정의 키`를 입력하면 됩니다.

<br>

### credentials

`aws configure`명령어로 설정하는 방법도 있지만 **`credentials`**을 이용하는 방법도 있습니다.

```sh
$ vim ~/.aws/credentials

[default]
aws_access_key_id=
aws_secret_access_key=
```

마찬가지로 본인 IAM 계정의 키를 입력하면 됩니다.

<br><br>

# EKS 시작

EKS의 클러스터를 생성해봅니다. 생성할 때는 **`eksctl`**을 이용하는 방법과 **`AWS Management Console`**을 이용하는 방법이 있는데 **`eksctl`**을 이용해서 진행해봅니다.  


클러스터를 배포할 때에는 2가지 유형이 있습니다.
* **Fargate - Linux** : **`AWS Fargate`**에 Linux 애플리케이션을 실행합니다. EC2 인스턴스를 관리하지 않고도 Kubernetes pods를 배포할 수 있는 컴퓨팅 엔진입니다.

* **Managed nodes - Linux** : **`EC2`** 인스턴스에서 Amazon Linux 애플리케이션을 실행하려면 이 유형을 선택합니다.
  
간단하게 EC2 환경에서 실행해보기 위해서 2번째 방법인 Managed nodes - Linux를 이용하겠습니다.

```sh
# 클러스터명과 리전을 선택합니다.
$ eksctl create cluster --name my-cluster --region region-code

# 완료되면 아래와 같은 메시지가 출력됩니다.
2023-01-15 16:04:43 [✔]  EKS cluster "test-cluster" in "ap-northeast-2" region is ready
```

위의 명령어를 실행하면 새로운 `VPC`, `CloudFormation`이 생성되고 EKS 클러스터도 생성이 됩니다. 몇분의 시간이 지나고 나면 완료됩니다.

각자의 피씨에는 `~/.kube/config`에 설정 내용들이 저장되어 피씨에서 kubectl로 관리할 수 있도록 세팅이 됩니다.

## 확인

```sh
# nodes 확인
$ kubectl get nodes -o wide
```


결과를 보면 EC2 2개의 정보가 노드로 있는 것을 확인하실 수 있습니다. 이는 EC2 인스턴스에서도 확인이 가능합니다. 현재 실행중인 pods도 확인이 가능합니다.

```sh
# pods 확인
$ kubectl get pods -A -o wide
```

<br><br>

# EKS 삭제

삭제를 할 때에는 **`eksctl`**명령어의 **`delete`**를 이용합니다.

```sh
# 삭제할 클러스터명과 리전을 선택합니다.
$ eksctl delete cluster --region=region-code --name=my-cluster

# 결과
2023-01-15 16:19:41 [ℹ]  will delete stack "eksctl-test-cluster-cluster"
2023-01-15 16:19:41 [✔]  all cluster resources were deleted
```

위와 같은 문구가 뜨면 정상적으로 삭제된 것을 알 수 있습니다.
