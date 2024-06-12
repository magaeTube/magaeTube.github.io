---
title:  "[BigQuery] 구글 빅쿼리 (2) - 시작하기"
date:   2020-07-01 00:00:00 +0900
categories:
  - GCP
tags:
  - Cloud
  - GCP
  - BigQuery
toc: true
toc_sticky: true
toc_label: "BigQuery 시작"
header-img: https://user-images.githubusercontent.com/78892113/209471556-8c6c325a-8c24-48a0-a12c-4f9ffea0bbc3.png
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
이전 글에서 BigQuery에 대해 알아보았고 이번에는 BigQuery를 이용해보도록 하겠습니다. GCP에 가입하는 부분은 생략하였습니다.
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/209471556-8c6c325a-8c24-48a0-a12c-4f9ffea0bbc3.png){: .align-center}{: width="70%" height="70%"} 

<br>

# GCP 접속

<a href="https://cloud.google.com">GCP URL</a>로 접속합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209523340-1639dc32-b8aa-4ef4-995b-f3d38e4f8065.png){: .align-center}

우측 상단 또는 좌측 중앙에 **`콘솔(Console로 이동)`**을 눌러서 GCP로 이동을 합니다. 저는 이미 자동 로그인이 되어있기 때문에 화면이 바로 넘어가지는데 그렇지 않은 분이나 가입이 필요하신 분은 진행해 주세요

<br>

![image](https://user-images.githubusercontent.com/78892113/209523346-5d9134b1-eba0-4f31-bbc3-4253f392ea0e.png){: .align-center}

넘어오면 메인 화면에 위와 같이 되어있는 것을 볼 수 있습니다. 저는 기존에 했던 프로젝트가 있기 때문에 위와 같이 뜹니다. 

<br><br>

# 새로운 프로젝트 생성

새로운 프로젝트를 만들어서 진행해봅시다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209523361-1b99e132-2a9a-4e9a-962a-681d78d6f97d.png){: .align-center}

좌측 상단에 빨간색 박스를 누르면 (저는 UiPath Test라고 되어있는 부분) 아래와 같이 **프로젝트를 선택하는 팝업창**이 뜹니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209523368-80965d2f-25dd-4206-bbe5-52b6ca52edde.png){: .align-center}

프로젝트 선택 창이 뜨면 우측 상단에 **`새 프로젝트`**를 클릭합니다. 

<br>

![image](https://user-images.githubusercontent.com/78892113/209523388-86372e9b-ba9c-4550-b3fe-b4bc2d4c3b36.png){: .align-center}

새 프로젝트를 만드는 창으로 넘어가면 **프로젝트 이름을 지정**하고 **`만들기`**를 클릭합니다. 저와 같은 경우에는 BigQueryTest로 이름을 지정하였는데 편하신 이름으로 지정해 주세요  
만들기가 완료되면 다시 메인 홈으로 돌아오는데 다시 좌측 상단 프로젝트를 눌러서 방금 만든 프로젝트로 변경하겠습니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/209523398-df4f6666-c9a0-48a3-80e8-d0b430b79c77.png){: .align-center}

<br><br>

# BigQuery로 넘어가기

이렇게 되면 프로젝트 생성은 완료가 되었습니다. 이제 BigQuery를 이용해보도록 하겠습니다.  
메인 페이지에서 왼쪽에 목록을 보면 아랫부분에 **`[빅데이터]`** 안에 **`[BigQuery]`**가 있는 것을 확인할 수 있습니다. 

<br>

![image](https://user-images.githubusercontent.com/78892113/209523409-6577f96e-c059-4b6b-8679-9dab2d79a61a.png){: .align-center}

해당 메뉴를 누르면 아래와 같은 페이지가 나오면서 BigQuery를 이용할 준비가 완료된 것입니다. 팝업창에 **빠른 시작 가이드**를 이용해도 됩니다.  

다음 포스팅에는 BigQuery를 이용하여 직접 작업해보도록 하겠습니다. 