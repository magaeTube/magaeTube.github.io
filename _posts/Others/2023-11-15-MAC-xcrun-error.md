---
title:  "[MAC] MacOS 업그레이드 후 xcrun error"
date:   2023-11-15 00:00:00 +0900
categories:
  - Others
header-mask: true
---

&nbsp;안녕하세요 마개입니다.  
2017년형 맥북을 이용 중인데 필요에 따라 OS를 업그레이드해야 했습니다. (Ventura) 업그레이드는 정상적으로 진행되었지만 이후에 터미널에서 발생한 에러에 대해 알아봅니다.
{: .notice--info}

<br><br>

&nbsp;OS를 업그레이드한 이후로 pip와 python3 명령어 등 여러 명령어를 사용하면 아래와 같은 이슈가 발생하고 있습니다. 이것은 파이썬 관련뿐만 아니라 git이나 다른 것을 실행해도 발생하는 문제입니다. 에러의 내용은 다음과 같았습니다.

```sh
xcrun: error: invalid active developer path
```

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/7fe5cb5f-eb61-4873-a93e-b96c315cfc04)
{: width="80%"}

많은 사람들이 겪고 있는 문제인 것 같은데 이것에 대한 해결 방법을 알아봅니다. 이는 각 프로그램의 문제가 아니라 CommandLineTools를 식별하지 못해서 발생하는 문제입니다.  
해결은 "xcode-select --install" 명령어를 이용해 다시 설치해 줍니다.

<br>

```sh
xcode-select --install
```

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/02c81927-02c2-4d53-a23e-fe37a343fd20)
{: width="80%"}

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/8bba8672-be77-4a8f-9f39-2f5f711ebac3)
{: width="80%"}

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/7bd72495-b049-41d2-9c2f-69f00982f998)
{: width="80%"}

위 명령어를 입력하면 설치 여부를 묻는 팝업창이 뜨고 여기서 "설치" 버튼을 눌러서 설치합니다. 소요시간은 각자에 따라 다르니 기다려줍니다.

<br>

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/6ada82e1-4028-421b-8f0f-e15f3e3733f5)
{: width="80%"}

완료되면 "git"이나 다른 명령어를 이용해서 확인해봅니다.