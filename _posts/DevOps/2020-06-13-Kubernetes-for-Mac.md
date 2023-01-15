---
title:  "[Kubernetes] Docker for Mac에서 쿠버네티스 설정하기"
date:   2020-06-13 00:00:00 +0900
categories:
  - DevOps
tags:
  - DevOps
  - Kubernetes
toc: true
header-img: https://user-images.githubusercontent.com/78892113/212546158-8d876bf1-fbb1-479a-b303-3a37d0fa0511.png
header-mask: true
---



&nbsp;안녕하세요 마개입니다.  
개인 로컬 피씨에 Kubernetes를 설치해보기 위해 작업을 진행해보겠습니다. 개인 피씨는 맥북이고 기존에 Docker Desktop을 이미 설치한 상태입니다. 이를 이용해 Kubernetes를 설정해보겠습니다.
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/212546158-8d876bf1-fbb1-479a-b303-3a37d0fa0511.png){: .align-center}{: width="80%" height="80%"} 

<br>

# 설치 

![image](https://user-images.githubusercontent.com/78892113/212546403-4ad0b375-d6a6-411d-8a8b-2b7ed1c55a03.png){: .align-center}

Docker Desktop으로 Kubernetes를 설정하는 것은 생각보다 간단합니다. Docker Desktop을 설치하고 이용하면 우측 상단에 `Docker` 모양을 찾을 수 있습니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/212546454-533abfdf-07e5-41dd-b1ab-ebcf518c6d81.png){: .align-center}{: width="20%" height="20%"}


이것을 클릭하여 **`Preferences`** 메뉴를 클릭합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/212546496-43b95588-12e5-4637-b4de-efbd9f59bf59.png){: .align-center}

선택을 하면 위와 같은 팝업창이 뜨는데 여기서 **`[Kubernetes]`**를 클릭합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/212546505-116f87ac-2ef1-4b48-99ab-4b6f59e8d7c2.png){: .align-center}

**`[Kubernetes]`** 메뉴로 넘어오면 여기에서 
* Enable Kubernetes : Kubernetes 활성화 
* Deploy Docker Stacks to Kubernetes by default : 기본 설정으로 Docker Stacks Deploy 작업을 Kubernetes로 설정, 체크 안 하면 도커 스웜이 설정됨

두 가지를 선택하고 **`Apply & Restart`**를 누르면 일정 시간이 지나고 세팅이 완료됩니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/212546510-0aec8be5-0bbd-4cf7-8436-f6c9ef8f6b2c.png){: .align-center}

완료가 되면 아래와 같은데 좌측 하단에 **`Kubernetes running`**이라고 되어있는 것을 확인할 수 있습니다. 설정은 완료되었으니 다음 작업을 진행해보겠습니다.