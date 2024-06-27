---
title:  "[Linux] Ubuntu에 개발환경 세팅하기"
date:   2023-07-22 00:00:00 +0900
categories:
  - Others
tags:
  - Linux
  - Ubuntu
toc: true
toc_sticky: true
toc_label: "개발 환경 세팅"

---

&nbsp;안녕하세요 마개입니다.  
새로운 데스크탑을 맞추고 Linux를 이용하기 위해서 `Ubuntu 22.04`를 설치했습니다. 여기에 제가 평소에 개발하는 환경들을 세팅하려고 합니다. 설치하는 툴이나 방법들이 저에게 필요한 것들이기 때문에 여러분과는 다를 수 있다는 점을 알아주시면 됩니다.    
{: .notice--info}

<br><br>

# Vim

&nbsp;Linux 환경에서는 기본 편집기가 vi로 실행이 됩니다. 하지만 이보다 더 좋은 `Vim`을 이용해서 개발을 합니다. Vim은 vi의 호환 버전이라고 보면 되는데 이를 이용하기 위해 설치해봅니다.

```sh
$ sudo apt-get update
$ sudo apt-get install vim
```

&nbsp;위의 명령어로 간단하게 설치할 수 있는데 저의 OS 환경인 Ubuntu 22.04에는 기본적으로 Vim이 설치되어 있기 때문에 따로 작업을 진행하지는 않았습니다.

<br><br>

# Git

&nbsp;`Git`은 소스 코드의 버전 관리를 위한 툴입니다. Vim은 처음에 설치되어 있지만 Git의 경우 설치되어 있지 않습니다. 그렇기에 직접 설치하도록 하겠습니다.

```sh
$ sudo apt update
$ sudo add-apt-repository ppa:git-core/ppa
$ sudo apt update
$ sudo apt install git
$ git version
```

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/f8d4305d-94fb-4f2c-aaca-b644faffce87)

&nbsp;설치를 진행하면 위와 같은 방식으로 진행이 되고 완료되는 것을 확인하실 수 있습니다.

<br>

## Git User 정보 설정

&nbsp;`Git`을 처음 설치하면 유저명과 이메일 주소를 먼저 설정하고 이용해야 합니다. 이는 Commit과 Push 등 여러 이력을 추적하는데 사용되기 때문이죠. 개발 환경에 따라 다르게 설정할 수 있지만 일단은 Global 세팅을 진행합니다.

```sh
# 유저명과 이메일 주소 설정
$ git config --global user.name magaeTube
$ git config --global user.email magae.tube@gmail.com

# 유저명과 이메일 주소 확인
$ git config user.name
$ git config user.email
```

&nbsp;설정이 완료되면 등록된 정보가 나오는 것을 확인할 수 있습니다.

<br><br>

# 폰트 적용

&nbsp;모든 어플을 사용하는 일반 고객들에게도 `가독성`이 중요하듯이 개발을 하는 개발자들에게도 가독성은 매우 중요합니다. 대문자 I와 소문자 l을 구분한다거나 숫자 0과 대문자 O를 구분한다거나 글자 간의 크기가 일정하다거나 등등 이러한 것들이 `가독성`에 중요할 수 있습니다. 우리나라의 경우 한글도 참고해서 폰트에 신경을 써야하는데 각자가 선호하는 스타일의 폰트를 찾아서 적용해야 합니다.  

&nbsp;저의 경우 네이버의 `D2코딩`을 좋아하기 때문에 이 폰트를 적용해보겠습니다. (여러 코딩용 폰트를 체험해볼 수 있는 **<a href="https://www.codingfont.com/">코딩 월드컵 사이트</a>**가 있으니 여기서 경험을 해봐도 좋을 것 같습니다.)  

&nbsp;`D2코딩`은 **<a href="https://github.com/naver/d2codingfont">네이버 깃헙</a>**에서 다운로드받을 수 있습니다.  

&nbsp;원하는 폰트를 다운로드 받았다면 압축을 해제합니다.

```sh
$ cd ~/Downloads
$ unzip D2Coding-Ver1.3.2-20180524.zip
```

<br>

그러면 여러 `.ttf` 파일들이 생성되는데 이들을 옮겨야 합니다. Ubuntu에서는 `/usr/share/fonts`로 옮기면 됩니다.

