---
title:  "[Python] Jupyter Notebook 서비스 생성"
date:   2020-09-17 00:00:00 +0900
categories:
  - Python
tags:
  - Python
  - Jupyter

toc: true
toc_sticky: true
toc_label: "Jupyter Notebook 서비스"
header-mask: true
---



&nbsp;안녕하세요 마개입니다.  
Jupyter Notebook을 설치 완료하고 백그라운드 실행을 완료하였다면 서비스를 등록하여 서버가 재가동되더라도 실행되도록 해봅니다.
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/209921388-b7dcbeaf-d060-4d3e-865d-2700403002cb.png)

<br><br>

# service 생성

Ubuntu 환경에서 진행을 하고 여기서는 service 파일을 생성해서 진행해봅니다.

<br>

```sh
vi /etc/systemd/system/jupyter.service
```

<br>

```
[Unit]
Description=Jupyter Notebook Server

[Service]
Type=simple
PIDFile=/run/jupyter.pid
User=root
ExecStart=/root/anaconda3/bin/jupyter-notebook
WorkingDirectory=/home/hacdev/hacademy

[Install]
WantedBy=multi-user.target
```

여기에서 기존에 설정한 `config` 파일 내용대로 하고 싶다면 아래와 같이 수정합니다.여기서 **`User`**와 **`ExecStart`**의 root는 개인별로 jupyter notebook이 설치되어있는 위치로 변경합니다. 

<br>

```
[Unit]
Description=Jupyter Notebook Server

[Service]
Type=simple
PIDFile=/run/jupyter.pid
User=root
ExecStart=/root/anaconda3/bin/jupyter-notebook --allow-root --config /root/.jupyter/jupyter_notebook_config.py

[Install]
WantedBy=multi-user.target
```

<br><br>

# service 등록

위와 같이 service를 생성했다면 이를 등록합니다.

```sh
systemctl daemon-reload
systemctl enable jupyter.service
systemctl start jupyter.service
```

<br><br>

# 확인

![image](https://user-images.githubusercontent.com/78892113/209921409-e32fe0b6-48f4-4fe0-a984-75bd8002cc51.png)

웹 브라우저를 통해 비밀번호를 넣고 잘 들어가는지 확인해봅니다.