---
title:  "[Data Engineering] Apache Druid 설치 (Docker 버전)"
date:   2020-11-04 00:00:00 +0900
categories:
  - Data
tags:
  - Data
  - Druid
toc: true
toc_sticky: true
toc_label: "Druid 설치"
header-img: https://user-images.githubusercontent.com/78892113/209270038-0e690c31-c629-404d-9a86-38fe361cb57a.png
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
이번에는 Docker를 이용하여 설치해보겠습니다. OS는 마찬가지로 Ubuntu에서 진행하고 Docker가 설치되어 있다는 가정하에 진행합니다. 마찬가지로 공식 홈페이지를 보고 참고하였습니다. Docker 중에서 `docker-compose`를 이용하여 진행할 것인데 Druid에서 제공하는 기본 예제 `docker-compose.yml`을 이용하여 진행하겠습니다. 
{: .notice--info}

<br>

![Druid](https://user-images.githubusercontent.com/78892113/209270038-0e690c31-c629-404d-9a86-38fe361cb57a.png){: .align-center}{: width="70%" height="70%"} 

<br><br>

# 디렉터리 생성

저의 경우 새로운 디렉터리를 만들고 그 안에 `docker-compose.yml`을 작업하고자 합니다.

![image](https://user-images.githubusercontent.com/78892113/209301832-271867d4-565d-4bad-a27a-54569bfef3d8.png)

<br><br>

# docker-compose.yml 작성

Druid에서 제공하는 기본 <a href="https://github.com/apache/druid/blob/master/distribution/docker/docker-compose.yml">docker-compose.yml</a> 파일을 보고 작성합니다. 작성 과정은 생략합니다.

<br><br>

# environment 작성

`docker-compose`를 실행할 때 환경 설정들을 지정해야 하는데 이 또한 Druid에서 제공하는 기본 <a href="https://github.com/apache/druid/blob/master/distribution/docker/environment">environment</a>를 이용합니다. 이 또한 복붙하였기 때문에 작성 과정은 생략합니다.

<br><br>

# docker-compose 실행

이렇게 작성한 `docker-compose`를 실행합니다.

```sh
docker-compose up
```

백그라운드로 실행하고 싶으시면 뒤에 `-d` 옵션을 붙여주세요.

<br>

![image](https://user-images.githubusercontent.com/78892113/209301840-f469c696-5760-42c4-82c7-c2deecce5bd4.png)

![image](https://user-images.githubusercontent.com/78892113/209301854-55fefaa9-384e-40dd-b7ae-86b65498f551.png)

순서에 따라 이미지가 다운로드됩니다.

<br><br>

# 확인

<br>

![image](https://user-images.githubusercontent.com/78892113/209301866-b6b198a1-d076-41c6-8ad7-8f2111f00519.png)

이미지를 모두 받고 실행된다면 위와 같이 로그를 확인할 수 있습니다. (`-d` 옵션으로 실행했다면 백그라운드 환경이기 때문에 해당 로그는 보이지 않습니다.)

<br>

이제 마찬가지로 <a href="http://localhost:8888/">http://localhost:8888/</a> 여기로 접속해봅시다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209301877-2e41c9e8-bdef-4271-8035-4a4824f2511e.png)

위와 같이 정상적으로 실행되는 모습을 확인하실 수 있습니다.
