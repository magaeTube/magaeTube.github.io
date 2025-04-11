---
title:  "[DeepSeek] 로컬에 DeepSeek R1 모델 구축하기"
date:   2025-04-10 00:00:00 +0900
categories:
  - AI
  - DeepSeek
tags:
  - AI
  - DeepSeek
  - R1
toc: true
toc_sticky: true
toc_label: "Setup DeepSeek R1"
header-img: 
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
&nbsp;DeepSeek R1 모델에 대해서 로컬에 구축할 수 있는데 구축하는 과정을 기록해봅니다.   
{: .notice--info}

<br>

![DeepSeek](https://github.com/user-attachments/assets/56ed3a0e-9ffb-43c8-b32f-77089315122b){: .align-center}{: width="70%" height="70%"} 

<br><br>

## DeepSeek R1
&nbsp;구축하기 전에 앞서 `DeepSeek R1` 모델에 대해서 보자면 DeepSeek R1은 `오픈소스`로 제공하고 있는 고급 AI 모델입니다. MIT 라이센스를 이용하기 때문에 우리가 직접 가져다 사용할 수 있죠. 수학, 코딩, 논리 문제를 잘 해결하고 OpenAI의 고가 모델에 비해 DeepSeek는 오픈 소스로 제공하기 때문에 무료로 이용해볼 수 있다는 것이 큰 장점입니다.

<br>

### 특징
* **오픈 소스** : 오픈 소스로 제공하고 있어서 무료로 사용이 가능함
* **로컬 설치** : 로컬에 DeepSeek 모델을 설치할 수 있기에 데이터 접근 제어가 가능함
* **다양한 모델 크기 지원** : 각 하드웨어 사양에 따라 맞는 모델 크기를 설치할 수 있음

<br><br>

## DeepSeek R1 로컬 구축
&nbsp;이번에는 해당 DeepSeek R1 모델을 로컬에 구축해보도록 합니다. 일단 저의 개발 환경은 다음과 같습니다.
* OS : macOS 15.2 (Sequoia)
* CPU : M4 Pro
* Memory : 48 GB
* GPU : M4 자체 (14 코어)

<br>

&nbsp;이제 로컬에 구축을 해봅시다. 구축을 하려면 필요한 소프트웨어가 두 가지 있습니다.
* `Docker` : Web UI를 실행하는데 필요합니다.
* `Ollama` : 무료 오픈소스 프로젝트로 LLM 모델들을 macOS, Linux, Windows에서 구축할 수 있도록 도와주는 도구입니다.

<br>

### Ollama 설치
&nbsp;Ollama를 먼저 설치해보겠습니다. Ollama의 <a href="https://ollama.com/download">공식 홈페이지</a>를 통해서 각자의 OS에 맞는 것을 다운로드합니다. 
* <a href="https://ollama.com/download">https://ollama.com/download</a>

<br>

&nbsp;저는 맥북으로 진행하는데 파일을 다운로드 받지 않고 Homebrew를 써서 설치하도록 하겠습니다.  

```sh
$ brew install --cask ollama
$ ollama -v
```

![image](https://github.com/user-attachments/assets/e5d733ad-69f7-4ffc-adb8-de4c011d6ddf)

&nbsp;버전까지 체크해서 제대로 설치되었는지 확인합니다. `Ollama`가 확인되었으면 이제 DeepSeek R1을 설치합니다.

<br>

### DeepSeek 설치
&nbsp;`DeepSeek`는 위에서 설치한 Ollama에서 모델을 가져와서 실행합니다.

<br>

```sh
$ ollama pull deepseek-r1
```

![image](https://github.com/user-attachments/assets/783237fa-3914-4a07-a048-4c20e99a2a2b)
![image](https://github.com/user-attachments/assets/3ce0cc59-2084-4f47-b60e-d90fbcbd6596)

&nbsp;모델을 가져왔다면 실행해봅시다.

<br>

```sh
$ docker run -d -p 3000:8080 \
  --add-host=host.docker.internal:host-gateway \
  -v open-webui:/app/backend/data \
  --name open-webui \
  --restart always \
  ghcr.io/open-webui/open-webui:main
```

![image](https://github.com/user-attachments/assets/269d7433-4106-44e6-baf2-73ad8fdb76e7)
&nbsp;Docker 컨테이너가 정상적으로 실행되면 `localhost:3000`으로 접속하면 위와 같은 화면이 뜹니다.

<br>

![image](https://github.com/user-attachments/assets/33e8fa7b-6831-40eb-ae69-77b138de336f)
&nbsp;관리자 계정을 생성합니다.

<br>

![image](https://github.com/user-attachments/assets/7838e4d0-feb7-457f-903b-bc0177120e8d)
&nbsp;관리자 계정을 생성하면 위와 같이 DeepSeek에 대한 UI가 나오는 것을 확인하실 수 있습니다. 정상적으로 설치가 완료되었네요.

<br><br>

## 질의해보기
&nbsp;설치를 해봤으니 질의를 한번 해보고 답을 보겠습니다. ChatGPT한테 했었던 질문인데 똑같이 물어봤습니다.

<br>

![image](https://github.com/user-attachments/assets/88dce178-3616-4a27-ac48-7fdbb04b7dba)
&nbsp;결과에서 보면 알 수 있듯이 한글에 대해서 호환이 잘 안되어있는 모습입니다. Airflow에 대한 내용은 두번째에 제대로 된 답변이 나왔는데 결과가 영어로 나온 것을 확인하실 수 있습니다. 한글에 대해 잘 호환할 수 있는 방법과 오픈소스 모델이라서 속도가 느린편인데 이러한 것에 대해서 개선점을 찾아봐야겠습니다.