---
title:  "[MongoDB] collection 생성 에러"
date:   2018-01-01 00:00:00 +0900
categories:
  - Data
tags:
  - Data
  - MongoDB
  - NoSQL
---

<br><br>

MongoDB를 이용해서 Collection을 생성할 때 아래와 같은 에러가 발생할 수 있다.

![image](https://user-images.githubusercontent.com/78892113/144739455-0e482321-8431-4bca-9eec-640e5065f24d.png)


<br><br>

MongoDB 버전 문제로 인해 'autoIndex'라는 필드가 없다. 아래와 같이 'autoIndexId'로 수정하면 된다.

![image](https://user-images.githubusercontent.com/78892113/144739490-37730525-09f8-4e25-86c6-b0e522153870.png)
