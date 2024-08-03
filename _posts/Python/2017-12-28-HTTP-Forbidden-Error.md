---
title:  "[머신러닝, 파이썬] urllib.error.HTTPError: HTTP Error 403: Forbidden"
date:   2017-12-28 00:00:00 +0900
categories:
  - Python
tags:
  - Python
---

&nbsp;안녕하세요 마개입니다.  
파이썬을 이용해서 특정 URL에서의 정보를 가져올 때 발생한 에러에 대해 공유합니다.  
{: .notice--info}

<br><br>

urllib를 이용하여 특정 URL에서 데이터를 가져오는 작업을 개발하고 있었다.

​
원본 소스는 아래와 같다.  
(거두절미하고 urlopen부분만 편집)

![image](https://user-images.githubusercontent.com/78892113/143765142-388d195e-5a91-46f8-81ad-8e033d213271.png)

  
  
<br><br>

갑자기 잘 되던 것이 아래의 메시지와 함께 오류를 발생시켰다.  
내용을 보면 맨 아래에서 알 수 있듯이 'HTTP Error 403 : Forbidden' 에러가 나왔다.

<br>

![image](https://user-images.githubusercontent.com/78892113/143765173-3147367f-27a6-4fcb-be39-6501149716df.png)

<br><br>

해당 내용을 찾아보니 HTTP 403 에러자체가 서버에서 사람이 아닌 자동으로 내용을 읽는 Spider/Bot 으로 판단하여 차단시켜버린 것이라고 한다. 


이에 따라 urllib.request 작업을 할 때 한가지 설정을 해주면 된다.  
아래와 같이 headers를 설정하여 User-Agent 를 지정해주면 실행이 된다. 

![image](https://user-images.githubusercontent.com/78892113/143765188-13efdbe8-c83b-4fad-ab01-92761ede8d0d.png)


<br><br>