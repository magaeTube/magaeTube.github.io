---
title:  "[Data Engineering] Apache Druid 설치"
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
Ubuntu 환경에서 Apache Druid를 설치해보겠습니다.  
공식 홈페이지의 <a href="https://druid.apache.org/docs/latest/tutorials/index.html">Quickstart</a>를 참고하였습니다.  
Docker를 이용하는 방법과 Docker를 이용하지 않는 방법이 있는데 Docker를 이용하지 않고 진행해보았습니다. OS는 Ubuntu에서 진행을 하였고 JAVA 1.8이 설치되어 있다는 가정하에 진행합니다. 
{: .notice--info}

<br>

![Druid](https://user-images.githubusercontent.com/78892113/209270038-0e690c31-c629-404d-9a86-38fe361cb57a.png){: .align-center}{: width="70%" height="70%"} 

<br><br>

# 필요사항

* **OS** : Linux, Mac OS X, Unix 계열 OS (윈도우는 지원하지 않습니다.)
* **Java** : 8u92 이상 

<br><br>

# 다운로드

현재 나와있는 `Druid`를 먼저 <a href="https://www.apache.org/dyn/closer.cgi?path=/druid/0.20.0/apache-druid-0.20.0-bin.tar.gz">다운로드</a>합니다.  

<br><br>

# 압축 해제

<br>

```sh
tar -xzvf apache-druid-0.20.0-bin.tar.gz
cd apache-druid-0.20.0/
```

![image](https://user-images.githubusercontent.com/78892113/209303020-2958cb88-3177-427e-8048-9642f63dfd52.png)

버전의 경우 개인마다 다를 수 있습니다. 압축을 해제하면 디렉터리가 생성된 것을 확인할 수 있습니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209303026-cc4b55cb-76e9-4c75-b5fe-28c032ad077b.png)

<br><br>

# 실행하기

개인 PC에서 진행하기 때문에 `micro-quickstart`로 진행합니다.

```sh
./bin/start-micro-quickstart
```

![image](https://user-images.githubusercontent.com/78892113/209303031-27ddc917-7d0a-40df-913c-c73322b284af.png)

![image](https://user-images.githubusercontent.com/78892113/209303034-b2af47a3-a6d0-4930-891f-c88098a609af.png)

실행을 하면 `ZooKeeper`와 `Druid` 서비스가 같이 실행되는 것을 확인하실 수가 있습니다. 

<br><br>

# console 확인

<a href="http://localhost:8888">http://localhost:8888</a>로 접속해서 아래 화면과 같이 `Druid`가 실행되는지 확인합니다.

![image](https://user-images.githubusercontent.com/78892113/209303048-7881cbb0-37bf-48ec-ab00-b27592b018e4.png)