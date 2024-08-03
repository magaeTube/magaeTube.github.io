---
title:  "[RPA] UiPath 엑셀 오류 (System.InvalidCastException: Unable to cast COM object of type)"
date:   2018-06-21 00:00:00 +0900
categories:
  - UiPath
tags:
  - RPA
  - UiPath
  - Excel
toc: true
toc_sticky: true
toc_label: "엑셀 오류"
---

&nbsp;안녕하세요 마개입니다.  
UiPath를 이용해서 엑셀작업을 할 때 한번쯤은 발생할 수 있는 에러 내용에 대해 공유합니다.  
{: .notice--info}

<br>


## 에러 상황
---

UiPath의 Excel Application Scope를 이용할 때 다음과 같은 에러가 발생할 수 있습니다. 

![image](https://user-images.githubusercontent.com/78892113/143771933-0ffee76e-09d6-4105-8930-d0d3f3bb39ab.png)


위와 같이 **System.InvalidCastException: Unable to cast COM object of type 'Microsoft.Office.Interop.Excel.ApplicationClass' to interface type 'Microsoft.Office.Interop.Excel._Application'** 오류가 날 때의 처리 방법을 알아봅니다.

<br><br>

## 처리 방법
---

* 위에서 노란색으로 친 CLSID를 복사합니다.
* 레지스트리 편집기를 실행합니다. (**regedit**)
* '**HKEY_CLASSES_ROOT**' 클릭 후 ctrl+F 으로 CLSID(1번에서 복사한 CLSID값) 번호를 찾습니다.
* 결과를 찾았다면 하단 디렉토리에 '**TypeLib**' 클릭하면 오른쪽에 나오는 기본값 데이터를 복사합니다.

![image](https://user-images.githubusercontent.com/78892113/143772400-76488bde-a385-4c7f-a2b1-a544c9e5dac3.png)

* 다시 레지스트리 편집기에서 **[HKEY_CLASSES_ROOT] - [WOW6432Node] - [TypeLib]** 에서 위에 복사한 CLSID를 검색합니다. 경로가 나오면 root를 열어 '**Win32**' 폴더로 이동합니다.

![image](https://user-images.githubusercontent.com/78892113/143772438-e06c91ac-60a9-4ad2-8b2e-de1584afb186.png)

* 기본값이 데이터에 적혀있는 경로가 있는지 확인합니다.
* 없다면 엑셀이 있는 경로로 변경합니다.

![image](https://user-images.githubusercontent.com/78892113/143772489-ca50fa22-611c-4e5d-ad8b-ac28fbcc686b.png)

* 변경 후 다시 되는지 확인합니다.