```sh
# D2코딩 폰트들을 같이 보관하기 위해 별도의 디렉터리를 생성합니다.
$ sudo mkdir /usr/share/fonts/d2c
$ sudo cp /home/magae/Downloads/D2CodingLigature/D2Coding-Ver1.3.2-20180524-ligature.ttf /usr/share/fonts/d2c/
$ sudo cp /home/magae/Downloads/D2CodingLigature/D2CodingBold-Ver1.3.2-20180524-ligature.ttf /usr/share/fonts/d2c/
$ sudo cp /home/magae/Downloads/D2Coding/D2Coding-Ver1.3.2-20180524.ttf /usr/share/fonts/d2c/
$ sudo cp /home/magae/Downloads/D2Coding/D2CodingBold-Ver1.3.2-20180524.ttf /usr/share/fonts/d2c/
```

<br>

&nbsp;이동한 폰트들을 적용하기 전에 폰트를 관리하는 유틸리티인 `fontconfig` 패키지를 설치하고 적용하도록 합니다.. 이미 설치되어 있다면 넘어갑니다.

```sh
# fontconfig 설치
$ sudo apt install fontconfig

# 폰트 시스템에 적용
$ sudo fc-cache -f -v
```

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/713841f6-9505-4ce3-8aea-bda949a11336)

&nbsp;적용이 완료되면 중간에 위와 같은 문구를 찾을 수 있습니다. 정상적으로 폰트가 적용되었다는 것을 알 수 있습니다.

<br><br>

# Pycharm

&nbsp;저는 현재 데이터 엔지니어로서 일을 하고 있어서 `Pycharm`은 가장 많이 사용하는 개발 툴 중에 하나입니다. Pycharm을 설치하고 유용한 Plugins도 설치해봅니다. 

&nbsp;Pycharm 공식 **<a href="https://www.jetbrains.com/ko-kr/pycharm/download/?section=linux">홈페이지</a>**에 들어가서 각자 원하는 IDE버전과 본인에 맞는 OS를 선택하고 다운로드합니다. 다운로드를 한 후에는 압축을 해제합니다.

```sh
$ cd ~/Downloads
$ tar -xzf pycharm-community-2023.1.4.tar.gz
```

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/f323a175-b1b6-4cbe-b7bf-925d902f8516)

<br>

압축을 해제하면 `Pycharm`을 실행할 수 있습니다.

```sh
$ ./pycharm-community-2023.1.4/bin/pycharm.sh
```

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/9dcf755c-0375-43d4-b9a6-50718875670f)

<br>

그러면 라이센스 동의 여부를 체크하고 설치가 완료되면 아래와 같은 모습을 볼 수 있습니다.

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/c4bf4b4f-8317-4490-ba05-d072b46ec6e0)

<br>

## Applications 생성

&nbsp;이 상태에서 실행하면 Ubuntu의 Applications에는 `Pycharm`이 보이지 않습니다. Applications에서 사용하기 위한 작업을 진행합니다. 떠있는 팝업창은 종료하고 다음 작업을 진행합니다.

```sh
# 심볼릭 링크 생성
$ sudo ln -s ~/Downloads/pycharm-community-2023.1.4/bin/pycharm.sh /usr/bin

# 링크 연결 후 Applications에 생성
$ sudo vim /usr/share/applications/pycharm.desktop

# pycharm.desktop 파일에 아래 내용 작성
[Desktop Entry]
Name=Pycharm
Comment=Pycharm Integrated Development Envrionment
Exec=pycharm.sh
Icon=/home/magae/Downloads/pycharm-community-2023.1.4/bin/pycharm.png
Terminal=false
Type=Application
Categories=Development;pycharm;python;

# 권한 부여
$ sudo chmod 775 /usr/share/applications/pycharm.desktop
```

위와 같은 작업을 진행하면 `Applications`에 Pycharm이 생성된 것을 확인할 수 있습니다.

<br>

## Font

위에서 설치했던 `D2Coding` 폰트를 Pycharm에 적용하도록 합니다. 

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/4e1d8fb5-c620-40c6-ac9e-0039f148e449)

&nbsp;Pycharm에서 `Settings - Editor - Font`로 이동하면 위와 같이 Font를 설정할 수 있습니다. 여기서 D2Coding을 찾아서 설정하면 됩니다. (만약에 리스트에 없다면 Pycharm을 재실행해보시길 바랍니다.)

<br>

## Plugins

개발을 하는데 있어 Pycharm에서 사용할 수 있는 유용한 Plugin을 몇 가지 설치하겠습니다.

<br>

### IdeaVim

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/b7b04225-e1cd-4fc8-ac47-3007edc568bf)

&nbsp;Pycharm Editor에서 <u>Vim 문법을 이용할 수 있게 해주는 Plugin</u>입니다. 이는 Vim 환경에 익숙하고 선호하는 분들에게 필수인 Plugin이라고 볼 수 있습니다.

<br>

### Rainbow Brackets

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/1bdb3ba5-571a-4568-846f-34cc80d9df67)

