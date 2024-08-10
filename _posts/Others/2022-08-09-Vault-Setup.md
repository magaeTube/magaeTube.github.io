---
title:  "[Vault] 구축"
date:   2022-08-09 00:00:00 +0900
categories:
  - Others
tags:
  - Vault
toc: true
toc_sticky: true
toc_label: "Vault 구축"
header-img: https://user-images.githubusercontent.com/78892113/210087509-d338b20f-1524-452c-bb83-cc12d5e78498.png
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
개발을 하는데 있어 필요한 토큰, 비밀번호, 암호화 키와 같은 민감한 데이터를 어떻게 다루고 관리해야할지 고민할 때 고려해야 하는 기술 및 툴 중에 하나인 Vault에 대해 알아봅니다.  
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/210087509-d338b20f-1524-452c-bb83-cc12d5e78498.png){: .align-center}{: width="70%" height="70%"} 

<br><br>

## Vault란

**`Vault`**는 토큰, 비밀번호, 인증서, 암호화 키와 같은 비밀 정보나 민감한 데이터를 UI, CLI, HTTP API를 활용할 때 안전하게 저장하고 제어할 수 있게 해주는 HashiCorp의 오픈소스입니다.

* 공식 홈페이지 <a href="https://www.vaultproject.io/">URL</a>

<br><br>

## 주요 기능

* **Secure Secret Storage** : Vault에 `임의의 키/값 비밀을 저장`, Vault는 이러한 비밀을 영구 저장소에 쓰기 전에 암호화하므로 원시 저장소에 대한 액세스 권한을 얻는 것만으로는 비밀에 액세스할 수 없다.
* **Dynamic Secrets** : AWS 또는 SQL 데이터베이스와 같은 `일부 시스템에 대해 온디맨드로 비밀을 생성`할 수 있다.
* **Data Encryption** : 데이터를 저장하지 않고 `암호화 및 해독`할 수 있습니다. 이를 통해 보안 팀은 암호화 매개변수를 정의하고 개발자는 자체 암호화 방법을 설계하지 않고도 SQL 데이터베이스와 같은 위치에 암호화된 데이터를 저장할 수 있다.
* **Leasing and Renewal** : 모든 비밀에는 관련 임대가 있습니다. 임대 기간이 끝나면 Vault는 `해당 비밀을 자동으로 취소`한다.
* **Revocation** : 키 롤링과 침입 시 `시스템 잠금을 지원`한다

<br><br>

## 설치

**`Vault`**를 설치할 때 **`Docker`**를 이용해서 설치해봅니다.

<br>

### 이미지 다운로드

Docker 이미지는 공식으로 제공하는 `vault` 이미지를 이용합니다. 이미지를 이용하기 위해 `pull`합니다.

<br>

```sh
$ docker pull vault
```

![image](https://user-images.githubusercontent.com/78892113/210088544-0e9a1384-1747-46bc-9113-805050a7cbd4.png){: .align-center}

<br>

### 디렉터리 생성 및 설정 파일 생성

**`Vault`**에서 사용할 디렉터리들을 생성하고 설정 파일을 만들어보겠습니다.

<br>

```sh
$ mkdir logs
$ mkdir file
$ mkdir config
```

![image](https://user-images.githubusercontent.com/78892113/210088773-799b98bd-d5ce-4e5e-9bd5-85b38f145cb2.png){: .align-center}

<br>

디렉터리들을 생성했다면 `config` 폴더 안에 `local.json` 파일을 생성해서 설정 내용을 작성합니다.

```json
{
   "listener":{
       "tcp":{
         "address":"0.0.0.0:8200",
         "tls_disable":1
      }
   },
   "backend":{
      "file":{
         "path":"/vault/file"
      }
   },
   "default_lease_ttl":"876000h",
   "max_lease_ttl":"876000h",
   "ui":true,
   "log_level": "info"
}
```

<br>

### 실행

설정을 지정했다면 이제 Docker 이미지를 이용해서 실행해봅니다. 

<br>

```sh
$ docker run --name vault -d --cap-add=IPC_LOCK -p 8200:8200 \
--log-opt mode=non-blocking  \
-v /home/tide/sw/vault/config:/vault/config \
-v /home/tide/sw/vault/file:/vault/file \
-v /home/tide/sw/vault/logs:/vault/logs \
vault server
```

위와 같이 이미지를 바로 실행시키거나 아래와 같이 `Docker Compose` 파일을 작성하여 실행합니다.

<br>

```yml
version: '3'

services:
  vault:
    image: vault:latest
    container_name: vault
    ports:
      - 8200:8200
    environment:
      - SKIP_CHOWN=true
    volumes:
      - ./config:/vault/config
      - ./file:/vault/file
      - ./logs:/vault/logs
    cap_add:
      - IPC_LOCK
    command: vault server -config=/vault/config/local.json
```

<br>

```sh
$ docker-compose up -d
```

<br>

### 확인 및 초기 설정

이제 설치를 했으니 웹 페이지에 접속해서 확인하고 초기 설정을 진행해보겠습니다. 

<br>

![Web UI](https://user-images.githubusercontent.com/78892113/210089648-893b26a8-4c9c-4b24-8282-c6ff8f0f23b8.png){: .align-center}

<a href="http://localhost:8200">http://localhost:8200</a>에 접속을 하면 위와 같은 모습을 보입니다. 처음의 모습은 비상 시 필요한 마스터 키의 초기 세트를 설정하는 곳입니다. 

* **Key shares** : 총 key 수 설정
* **Key Threshold** : 몇 개 이상의 key를 보유해야 login이 가능한지 설정

테스트로 **`Key shares`**는 3개, **`Key Threshold`**는 2개로 설정을 하고 **Initialize** 버튼을 클릭합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/210090052-d39ba577-5808-4efb-a2bb-c98ec895d1ba.png){: .align-center}{: width="70%" height="70%"}

위와 같이 `initial root toekn`과 `Key` 3개가 생성되면 **Download keys**버튼을 클릭하여 내용을 받습니다. 그리고 **Continue to Unseal** 버튼을 클릭합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/210090292-9c428d49-eae3-4275-8bf0-b9ee430e3487.png){: .align-center}

현재는 **`Vault`**가 **`Sealed (봉인 상태)`**이기 때문에 봉인을 해제해야 합니다. 좀전에 받은 JSON 파일에서 **`keys`**부분을 보면 3개가 있는 것을 확인할 수 있습니다. 이것이 위에서 발급받았던 3개의 key입니다. 위의 Key Threshold에서 2로 지정을 했기 때문에 3개의 key중에서 2개만 제대로 입력하면 봉인은 해제됩니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/210090433-bab19e55-3c02-4a79-9c9f-7e839424eaa7.png){: .align-center}

2개의 Key를 입력하고 나면 봉인상태는 풀리고 로그인을 해야합니다. 로그인할 때는 Method에 다양한 방법이 있는데 우리는 위에서 받은 JSON파일에서 **`root_token`**을 이용하여 로그인하겠습니다. 해당 내용을 이용해서 로그인합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/210090535-f3b8753c-2f91-4966-aced-fda40bc21d2d.png){: .align-center}

로그인하면 초기 설정은 끝입니다.
