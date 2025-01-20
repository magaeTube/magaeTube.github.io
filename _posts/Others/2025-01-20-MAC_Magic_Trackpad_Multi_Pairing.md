---
title:  "[macOS] 맥북 트랙패드 블루투스 멀티 페어링"
date:   2025-01-20 00:00:00 +0900
categories:
  - Others
tags:
  - MAC
  - macOS
  - Trackpad
toc: true
toc_sticky: true
toc_label: "매직 트랙패드 블루투스 멀티 페어링"
header-mask: true
---

&nbsp;안녕하세요 마개입니다.  
집에 매직 트랙패드가 있는데 회사용 맥북과 이번에 구매한 맥북 양쪽에서 사용해야 하는 상황입니다. 하지만 블루투스 연동이 되지 않아 당황하면서 해결책을 찾았던 방법에 대해 공유합니다.
{: .notice--info}

## 현상
&nbsp;애플 매직 트랙패드는 (여러 맥북 간) `멀티 페어링`을 지원하지 않기 때문에 여러 맥북을 소지하고 있는 사용자에게는 어려움을 제공하고 있습니다. 멀티 페어링을 지원하지 않기 때문에 기기가 여러 개일 때 트랙패드의 사용이 쉽지 않았습니다. 그래서 방안에 대해 찾다 보니 <u>기기 하나당 트랙패드 하나를 사용</u>하라는 의견(ㅋㅋㅋ), 트랙패드를 사용할 주체를 <u>옮길 때마다 unpairing & pairing</u>을 하라는 의견, <u>유료 툴을 써서 사용</u>하라는 의견 등 여러 의견을 봤지만 그중에 제일 좋아 보이는 것, 트랙패드 하나로 해결할 수 있고 유료 툴을 쓰지 않아도 되는 방법에 대해 글을 남겨놓습니다.

<br><br>

## 코드를 이용해서 만들기
### Blueutil
&nbsp;해결 방법은 코드를 작성해서 이용하는 것입니다. 코드라고 하면 어렵게 생각하실 수도 있지만 이를 따라 해보면 어렵지 않다는 것을 아실 수 있습니다. `Blueutil`은 macOS에서 <u>블루투스에 대한 명령을 CLI로 내릴 수 있게 해주는 오픈소스</u>입니다. 이 오픈소스를 이용해서 블루투스 기기를 페어링 하고 언 페어링 하고 동작할 수 있게 해주는 것이죠. 이를 사용해 보겠습니다.

<br>

### BlueUtil 설치
&nbsp;`Blueutil`은 맥북의 패키지 관리자인 `Homebrew`를 이용해서 쉽게 설치할 수 있습니다.
  
```sh
# install
$ brew install blueutil

# update
$ brew update
$ brew upgrade blueutil

# uninstall
$ brew remove blueutil

# help
$ blueutil --help
```

![image](https://github.com/user-attachments/assets/8be66096-12c6-411b-821c-712bace8db54)
&nbsp;위의 명령어를 이용해서 `Blueutil`을 설치합니다. 설치 완료되면 help를 이용해서 잘 동작하는지 확인합니다.

<br>

### 트랙패드 Address 확인하기 
&nbsp;내 트랙패드의 Address를 알아야 양쪽 맥북에 설정하고 `Blueutil`을 이용해서 블루투스 명령을 내릴 수 있습니다. 그렇기에 현재 트랙패드가 연결된 맥북에서 다음 명령어를 이용해 `Address`를 알아냅니다.
  
```sh
$ blueutil --connected
```

![image](https://github.com/user-attachments/assets/b83c8fb7-1652-4ac8-af9c-a724ac747aa5)
&nbsp;위 명령어를 입력하면 결과가 나옵니다. (저의 정보가 나오기 때문에 위에서는 보이지 않습니다.) 그 정보에서 `address:XX-XX-XX-XX-XX-XX` 형태가 있는 것을 확인할 수 있습니다. **address:** 이후에 나오는 주소를 이용해야 합니다.

<br>

### 자동화 스크립트 작성
&nbsp;간단한 스크립트로 블루투스를 제어할 수 있도록 합니다. 이 스크립트는 모든 기기에 작성해야 합니다. 터미널을 열어 쉘 스크립트 파일을 만들 위치로 이동합니다. 그리고 거기에서 vim 에디터를 이용해서 파일을 하나 생성합니다.
  
```sh
$ vim pairing_magic_trackpad.sh
```
&nbsp;저의 경우에 파일의 이름을 위와 같이 `pairing_magic_trackpad.sh`로 지정했습니다. 그러면 INSERT 모드("i" 키)로 들어가고 아래의 스크립트를 작성합니다.
  
```shell
#!/bin/zsh

export BLUEUTIL=`which blueutil`
export DEVICE_ID=XX:XX:XX:XX:XX:XX

res=$($BLUEUTIL --is-connected $DEVICE_ID)

if [[ "$res" = '1' ]] then
$BLUEUTIL --unpair $DEVICE_ID
exit 0
fi

$BLUEUTIL --unpair $DEVICE_ID
sleep 1
$BLUEUTIL --pair $DEVICE_ID
sleep 1
$BLUEUTIL --connect $DEVICE_ID
```
  
![image](https://github.com/user-attachments/assets/2a823115-1dab-4bff-883a-d12d97920a7e)
&nbsp;위에서 `XX:XX:XX:XX:XX:XX`는 이전 단계에서 복사한 주소에서 "-" 대신 ":"로 변경해서 작성하면 됩니다.
  
```sh
$ chmod +x pairing_magic_trackpad.sh
```
&nbsp;내용을 다 입력했다면 위의 명령어를 통해 실행 권한을 부여합니다. 

<br>

## 페어링 전환하기
&nbsp;모든 맥북 기기에 위와 같은 스크립트를 작성해서 준비가 되었다면 이제 다음의 순서로 하나씩 진행합니다. 이때의 전제 조건은 변경하고 싶은 기기에 한 번은 페어링 한 적이 있어야 합니다.

* 연결을 해제하고 싶은 맥에서 위에서 만든 스크립트를 실행합니다. (`unpair`)
* 연결을 하고 싶은 맥에서 위에서 만든 스크립트를 실행합니다. (`pair`)
  
&nbsp;순서는 위와 같이 진행하고 스크립트를 실행하는 명령어로 아래 명령어를 수행합니다.
  
```sh
$ ./pairing_magic_trackpad.sh
```
  
&nbsp;이러면 기존에 페어링 되어 있던 기기에서 트랙패드가 해제되고 연결하고 싶은 기기에 페어링 됩니다. 잘 동작하지 않는다면 트랙패드의 전원을 다시 키고 진행해 봅니다.