&nbsp;개발을 하다보면 수많은 괄호들 `({[]})`을 보게 되는데 이를 구별하는게 쉽지 않을 수 있습니다. 그럴 때 이 Plugin을 사용하면 유용합니다. 그러나 형형색색으로 되기 때문에 눈이 부셔서 힘들 수 있다는 의견도 있어서 호불호는 갈릴 수 있습니다.

<br>

### Atom material Icons

![Screenshot from 2023-07-16 17-34-06](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/959bce31-8053-4f90-add9-2a90bd5d8b8b)

&nbsp;개발을 할 때 생성한 또는 기존에 존재하는 파일들의 **확장자**에 따라 Project 트리구조에서 다른 `아이콘`으로 보여주는 기능입니다. 각 확장자를 대표하는 이미지들로 구성되어 있어 확장자를 보지 않고 아이콘을 먼저 봐도 어떠한 파일인지 유추할 수 있도록 도와줍니다.

<br><br>

# Visual Studio Code

&nbsp;Python 작업을 할 때는 Pycharm을 주로 이용하지만 그외 웹이나 다른 작업을 할 때는 `Visual Studio Code`를 많이 이용합니다. 그렇기 때문에 해당 프로그램도 설치를 진행해보겠습니다.  
프로그램 다운로드의 경우 **<a href="https://code.visualstudio.com/download">공식 홈페이지</a>**에서 설치하면 됩니다. 설치가 완료되면 이번에도 Font를 적용하고 유용한 Extensions에 대해 알아보겠습니다.

<br>

## Font

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/3b16c2bd-ac57-4e18-a646-1e1e845e1ba3)

&nbsp;Font의 경우 `Settings - Text Editor - Font`에서 **Font Family**에서 맨 앞에 원하는 Font를 작성하면 됩니다. 저의 경우 맨 앞에 `D2Coding`을 작성하였습니다.

<br>

## Extensions

&nbsp;Pycharm에서는 Plugins을 설치하여 여러 유용한 기능들을 이용한다면 VSC에도 Extensions라는 기능으로 유용한 기능들을 사용할 수 있습니다. 유용한 기능들을 알아보도록 합니다. 

<br>

### Project Manager

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/8a2553b0-788c-48cc-a5ce-7d00a3aceda8)

&nbsp;VSC에서 현재 작업 중인 프로젝트말고 다른 프로젝트를 이용하고 싶다면 워크스페이스로 원하는 프로젝트를 드래그해서 가져오거나 Open Folder로 프로젝트를 가져와야 합니다. 하지만 해당 Extension을 이용하면 더욱 편하게 이용할 수 있습니다. Extensions에서 `Project Manager`로 검색하면 위와 같은 항목을 볼 수 있습니다. 이를 설치합니다. 

<br>

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/c2721e92-cde7-4ba9-87b7-256a9e399fe1)

설치가 완료되면 좌측 메뉴 맨 하단에 새로운 메뉴가 추가된 것을 확인할 수 있습니다.

<br>

### Code Runner

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/15180616-37da-4adb-b8e0-84dfa010f595)

&nbsp;`Code Runner`는 IDE처럼 Java, C, Python 등 여러 언어를 컴파일할 수 있도록 지원해주는 기능입니다. Marketplace에서 Code Runner를 검색하고 설치합니다. 

<br>

### Error Lens

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/d0885bda-57ff-4c9f-a53f-92636dd45a23)

코드에 Error가 있을 경우 해당 줄에 Overlay 형식으로 띄워줍니다. 기본적인 Error와 라이브러리 미참조 등 왠만한 오류를 잡을 수 있습니다.

<br>

### Output Colorizer

![Output Colorizer](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/123c90bd-827b-4f91-b7ca-e4cf151d57e9)

실행 결과인 Output 창의 글자들에 색깔을 입혀줘서 가독성을 향상시켜줍니다.

<br>

### Log File Highlighter

![Log File Highlighter](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/f55867f1-6503-4d95-a006-88da9cda7965)

로그 파일에 색깔을 입혀줘서 가독성을 향상시켜주는 기능입니다.

<br>

### Rainbow CSV

![Rainbow CSV](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/dfeb1157-9e78-464a-a538-249bc353218f)

`CSV`의 데이터는 한눈에 파악하기 쉽지 않은데 해당 기능은 색깔을 통해 데이터를 파악하기 쉽게 도와주는 기능입니다.

<br>

### Better Shell Syntax

![Better Shell Syntax](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/eb9bf693-8629-47f1-b6c4-70fbd104496a)

해당 기능은 `BASH` 쉘 스크립트의 코드 하이라이트를 개선해주는 기능입니다.

