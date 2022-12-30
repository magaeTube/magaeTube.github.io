---
title:  "[Python] Jupyter Notebook 실행 및 백그라운드 실행"
date:   2020-09-14 00:00:00 +0900
categories:
  - Python
tags:
  - Python
  - Jupyter

toc: true
toc_sticky: true
toc_label: "Jupyter Notebook"
header-mask: true
---



&nbsp;안녕하세요 마개입니다.  
이전에 가상환경에서 Jupyter를 설치하였습니다. 이번에는 Jupyter Notebook을 실행시켜보도록 하겠습니다.
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/209921388-b7dcbeaf-d060-4d3e-865d-2700403002cb.png)

<br><br>

# Jupyter Notebook 실행하기

Jupyter가 설치된 가상환경에서 다음을 실행합니다.

```sh
jupyter notebook
```

![image](https://user-images.githubusercontent.com/78892113/210044700-5677b121-111a-43b4-9e79-b85ce9b00a3e.png)

해당 명령어를 실행하면 **root 권한에서는 실행할 수 없다**고 합니다. Root에서 실행해보기 위해 **`--allow-root`** 옵션을 추가하여 root 권한에서도 실행되도록 설정을 해봅시다

<br>

```sh
jupyter notebook --allow-root
```

![image](https://user-images.githubusercontent.com/78892113/210044712-8d7a586c-cbe2-47bd-bc45-c8468ff11c4a.png)

실행을 하면 저의 경우에는 위와 같이 나옵니다. 그러나 웹으로 들어가도 제대로 되지 않는 것을 보실 수 있습니다. (자동으로 뜨지 않는다면 위의 이미지 중간에 **http://localhost:8888/?token=....** 으로 되어 있는 부분을 복사하여 웹페이지에서 들어가 봅니다.)  
환경설정을 해줘야 하는데 **ctrl + c**를 눌러서 취소시키고 환경설정 **`config`** 파일을 수정합니다.

<br>

```sh
jupyter notebook --allow-root --generate-config
```

![image](https://user-images.githubusercontent.com/78892113/210044728-f569476d-6f1a-4c99-9201-ff78328055fa.png)

이러면 **`config`** 파일이 생성되는데 config 파일을 수정합니다.  
(아래 'Writing default config to: ' 뒤에 쓰여있는 부분이 config 파일이 있는 경로입니다. OS 환경마다 다를 수 있습니다.)

<br>

```sh
vi /root/.jupyter/jupyter_notebook_config.py
```

![image](https://user-images.githubusercontent.com/78892113/210044731-ed770f26-d222-4e68-8ffd-90726016eaf3.png)

**`config`** 파일 최하단에 위와 같이 입력하고 저장합니다. 빨간 박스 안에는 **해당 서버의 IP**를 입력합니다. 해당 부분을 통해 외부에서 웹 브라우저를 통해서 접속할 수가 있습니다. 

<br>

```sh
jupyter notebook --allow-root --config /root/.jupyter/jupyter_notebook_config.py
```

해당 명령어는 **`jupyter_notebook_config.py`**에 작성한 내용을 적용하면서 jupyter notebook을 실행시키는 것입니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/210044734-73457ed8-4c4c-4a28-8553-8191b73a448d.png)

위에 나오는 URL로 접속합니다.

<br>

![image](https://user-images.githubusercontent.com/78892113/210044739-537483b1-3027-4e1e-843f-0abcf80480be.png)

그러면 위와 같이 jupyter notebook에 제대로 접속된 것을 확인하실 수 있습니다. 

<br><br>

# 시작 경로 변경하기

이번에는 실행했을 때 나오는 폴더의 위치를 변경해봅니다. ctrl + c를 눌러서 서버를 종료 시킨 후에 **`config`** 파일을 변경합니다. 

<br>

![image](https://user-images.githubusercontent.com/78892113/210044745-c6e95f7c-2ac5-441e-842b-e452ac48ff9a.png)

**`c.NotebookApp.notebook_dir`**은 Jupyter Notebook이 실행될 때 기본으로 설정되는 디렉터리입니다. 이렇게 작성하고 다시 노트북을 실행합니다 

<br>

![image](https://user-images.githubusercontent.com/78892113/210044748-1e4eac48-596f-4489-bc4d-3a7f2828f52e.png)

노트북을 실행하면 시작 경로가 변경된 것을 확인하실 수 있습니다.

