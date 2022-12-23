---
title:  "[Hadoop] MacOS Hadoop 설치 및 세팅"
date:   2020-10-10 00:00:00 +0900
categories:
  - Data
tags:
  - Data
  - Hadoop
toc: true
toc_sticky: true
toc_label: "Hadoop 설치 (MAC)"
header-img: https://user-images.githubusercontent.com/78892113/209270038-0e690c31-c629-404d-9a86-38fe361cb57a.png
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
맥북(Mac OS)에 하둡을 설치하고 세팅을 해보겠습니다.  
{: .notice--info}

<br>

![Hadoop](https://user-images.githubusercontent.com/78892113/209311090-5f9d1b6f-f490-40b6-832d-d7ec2bfaa8ea.png){: .align-center}{: width="70%" height="70%"} 

<br><br>

# 설치

하둡의 경우 <a href="https://hadoop.apache.org/releases.html">홈페이지</a>에서 원하는 버전을 이용해서 다운로드합니다. 다운로드한 `tar.gz` 압축파일을 해제합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209310337-893c9d9a-eda7-43ed-bae8-f75ab4048da4.png){: .align-center}{: width="70%" height="70%"}

압축을 해제한 후에 하둡 폴더가 있는 위치를 확인한 후 환경 설정을 진행합니다.

<br>

```sh
export HADOOP_HOME=/Users/ksh/study/hadoop-3.1.4
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

위와 같이 설정을 한 후에 확인을 해봅니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209310365-d0765330-e5e5-45ef-a374-f50fcb1e3fc8.png){: .align-center}{: width="70%" height="70%"}

설정을 확인한 후에는 하둡의 버전을 확인해봅니다. 하둡 데몬을 실행하는 스크립트는 `sbin` 디렉터리에 있습니다. 해당 디렉터리를 위에서 환경설정하였기 때문에 진행을 해보면 됩니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209310377-12fccb17-423c-4c55-b8c5-07bf34b3c89b.png){: .align-center}{: width="80%" height="80%"}

위와 같이 하둡의 버전이 3.1.4인 것을 확인할 수 있습니다.

<br><br>

# 환경 설정

하둡의 각 구성요소에 대한 설정은 주로 `XML` 파일을 이용합니다.
* 공통 속성 : core-site.xml
* HDFS 속성 : hdfs-site.xml
* 맵리듀스 속성 : mapred-site.xml
* YARN 속성 : yarn-site.xml
위의 설정 파일들은 모두 `etc/hadoop`의 서브디렉터리에 있습니다.  

<br>

하둡은 3가지 모드가 있습니다.
* **독립모드** : 데몬이 실행되지 않고 모든 것이 단독 JVM 내에서 실행됩니다. 시험과 디버깅을 쉽게 할 수 있기 때문에 개발 단계에서 맵리듀스 프로그램을 실행할 때 적합합니다.
* **의사 분산 모드** : 모든 하둡 데몬을 로컬 머신에서 실행합니다. 따라서 작은 규모의 클러스터에서 실행하는 것과 같은 효과가 있습니다.
* **완전 분산 모드** : 하둡 데몬을 여러 대의 머신으로 구성된 클러스터에서 실행합니다.

<br>

독립 모드는 따로 추가 작업이 없어서 의사 분산 모드로 설정해봅니다. 이를 위해 각 속성 파일들을 수정합니다.

<br>

## core-site.xml 수정

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://localhost/</value>
  </property>
</configuration>
```

<br>

## hdfs-site.xml 수정

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
  <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>
</configuration>
```

<br>

## mapred-site.xml 수정

```xml
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
</configuration>
```

<br>

## yarn-site.xml 수정

```xml
<?xml version="1.0"?>

<configuration>

<!-- Site specific YARN configuration properties -->
  <property>
    <name>yarn.resourcemanager.hostname</name>
    <value>localhost</value>
  </property>
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
</configuration>
```

<br>

위와 같이 설정을 했다면 SSH 설정을 위해 맥북에서 [시스템 환경설정] - [공유] - [원격 로그인]을 체크합니다.  
이후에는 HDFS를 포맷해야 합니다.

<br>

```sh
hdfs namenode -format
```

위의 명령어를 통해 HDFS를 사용하기 전에 파일 시스템을 포맷합니다.

<br><br>

# 시작

**데몬 시작**

```sh
start-dfs.sh
start-yarn.sh
mr-jobhistory-daemon.sh start historyserver
```

<br>

**데몬 중지**

```sh
mr-jobhistory-daemon.sh stop historyserver
stop-yarn.sh
stop-dfs.sh
```