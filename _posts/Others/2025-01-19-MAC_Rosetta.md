---
title:  "[macOS] Intel 전용 앱을 Apple Silicon에서 사용하려면 - Rosetta"
date:   2025-01-19 00:00:00 +0900
categories:
  - Others
tags:
  - MAC
  - macOS
  - Rosetta
toc: true
toc_sticky: true
toc_label: "Rosetta"
header-mask: true
---

&nbsp;안녕하세요 마개입니다.  
이번 M4 Pro로 맥북을 교체하고 개발환경을 구성하면서 발생한 메시지에 대해 알아봅니다.
{: .notice--info}

## 현상
  
![image](https://github.com/user-attachments/assets/b7a3b8a5-b5de-4812-8722-31d938007a77)
&nbsp;AWS CLI을 설치하기 위해 설치 패키지를 다운로드하고 실행할 때 위와 같은 메시지가 발생했습니다. `Rosetta??` CLI을 설치할 떄 이런 것이 있었나..?하면서 무엇인지 찾아보기 시작했습니다.

<br><br>

## Rosetta
&nbsp;위의 메시지를 잘 보면 `Rosetta를 사용하여 Apple Silicon Mac에서 Intel 기반 기능을 실행할 수 있습니다.`라고 되어있는 걸 보니 제 맥북은 Apple Silicon인데 지금 Intel 기반 앱을 설치하려고 하는구나라고 생각할 수 있었습니다. 그에 따라 Apple 공식에서 설명하는 것을 보겠습니다.

<br>

![image](https://github.com/user-attachments/assets/7c558fba-af27-488e-a891-b11c0f88c277){: .align-center}{: width="70%" height="70%"} 
  
&nbsp;공식에서도 알 수 있듯이 Intel 프로세서로 제작된 앱을 Apple Silicon이 장착된 Mac에서 사용하려면 `Rosetta 2`가 필요하다고 합니다. 설치는 따로 프로그램을 다운로드 받는 것이 아니라 Intel 기반 앱을 실행하면 자동으로 위와 같이 팝업이 뜨는데 이때 설치하면 됩니다.  
&nbsp;사용하는 방법도 따로 있는 것이 아니라 백그라운드에서 자동으로 실행되는 방식입니다.

<br>

## 설치
&nbsp;처음에 봤던 이미지에서 `설치`를 눌러 실행합니다.
  
![image](https://github.com/user-attachments/assets/f5aac830-94d8-4d5f-b01f-a85a8f8a17e5){: .align-center}{: width="50%" height="50%"} 
  
&nbsp;위와 같이 admin 권한으로 실행해야되기 때문에 암호를 입력합니다.
  
![image](https://github.com/user-attachments/assets/00a4c8b7-cdb8-4ff3-a310-f3771857cee0)
  
&nbsp;그 다음에는 쭉쭉 넘어가면서 설치합니다. 설치가 완료되면 해당 프로그램은 Rosetta를 통해 사용되는 상태가 됩니다.