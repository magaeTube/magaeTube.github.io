---
title:  "[Athena] No output location provided 에러"
date:   2022-11-04 00:00:00 +0900
categories:
  - Cloud
tags:
  - Cloud
  - AWS
  - Athena

header-mask: true
---

&nbsp;안녕하세요 마개입니다.  
AWS에서 Athena를 처음 이용하거나 IAM 유저를 새로 만들고 권한을 부여한 후 이용하게 될 때 발생할 수 있는 문제에 대해 알아봅니다.
{: .notice--info}

<br><br>

![image](https://user-images.githubusercontent.com/78892113/200014835-092c54d3-0e8d-4381-a6f3-9a4b0de05bf1.png){: .align-center}

<br> 

쿼리 문을 실행할 때 위와 같은 메시지 `No output location provided`가 뜨면 확인해야 할 것이 있습니다. Athena의 경우 쿼리를 실행하고 결과를 남기는데 해당 결과를 `S3`에 남기는 구조입니다. 근데 이 S3 경로를 지정하지 않으면 위와 같은 문제가 생기는데 이 부분을 봅니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/200015251-65865db0-e1b5-4235-a003-15670d33b4f9.png){: .align-center}

<br>

Athena Query editor를 보면 위와 같은 메뉴들이 있는데 여기서 `Settings`로 이동합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/200015798-6019f818-0d63-41b2-9a26-48c037a8e78b.png){: .align-center}

보면 위와 같이 Query result location이 비어있는 것을 보실 수 있는데 우측 상단에 `Manage` 버튼을 클릭합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/200015967-a4088175-9056-4515-801c-83324a245722.png){: .align-center}

위에서 `Location of query result`를 정해줘야 합니다. `S3`에 해당 결과용으로 버킷을 하나 생성하고 해당 버킷을 지정하도록 합니다.

버킷을 설정하고 `Save` 버튼을 클릭하여 저장합니다.

저장하면 버킷 경로가 잡히는 것을 확인할 수 있고 Athena 쿼리를 실행해 보면 정상적으로 실행되는 것을 확인할 수 있습니다.