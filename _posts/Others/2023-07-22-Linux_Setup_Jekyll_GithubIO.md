---
title:  "[Linux] Ubuntu에 Jekyll 설치하기 (Github IO)"
date:   2023-07-22 00:00:00 +0900
categories:
  - Others
tags:
  - Linux
  - Ubuntu

---

&nbsp;안녕하세요 마개입니다.  
새로운 데스크탑을 맞추고 Linux를 이용하기 위해서 Ubuntu 22.04를 설치했습니다. 여기에 새로운 개발 환경들을 세팅하고 기존에 운영하고 있던 Github IO를 이용하기 위해 Jekyll도 설치하려고 합니다. 이번에는 이 설치 과정들을 봅니다.    
{: .notice--info}

<br><br>

# Ruby 설치

Jekyll는 Ruby 환경에서 돌아가기 때문에 Ruby를 설치해야 합니다. 이에 따라 먼저 이 과정을 진행합니다.

```sh
$ sudo apt-get update
$ sudo apt-get install ruby-full build-essential zlib1g-dev
```

<br>

Ruby가 설치되었다면 Ruby의 패키지 관리자인 gem을 이용해서 진행합니다. 이 때 root유저로 설치되는 것을 방지하기 위해 유저 계정에 gem 설치 디렉터리를 설정합니다. 이는 환경 변수로 `~/.bashrc`에 설정합니다.

```sh
$ echo '#Install Ruby Gems to ~/gems' >> ~/.bashrc
$ echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
$ echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
$ source ~/.bashrc
```

<br>
위와 같이 진행을 했다면 이제 Jekyll를 설치합니다. 

<br><br>

# Jekyll 설치

Jekyll는 위에서 설정했던 gem을 이용해서 설치하도록 합니다. 

```sh
$ gem install jekyll bundler
```

<br><br>

# 의존성 버전 패키지 설치 및 실행하기

기존에 만들어놓았던 Github IO가 있다면 해당 프로젝트를 clone을 해온 뒤에 작업합니다. 

```sh
$ cd 해당 프로젝트
# 의존성 버전 패키지 설치
$ bundle install
# 실행하기 
$ bundle exec jekyll serve
```

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/e4c038ac-3581-40c5-86c7-a0d7d597282f)

<br>

실행했을 때 위와 같은 에러메시지가 발생한다면 다음과 같이 webrick을 설치합니다.

```sh
$ bundle add webrick
```

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/3aec2e72-ade9-4e58-be25-476fe81afb87)

<br>

이후에 다시 실행을 합니다. 

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/dfff9c87-b0e2-41fe-87ed-b4457035fe08)

별다른 에러 메시지 없이 실행된다면 <a href="http://localhost:4000">http://localhost:4000</a> 에서 확인해서 잘 나오면 완료입니다.