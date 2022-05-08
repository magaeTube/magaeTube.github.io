---
title:  "Docker Private Registry 만들기 (AWS EC2 Ubuntu)"
date:   2022-04-28 00:00:00 +0900
categories:
  - DevOps
tags:
  - Docker
  - Registry
toc: true
toc_sticky: true
header-img: https://user-images.githubusercontent.com/78892113/166467929-97635fc7-fa14-4c5a-8211-b171e9d96d0a.png

header-mask: true

---

![image](https://user-images.githubusercontent.com/78892113/166467929-97635fc7-fa14-4c5a-8211-b171e9d96d0a.png){: .align-center}{: width="80%" height="80%"} 

<br>

안녕하세요 마개입니다.  
AWS EC2 서버를 이용하여 Docker의 Registry를 만들려고 합니다.
  
EC2의 서버 스펙은 다음과 같이 하였습니다.
  
**<span style="color:red">OS :</span>** Ubuntu20.04  
**<span style="color:red">Instance :</span>** t2.medium (2 vCPU, 4 GiB Memory)  
**<span style="color:red">Storage :</span>** 40 GiB

대략적인 테스트와 아직은 도커 이미지가 적기 때문에 작은 스펙을 이용합니다. 도커 이미지가 많으신 분들은 더 좋은 스펙을 이용하세요.

<br><br>

# Docker & Docker Compose 설치

<br>

먼저 EC2에 Docker가 없기 때문에 Docker와 Docker Compose를 설치해야 합니다. Docker를 설치하는 것은 간단히 명령어로 대체하겠습니다. (이 때의 Docker Compose는 2.0.1 버전을 이용하였습니다.)

```bash
# Docker 설치 (Ubuntu)
$ sudo apt-get update

# 필수 패키지 설치
$ sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common    

# GPG Key 인증
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -    


# Docker Repository 등록
$ sudo add-apt-repository \
"deb [arch=$(dpkg --print-architecture)] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) \
stable"

$ sudo apt-get update
$ sudo apt-get install docker-ce docker-ce-cli containerd.io

$ sudo systemctl enable docker 
$ sudo usermod -aG docker $USER


# Version 확인
$ docker -v


# Docker Compose 설치 (Ubuntu)
$ sudo curl -L "https://github.com/docker/compose/releases/download/v2.0.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
$ sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
$ docker-compose -v
```

<br><br>

# Docker Registry 컨테이너 생성

<br>

사용하고자 하는 Registry 이미지를 먼저 서버에 생성하겠습니다.

```bash
$ docker pull registry
```

![image](https://user-images.githubusercontent.com/78892113/167300264-7d4d72c5-723d-4ccc-b0a7-93ceb616dcb9.png)

<br>

이미지가 정상적으로 다운로드되었는지 확인합니다.

```bash
$ docker images
```

![image](https://user-images.githubusercontent.com/78892113/167300314-337edeb9-f168-4457-9c08-5182c9c6ef5f.png)

<br>

이미지가 있다면 이제 로컬 테스트를 위해 컨테이너를 생성합니다.

```bash
$ docker run -d -p 5000:5000 --name docker-registry registry
$ docker ps
```

<br>

![image](https://user-images.githubusercontent.com/78892113/167300424-42bf9648-859b-43f9-9f7d-51a601968616.png)

컨테이너를 생성하였다면 제대로 생성이 되었는지 확인해봅니다.

<br>

```bash
$ curl localhost:5000/v2/_catalog
```

현재 Registry에 등록되어 있는 이미지가 있는지 확인해봅닏다.

![image](https://user-images.githubusercontent.com/78892113/167300496-882bd2a1-073a-406a-903f-4cfcf21f483a.png)

결과를 보면 현재는 없는 것을 확인하실 수 있습니다.

<br><br>

# 테스트용 이미지 생성 (로컬)

<br>

이제 테스트를 해보기 위해 서버에서 이미지를 하나 만든 후 Registry에 등록해보겠습니다.

```dockerfile
FROM ubuntu:18.04
MAINTAINER magae
CMD echo 'hello'
```

Ubuntu 이미지를 이용해서 테스트용 Dockerfile을 제작합니다.

<br>

```bash
$ docker build -t docker_hello .
$ docker images
```

**docker_hello**라는 태그로 이미지를 생성(빌드)해보겠습니다.

![image](https://user-images.githubusercontent.com/78892113/167300691-58dff3fc-ccdb-4e9d-a5ad-543c0ed790be.png)

<br>

이미지를 확인하면 **docker_hello**라는 Repository의 컨테이너가 생성된 것을 확인하실 수 있습니다. 이제 컨테이너를 생성해서 해당 이미지가 잘 동작하는지 확인합니다.

<br>

```bash
$ docker run docker_hello
```

![image](https://user-images.githubusercontent.com/78892113/167300724-6a212371-89eb-45d7-b2b9-864f3dd5a13c.png)

컨테이너가 정상적으로 실행되었다는 것을 확인하실 수 있습니다.

<br><br>

# 테스트용 이미지 Push (로컬)

<br>

이미지를 Registry에 Push하기 위해서는 이미지 태그를 변경해야 합니다.

(위에서는 이미지가 정상적으로 작동하는지 보기 위해 임의로 **docker_hello**로 작성하였지만 Registry에 등록하기 위해서는 태그를 맞춰줘야 합니다.)

```bash
$ docker tag docker_hello localhost:5000/docker_hello
$ docker images
```

![image](https://user-images.githubusercontent.com/78892113/167300862-00849b8c-dd2d-4e4e-9db5-f0bddbb7ba4a.png)

<br>

태그를 맞게 생성하였다면 이제 Push합니다.

```bash
$ docker push localhost:5000/docker_hello
```

![image](https://user-images.githubusercontent.com/78892113/167300899-db290f02-ab9d-4cb6-be25-443a3ac03d2d.png)

위와 같이 정상적으로 Push되었다면 Registry에 정상적으로 등록되어 있는지 확인합니다.

​<br>

```bash
$ curl localhost:5000/v2/_catalog
```

![image](https://user-images.githubusercontent.com/78892113/167300937-6287ac38-f1ea-4f42-a9b0-e7adc80b30d3.png)

정상적으로 등록된 것을 확인하실 수 있습니다.

<br><br>

# 테스트용 이미지 Pull (로컬)

<br>

Registry에 등록한 이미지를 Pull하기 전에 기존에 만들었던 이미지와 컨테이너를 삭제합니다.

```bash
$ docker ps -a

# 컨테이너 이름은 생성할 때 지정하지 않았기 때문에 각자 다릅니다. 위의 명령어를 입력해서 나온 컨테이너 중에 테스트용 컨테이너에 해당하는 이름을 찾아 입력합니다.
$ docker rm unruffled_fermi 
$ docker images

$ docker rmi localhost:5000/docker_hello:latest
$ docker rmi docker_hello:latest

# Ubuntu의 경우 테스트 이미지를 만들 때 같이 생성되었기 때문에 삭제합니다.
$ docker rmi ubuntu:18.04 
```

<br>

이미지와 컨테이너를 모두 삭제했다면 Registry에 등록되어 있는 이미지를 다운로드합니다.

```bash
$ docker pull localhost:5000/docker_hello:latest
```

![image](https://user-images.githubusercontent.com/78892113/167301077-f5e39825-64d6-424b-a83c-19f7129c9851.png)

<br>

```bash
$ docker images
```

![image](https://user-images.githubusercontent.com/78892113/167301099-e3e2f40c-f749-4291-ac77-04d2d3dbd3f8.png)

이미지가 제대로 다운로드된 것을 확인하실 수 있습니다.

<br><br>

# 테스트용 이미지 Push (원격)

<br>

이렇게 Docker Registry는 만들었는데 우리가 개발을 할 때 각자의 PC에서 개발을 하지 Registry 서버에서 직접 하지는 않습니다. 그렇기 때문에 이번에는 각자의 PC에서 진행을 해보겠습니다.

먼저 각자의 PC에서 Registry에 담겨있는 repository를 확인하겠습니다.

```bash
$ curl [Registry IP]:5000/v2/_catalog
```

![image](https://user-images.githubusercontent.com/78892113/167301159-414edba4-aae8-463b-950a-47679621d2c5.png)

아까 만들어놓았던 것이 있는 것을 확인하실 수 있습니다. 

<br>

이젠 위에서 진행했던 Dockerfile을 이용하여 이미지를 먼저 생성하겠습니다.

```bash
$ docker build -t [Registry IP]:5000/docker_test .
```

이번에는 아까와 다르게 **docker_test**"**라는 이름으로 진행합니다. 
이렇게 생성된 이미지를 원격 Registry로 Push해봅니다.

<br>

```bash
$ docker push [Registry IP]:5000/docker_test
```

![image](https://user-images.githubusercontent.com/78892113/167301244-4bb772a0-a2ea-465a-86e7-0adc0ffef6fd.png)

그러면 위와 같이 에러가 발생할 수 있습니다. (저는 IP를 hosts에 따로 등록하여 이용하였습니다.)

​이럴 때 Docker에 insecure-registries를 설정해야 합니다.

<br>

```bash
# 파일이 없으신 분은 생성합니다.
$ vim /etc/docker/daemon.json 
```

```json
{
    "insecure-registries": ["Registry IP:5000"]
}
```

<br>

설정을 한 후에 docker를 다시 실행시켜줍니다.

```bash
$ systemctl daemon-reload
$ systemctl restart docker
```

완료되었으면 다시 Push를 진행합니다.

​<br>

```bash
$ docker push [Registry IP]:5000/docker_test
```

이상없이 Push가 되었다면 다시 Repository를 확인합니다.

<br>

```bash
$ curl [Registry IP]:5000/v2/_catalog
```

![image](https://user-images.githubusercontent.com/78892113/167301364-b1140e7f-d63c-4cc6-9f3b-6e6e303ce094.png)

docker_test가 잘 올라간 것을 확인하실 수 있습니다.
