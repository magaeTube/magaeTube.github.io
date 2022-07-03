---
title:  "[Docker] 서버의 용량이 꽉 찼을 때 확인사항"
date:   2022-04-26 00:00:00 +0900
categories:
  - DevOps
tags:
  - Docker
  - AWS
  - EC2
toc: true
header-img: https://user-images.githubusercontent.com/78892113/166467929-97635fc7-fa14-4c5a-8211-b171e9d96d0a.png
header-mask: true

---



&nbsp;안녕하세요 마개입니다.  
AWS EC2를 운영하다 디스크 용량이 가득 차서 점검을 해본 사항에 대해 공유합니다.
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/166467929-97635fc7-fa14-4c5a-8211-b171e9d96d0a.png){: .align-center}{: width="80%" height="80%"} 


<br>
  
AWS EC2에는 Airflow를 운영 중입니다. (Worker는 다른 서버에서 운영 중입니다.)  
Webserver, Scheduler 등 다른 요소들을 사용 중이기 때문에 디스크는 일부러 작은 용량을 이용 중입니다. (32GiB) 이 서버에서는 Airflow와 Redis, Telegraf를 모두 Docker로 운영중입니다.
  
  
<br><br>

# 원인 찾기
  
<br>

디스크 용량이 가득 차서 Airflow가 실행되지 않는 것을 보고 원인을 찾아 나섰습니다.

```bash
$ df -h
```

명령어로 검색을 해보니 아래와 같습니다.

![image](https://user-images.githubusercontent.com/78892113/166471690-24712197-f1bb-46a9-a102-bb91a7317147.png){: .align-center}{: width="80%" height="80%"}

디스크를 96%나 사용 중이라서 제대로 된 서비스가 실행되지 않고 있었습니다.  
그래서 구석구석 찾다 보니 아래와 같은 명령어로 검색을 하였습니다.  

<br>

```bash
$ du -h --max-depth=1 /var/lib
```

![image](https://user-images.githubusercontent.com/78892113/166472037-1b21a5eb-8a15-4f0b-8e93-1bb2915f3605.png){: .align-center}{: width="80%" height="80%"}

결과를 보면 docker가 가장 많은 양을 차지하고 있었습니다.

<br><br>

# 해결책

<br>

**<span style="color:red">미사용 중인 이미지와 컨테이너 삭제</span>**

<br>

```bash
$ docker system prune -a -f
```

해당 명령어로 안 쓰는 이미지와 컨테이너를 삭제하였습니다.

이미지와 컨테이너를 삭제했지만 저의 경우 1G정도만 삭제되었습니다.
이것만으로는 부족합니다. 좀 더 분석을 하기 위해 /var/lib/docker로 들어가 봅니다.

<br>

```bash
$ cd /var/lib/docker
```

![image](https://user-images.githubusercontent.com/78892113/166472782-6004b985-849c-4b17-a6f1-a1d469e01952.png){: .align-center}{: width="80%" height="80%"}

이때 저와 같이 permission denied 에러가 발생한다면

<br>

```bash
$ sudo chmod 755 /var/lib/docker
```

해당 명령어로 디렉터리 접근 권한을 부여합시다.
해결이 되었다면 docker 디렉터리로 들어가 다시 찾습니다.

![image](https://user-images.githubusercontent.com/78892113/166473241-a6887659-1ed3-4109-aa37-8cb10be5b500.png){: .align-center}{: width="80%" height="80%"}

보면 containers와 overlay2가 많은 양을 차지하고 있는 것을 볼 수 있습니다.  

<br>

**<span style="color:red">Containers 로그 삭제</span>**

해당 디렉터리에는 컨테이너의 로그가 쌓이는 디렉터리입니다.

![image](https://user-images.githubusercontent.com/78892113/166473671-1f042381-1d1f-4ee1-afff-885467486035.png){: .align-center}{: width="80%" height="80%"}

디렉터리 명을 보면 앞부분은 Container ID입니다. 저의 경우 15G를 차지하는 것이 있는데 Airflow와 서버 메트릭을 위해 Telegraf를 컨테이너로 띄운 상태인데 이 컨테이너의 로그가 15G를 차지하고 있었네요.

![image](https://user-images.githubusercontent.com/78892113/166473994-24018969-ef68-4f33-9a15-c1da866567da.png){: .align-center}{: width="80%" height="80%"}

해당 컨테이너 디렉터리로 들어와 보면 `*-json.log` 파일이 15G인 것을 확인할 수 있습니다. 해당 파일을 정리합니다. (파일명은 사용자마다 다릅니다.)

<br>

```bash
$ sudo truncate -s 0 e941de3b2cf970010b3727fddcb4e29a1c95e98e8471151e7a5b48c51572c9fa-json.log
```

![image](https://user-images.githubusercontent.com/78892113/166474241-3a1ab514-6ce0-4565-a3aa-bac6c8f73f2d.png){: .align-center}{: width="80%" height="80%"}

용량이 확 줄은 것을 확인할 수 있습니다.

<br><br>

# 근본 원인 해결하기

<br>

**<span style="color:red">로그 데이터 용량 제한</span>**

저는 `docker-compose`를 이용하여 Telegraf 서비스를 생성하였습니다.
해당 서비스가 많은 로그 데이터를 쌓기 때문에 파일 사이즈에 제한을 두어야 합니다. 그래서 로그 제한은 `docker-compose`파일에 지정하였습니다.

```yaml
...(생략)...
telegraf:
  image: telegraf
  logging:
    driver: "json-file"
    options:
      max-file: "5"
      max-size: "100m"
  container_name: airflow_telegraf
  restart: always
...(생략)...
```

이 외에도 주기적으로 로그 파일을 삭제해 주는 로직을 만들 수도 있습니다.
편하신 대로 지정하면 될 것 같습니다.