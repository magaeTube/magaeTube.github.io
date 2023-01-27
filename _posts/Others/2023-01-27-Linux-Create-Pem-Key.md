---
title:  "[Linux] 비밀번호 없이 SSH 원격 접속하기 (PEM Key 생성)"
date:   2023-01-27 00:00:00 +0900
categories:
  - Others
tags:
  - Linux
  - Ubuntu

---

&nbsp;안녕하세요 마개입니다.  
Linux 상에서 원격서버(마찬가지로 Linux)에 SSH 접속을 할 때 계정과 비밀번호를 요구합니다. 하지만 자동화를 하는 과정에서는 계정과 비밀번호를 입력할 수 없으니 (또는 계정과 비밀번호를 하드코딩하는 것은 보안상에 문제가 됨.) OpenSSH와 사용되는 Pem Key를 이용해서 접근하는 것이 좋습니다. 이번에는 해당 부분을 확인합니다.   
{: .notice--info}

<br>

```sh
# [클라이언트]에서 2048 비트의 RSA키 생성
# ssh-keygen -t rsa -b 2048 -f [파일이름]
$ ssh-keygen -t rsa -b 2048 -f druid

# [원격 서버]에서 authorized_keys 파일 생성 (없는 경우)
$ mkdir ~/.ssh/
$ chmod 700 ~/.ssh/
$ touch ~/.ssh/authorized_keys
$ chmod 600 ~/.ssh/authorized_keys

# [클라이언트]에서 public key 복사
# cat ~/.ssh/[파일이름].pub
$ cat ~/.ssh/druid.pub
ssh-rsa AAAAB3Nza(.....생략.....)bMxxJDJJ magae@magae

# [서버]에서 authorized_keys에 붙여넣기 
$ vim ~/.ssh/authorized_keys

# [클라이언트]에서 private key를 PEM 포맷으로 변경
# ssh-keygen -f [파일이름].pub -e -m pem
$ ssh-keygen -f druid.pub -e -m pem
-----BEGIN RSA PUBLIC KEY-----
(.............................생략..............................)
-----END RSA PUBLIC KEY-----

# 위의 결과값 복사하여 PEM file 생성
$ vim druid.pem

# 원격 접속 테스트
$ ssh -i druid.pem ubuntu@[원격서버 IP]
```
