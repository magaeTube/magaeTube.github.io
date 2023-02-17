---
title:  "[Linux] 서버 간의 파일 실시간 동기화"
date:   2023-01-06 00:00:00 +0900
categories:
  - Others
tags:
  - Linux
  - Ubuntu

---

&nbsp;안녕하세요 마개입니다.  
Linux 서버 2개가 있을 때 2개 서버 간에 양방향으로 파일을 동기화하는 방법에 대해 알아봅니다. 서버는 A와 B가 있다고 가정하겠습니다.   
{: .notice--info}

<br>

# lsyncd, rsync 설치 및 대상 디렉터리 생성 (A, B서버 모두)

```sh
$ sudo apt-get update
$ sudo apt-get install lsyncd rsync

$ lsyncd --verion
Version: 2.2.3

$ rsync --version
rsync  version 3.2.3  protocol version 31
Copyright (C) 1996-2020 by Andrew Tridgell, Wayne Davison, and others.
Web site: https://rsync.samba.org/
Capabilities:
    64-bit files, 64-bit inums, 64-bit timestamps, 64-bit long ints,
    socketpairs, hardlinks, hardlink-specials, symlinks, IPv6, atimes,
    batchfiles, inplace, append, ACLs, xattrs, optional protect-args, iconv,
    symtimes, prealloc, stop-at, no crtimes
Optimizations:
    SIMD, no asm, openssl-crypto
Checksum list:
    xxh128 xxh3 xxh64 (xxhash) md5 md4 none
Compress list:
    zstd lz4 zlibx zlib none

rsync comes with ABSOLUTELY NO WARRANTY.  This is free software, and you
are welcome to redistribute it under certain conditions.  See the GNU
General Public Licence for details.

# 동기화할 디렉터리 생성
$ sudo mkdir /data
$ sudo chown -R magae:magae /data

```

<br>

# ssh-keygen 공유 (A, B서버 모두)

```sh
# 키 생성
$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/magae/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/magae/.ssh/id_rsa
Your public key has been saved in /home/magae/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:oeu9u..JPkyo4 magae@magae-OptiPlex-7071
The keys randomart image is:
+---[RSA 3072]----+
|       .+=+      |
...
|E= o.+B*=o       |
+----[SHA256]-----+

# 키 전달
# A서버에서는 "상대서버IP"에 B서버의 IP를 입력
# B서버에서는 "상대서버IP"에 A서버의 IP를 입력
$ ssh-copy-id -i /home/magae/.ssh/id_rsa.pub magae@상대서버IP

# 접속 확인
# 아래 명령어를 작성했을 때 비밀번호를 묻지않고 접속된다면 성공
$ ssh magae@상대서버IP
```

<br>

# lsync config파일 작성 (A, B서버 모두)

```sh
$ cd /etc
$ sudo mkdir lsyncd
$ cd lsyncd
$ sudo vim lsyncd.conf.lua
settings {
        logfile="/var/log/lsyncd/lsyncd.log",
        statusFile="/var/log/lsyncd/lsyncd-status.log",
        statusInterval=20,
        nodaemon=false,
		insist=true
}       

sync {
        default.rsyncssh,
        source="/data",                    -- 동기화를 하고자 하는 디렉터리
		--target="magae@[IP주소]:/home/magae/tmp",       -- 2.2.3버전이 아닌 이전 버전에서 작성하는 방식
		host="magae@[IP주소]",      -- 원격서버의 host (A서버에서는 B서버로 작성하고 B서버에서는 A서버로 작성)
        targetdir="/data",      -- 동기화하고자 하는 원격서버의 디렉터리
        delay=1,
        delete=true,            -- 파일이 지워지는 것도 동기화하고 싶다면 true, 아니면 false 
        rsync={
                archive=true,
                compress=true,
                verbose=true,
                _extra={"--bwlimit=730"},
				rsh="ssh -i /home/magae/.ssh/id_rsa -o StrictHostKeyChecking=no"
        }       
}     
```

<br>

# lsync log 디렉터리 및 파일 생성 후 시스템 재시작

```sh
$ cd /var/log
$ sudo mkdir lsyncd
$ cd lsyncd
$ sudo touch lsyncd.log
$ sudo touch lsyncd-status.log
$ sudo systemctl restart lsyncd
$ sudo systemctl enable lsyncd
```

<br>

# lsync 로그를 통해 확인

```sh
$ tail -1000f lsyncd.log
Fri Jan  6 10:43:41 2023 Normal: --- Startup, daemonizing ---
Fri Jan  6 10:43:41 2023 Noraml: recursive startup rsync: /data/ -> magae@[IP주소]:/data/
...

Fri Jan  6 10:43:41 2023 Normal: Startup of "/data/" finished: 0
```

별다른 에러메시지가 없다면 제대로 설정되었습니다.

<br>

# 테스트
```sh
# A서버
$ cd /data
$ vim a.txt
test
```

파일을 저장하고 B서버에서 파일이 생성되었는지 확인해보면 동기화된 것을 보실 수 있습니다. 반대로 B서버에서 파일을 생성해서 A서버에 동기화 되는지도 확인해봅니다. 파일을 생성하는 것뿐만 아니라 수정, 삭제를 해도 동기화됩니다.