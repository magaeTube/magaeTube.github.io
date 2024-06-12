---
title:  "[EC2] Instance Type"
date:   2024-03-08 00:00:00 +0900
categories:
  - AWS
tags:
  - Cloud
  - AWS
  - EC2
toc: true
toc_sticky: true
toc_label: "Overview"
header-img: https://github.com/magaeTube/magaeTube.github.io/assets/78892113/8d37d5f9-202a-4978-bd53-26041f8cdd6c
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
&nbsp;AWS에서 인스턴스를 다루다보면 많은 인스턴스 타입이 있고 이중에 어떤 것을 써야할지에 대한 고민이 생길 수 있습니다. 이에 대해 정리를 해봅니다.
{: .notice--info}

<br>

![EC2](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/8d37d5f9-202a-4978-bd53-26041f8cdd6c){: .align-center}

<br>

# Naming Convention

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/819af885-d863-4938-b538-3ea6483cc6fd)

&nbsp;EC2는 위와 같은 형태로 인스턴스 타입을 제공하고 있습니다. 각 글자에는 의미를 담고 있는데 첫 글자부터 해서 `Instance family`, `Instance generation`, `Processor family`, `Additional capability`, `Instance size` 순서로 의미합니다.

<br>

## Instance Family

&nbsp;Instance Family에는 각 인스턴스가 어떠한 형태에 특화된 인스턴스인지 나타내고 있습니다. 인스턴스에 올라가는 서비스의 형태, CPU 아니면 메모리를 많이 사용하는지 등에 따라 맞춰 형태를 지정하면 됩니다.

* C : 컴퓨팅 최적화
* D : 고밀도의 스토리지
* F : FPGA
* G : 그래픽 최적화
* Hpc : 고성능 컴퓨팅
* I : 스토리지 최적화
* Im : vCPU 대 메모리 비율이 1:4로 최적화된 스토리지
* Is : vCPU 대 메모리 비율이 1:6으로 최적화된 스토리지
* Inf : AWS 추론
* M : 일반적인 목적
* Mac : macOS
* P : GPU 가속화
* R : 메모리 최적화
* T : 폭발적인 성능
* Trn : AWS 교육
* U : 높은 메모리
* VT : 비디오 트랜스코딩
* X : 메모리 집약적

<br>

## Processor Family

&nbsp;Processor Familes는 Processor 종류에 따라 다르게 나누었습니다.

* a - AMD 프로세서
* g - AWS Graviton 프로세서
* i - Intel 프로세서

<br>

## Additional Capability

&nbsp;그외에 추가적인 능력에 따라 부여되는 네이밍입니다.

* b - EBS 최적화
* d - 인스턴스 스토어 볼륨
* n - 네트워크와 EBS 최적화
* e - 추가적인 스토리지 또는 메모리
* z - 고성능
* q - Qualcomm 추론 가속기
* flex - Flex 인스턴스

<br>

## Instance Size

&nbsp;마지막으로 인스턴스 사이즈는 특화된 Instance Family에 따라 지정한 사이즈만큼 스펙이 달라지게 됩니다. nano, small, large, xlarge, 4xlarge 등등 여러 종류가 있으며 Instance Family마다 다르기 때문에 맞는 사양을 찾아서 지정하면 됩니다. 해당 부분은 <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#AvailableInstanceTypes">링크</a>에서 찾으실 수 있습니다.

<br>

# 참고 링크
* <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html">https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html</a>