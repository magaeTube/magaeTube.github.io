---
title:  "[Python] 구글 스프레드시트 연동" 
date:   2024-04-09 00:00:00 +0900
categories:
  - Python
tags:
  - Python
  - GCP
  - SpreadSheet

toc: true
toc_sticky: true
toc_label: "구글 스프레드시트 연동"
header-mask: true
---

&nbsp;안녕하세요 마개입니다.  
Python의 Pandas를 이용하여 구글 스프레드시트에 데이터를 업로드하는 작업을 진행해보고자 합니다.  
해당 내용은 구글 스프레드시트 API 설정과 파이썬 연동, Pandas로 데이터 업로드하는 과정으로 진행됩니다.
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/210123814-45c26950-c3d7-40a9-a966-ca0be1dc1596.png){: .align-center}{: width="80%" height="80%"} 

<br><br>

# GCP 서비스 계정 생성

&nbsp;구글 스프레드시트 API를 이용하기 위해서는 GCP에 계정을 만들어야 합니다. 아래의 링크를 통해 접속하여 계정을 생성합니다. 저는 이미 계정이 있기 때문에 이 과정은 생략합니다.
* <a href="https://console.cloud.google.com/apis/dashboard">https://console.cloud.google.com/apis/dashboard</a>

<br>

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/ec610e23-f4f0-485b-a36e-1ced571c2566){: .align-center}
&nbsp;계정을 생성하고 완료되면 다시 메인 대시보드 화면으로 이동합니다. 그러면 위와 같은 모습을 보이는데 (저는 기존에 "BigQueryTest"라는 이름의 프로젝트가 있어서 정확히는 여러분과 다를 수 있습니다.) 좌측 상단의 빨간 박스 위치를 선택합니다. (신규로 생성하신 분은 "프로젝트 선택"으로 보일 것입니다.)

<br>

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/4c0b0c80-da42-4a76-9b6d-0e31aa284f0d){: .align-center}
&nbsp;위와 같이 "프로젝트 선택" 팝업이 뜨면 우측 상단의 "새 프로젝트"를 선택합니다.

<br>

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/a401d91e-27c5-4cd1-9a5a-b81bb409fb08){: .align-center}
&nbsp;그러면 위와 같이 나오는데 여기서 프로젝트 이름과 조직을 선택합니다. 생성이 완료되면 신규로 생성된 프로젝트를 선택합니다.

<br>

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/ce583c99-fab5-4a14-b757-de525bdf7ecd){: .align-center}
&nbsp;신규 프로젝트로 들어와지면 왼쪽 메뉴 중에 "사용자 인증 정보"를 선택합니다. 그러면 나오는 화면 상단에 "사용자 인증 정보 만들기"를 선택합니다.

<br>

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/67cbbfda-29be-4971-87c7-b109e144b678){: .align-center}
&nbsp;그러면 나오는 선택사항에 "서비스 계정"을 선택합니다.

<br>

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/cb684d36-c872-4f5c-a65d-d1fd3f44f5f2){: .align-center}
&nbsp;위와 같이 "서비스 계정 이름"을 정합니다. 그러면 아래 ID와 이메일 주소는 자동으로 생성이 됩니다.

<br>

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/a27fdc47-5b45-4c6b-8256-79119c4f2ed2){: .align-center}
&nbsp;생성이 완료되면 다시 API 페이지로 넘어오는데 방금 생성한 서비스 계정을 선택합니다.

<br>

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/e86ae7c9-6ad0-4b6f-b45f-663316c0ee70){: .align-center}
&nbsp;메뉴로 들어오면 상단 탭에 "키"를 선택하고 중간에 있는 [키 추가] - [새 키 만들기]를 선택합니다. 이 때 "키 유형"은 "JSON"을 선택합니다. 선택을 완료하면 JSON파일이 다운로드되고 그 안에는 키에 대한 정보들이 있는 것을 확인하실 수 있습니다. 이 정보는 민감 정보이기 때문에 절대 유출이 되면 안됩니다.

<br>

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/2fb2a60b-69bd-48cf-9524-b3d9031c3f0a){: .align-center}
&nbsp;생성이 완료되면 다음과 같이 키가 있는 것을 보실 수 있습니다.

<br><br>

# 구글 스프레드시트 API 설정

&nbsp;다음으로는 스프레드시트 API를 설정하고 시트와 연동해봅니다.

<br>

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/4384eb39-7792-43b0-83f6-7ad0f6bba25b){: .align-center}
&nbsp;다시 프로젝트 메인으로 넘어옵니다. 넘어오면 위와 같은 화면인데 여기서 "API 및 서비스 사용 설정"을 선택합니다. (만약 위와 같은 화면이 보이지 않는다면 좌측 Google Cloud 옆 햄버거 메뉴를  클릭하고 "API 및 서비스"를 찾습니다.)

<br>

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/91ca2364-4e91-4fa7-bf4c-da268a90f7b7){: .align-center}
&nbsp;그러면 여러 API가 나오는데 여기서 "Google Sheets API"를 선택합니다.

<br>

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/98d22f2f-23ca-403c-835b-4986f74f1da0){: .align-center}
&nbsp;그러면 다음과 같이 나오는데 여기서 "사용"을 선택합니다. 이러면 API 준비는 되었습니다. 이제 이를 스프레드 시트와 연결해봅니다.

<br>

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/4fc5fec4-5a55-4464-badd-799f07589eb0){: .align-center}
&nbsp;작업을 하기 전에 아까 생성했던 키의 이메일 주소를 복사합니다. 이는 바로 다음에 시트에서 공유 기능을 사용하기 위함입니다.

<br>

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/80b7d010-b16a-4270-a4c2-343377c84e21){: .align-center}{: width="80%" height="80%"}  
&nbsp;연동을 하고자 하는 스프레드시트로 넘어와서 우측 상단에 있는 공유 버튼을 선택합니다. 그러면 나오는 팝업에 위에서 복사한 이메일 주소를 입력하고 "편집자"로 권한을 부여합니다. 그리고 "전송" 버튼을 눌러 마무리합니다.

<br><br>

# 파이썬 테스트
&nbsp;API 키를 생성하고 작업하고자 하는 구글 스프레드시트에 연동을 했다면 이제 파이썬을 이용해서 테스트해봅니다.  
파이썬에서 SpreadSheet를 사용하기 위해서는 gspread 패키지가 필요합니다. 아래와 같이 설치합니다.

```sh
$ pip install gspread
```

설치를 완료했다면 아래와 같이 코드를 작성합니다.
```python
import gspread

# 위에서 키 정보 들이 담겨있던 JSON 파일 경로를 `json_file_path` 변수에 넣습니다.
json_file_path = "XXXXXXX.json"
gc = gspread.service_account(json_file_path)
spreadsheet_url = "[스프레드시트 URL]"
doc = gc.open_by_url(spreadsheet_url)

worksheet = doc.worksheet("시트1")
worksheet.update([['API 테스트']], 'A1')
```

위와 같이 작성하고 실행하면 아래와 같이 A1 열에 글자가 들어간 것을 확인하실 수 있습니다.

<br>

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/543bf182-58ca-435f-a5c9-d47feaf381d0)

<br>

&nbsp;이렇게 간단하게 구글 스프레드시트를 연동해서 파이썬으로 API를 이용하는 것까지 알아보았습니다. 다음에는 실제 데이터를 이용해서 업데이트하는 방법을 알아봅니다.