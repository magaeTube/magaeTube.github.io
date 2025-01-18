---
title:  "[블로그] GitHub Pages와 Jekyll 이용하여 테크 블로그 만들기"
date:   2021-09-23 00:00:00 +0900
categories:
  - Blog
tags:
  - Jekyll
toc: true
toc_sticky: true
toc_label: "테크 블로그 만들기"
header-img: 
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
&nbsp;기존에 네이버 블로그를 이용하여 IT 기술들에 대한 내용을 작성했는데 IT 기술을 작성하는 것은 다른 블로그에 작성하고자 합니다. 네이버에서 코드를 작성할 때 가독성이 떨어지는 문제도 있고 원하는대로 꾸미기가 힘들었기 때문입니다. 이에 대한 내용들을 공유합니다. 
{: .notice--info}

## 플랫폼 선정하기
&nbsp;테크 블로그로서 Tistory, Velog, Medium, GitHub Pages 등 여러 플랫폼들이 있지만 그 중에도 `GitHub Pages`를 이용하기로 결정했습니다.  

<br>

&nbsp;`GitHub Pages`를 선택한 이유는 몇 가지 있습니다.  
* 코드를 작성하기 좋고 가독성이 좋다.
* 개발자로서의 포트폴리오를 만들고 싶다.
* GitHub 코드와 연결하여 사용하고 싶다
* 광고를 붙여야 한다. (이왕이면...)
  
&nbsp;그래서 다른 플랫폼들도 훌륭한 플랫폼이지만 `GitHub Pages`를 이용해 보기로 했습니다. 이제 만들어봅시다.

<br>

## Repository 생성하기
![image](https://github.com/user-attachments/assets/1f2d4da7-1445-4f4f-9d1f-ca23abafc3a3)
&nbsp;첫 번째로는 저장 공간을 만드는 것입니다. GitHub에 로그인하여 좌측 상단에 `New`를 클릭하여 신규 Repository를 생성합니다.

<br>

![image](https://github.com/user-attachments/assets/12a0c936-2b5d-41b3-8de2-f054e7c4f153)
&nbsp;여기서 중요한 것은 **Repository name**입니다. 여기 이름은 `GitHub유저명.github.io`로 해주셔야 제대로 동작합니다. 저의 경우 **"magaeTube.github.io"**로 생성했습니다. 하단에 "Add a README file"도 체크하고 `Create repository`를 클릭합니다. 이러면 일단 웹페이지는 완성입니다. 당장 브라우저를 열어 각자의 github.io 주소 `https://GitHub유저명.github.io` 로 이동해봅니다.

<br>

![image](https://github.com/user-attachments/assets/93482af9-6bbf-40db-85c1-1316a4691e3c)
&nbsp;이러면 아주 심플한 모습이 나온 것을 확인하실 수 있습니다. 이대로 이용하면 매우 허전하니 여기에 테마를 적용하고 내용을 붙여보겠습니다.

<br>

![image](https://github.com/user-attachments/assets/2870d4ea-2762-4043-a63f-9a6cd5671451)
&nbsp;다시 GitHub으로 돌아와서 Repository를 Clone해서 꾸며보겠습니다. 우측 상단에 `Code`를 클릭하고 **HTTPS**의 주소를 복사합니다.

<br>

![image](https://github.com/user-attachments/assets/4b17d399-7bcb-4e77-bd0d-d38216de070d)
&nbsp;복사한 주소를 원하는 로컬 위치에 놓고 clone합니다.

<br><br>

## Jekyll 설치하기
&nbsp;저는 Jekyll을 이용하여 블로그를 꾸미기로 생각하고 설치 작업을 진행합니다.

<br>

&nbsp;터미널을 열고 아래 명령어를 실행합니다.
```sh
$ gem install jekyll bundler
```

![image](https://github.com/user-attachments/assets/3c275f0e-3ebc-452c-a6f0-fd5cb57961b0)
&nbsp;설치가 제대로 완료되었는지 로그를 통해 확인합니다.

<br>

![image](https://github.com/user-attachments/assets/c608cfec-3a5e-4afb-8402-c0f5cb8a4029)
&nbsp;설치 과정에서 위와 같은 권한 에러가 발생한다면 아래의 블로그를 참고하여 설치를 진행합니다.
* <a href="https://jojoldu.tistory.com/288">https://jojoldu.tistory.com/288</a>

<br><br>

## Jekyll 생성

![image](https://github.com/user-attachments/assets/822e9d79-c829-4137-abbd-8f43755afea7)
&nbsp;설치가 완료되면 github.io 디렉터리로 이동해서 `Jekyll`을 생성합니다.

<br>

```sh
$ jekyll new ./
```
&nbsp;해당 명령어를 실행하기 전에 디렉토리에 있는 파일들을 전부 삭제합니다. (README.md 포함)

<br><br>

## bundle install

![image](https://github.com/user-attachments/assets/f0f1ab39-9612-4c60-b211-f1f770df94ce)
&nbsp;완료가 되었다면 다음으로는 `bundle`을 설치합니다.

<br>

```sh
$ bundle install
```
&nbsp;위의 명령어를 입력해서 설치를 진행합니다.

<br>

![image](https://github.com/user-attachments/assets/7cea3ede-1649-44de-986d-cf9a770185bb)
&nbsp;설치 시에 위와 같은 에러(`uninitialized constant DidYouMean::SPELL_CHECKERS (NameError)`)가 발생할 수 있습니다.

<br>

![image](https://github.com/user-attachments/assets/69d014db-4dbd-484f-afd3-6e190962491e)
&nbsp;이럴 때 bundler를 업데이트해서 해결할 수 있습니다.

```sh
$ bundle update --bundler
```
&nbsp;위의 명령어로 업데이트하고 다시 실행합니다.

<br><br>

## 로컬에서 Jekyll 확인하기

![image](https://github.com/user-attachments/assets/e569177c-7547-4d29-a9cd-5443a7f4d895)
&nbsp;완료되었다면 서버를 띄워서 확인을 해보겠습니다.

<br>

```sh
$ bundle exec jekyll serve
```
&nbsp;위의 명령어를 입력해서 서버를 실행합니다.

<br>

![image](https://github.com/user-attachments/assets/c6ce84e6-6f3b-48b8-86dd-3429afd91ce2)
&nbsp;실행했을 때 위와 같은 에러가 발생한다면 `csv`를 추가합니다. 추가하고 다시 실행을 진행합니다.

<br>

```sh
$ bundle add csv
```

![image](https://github.com/user-attachments/assets/5f4cdd82-6040-478a-b32c-08165798bf1c)
&nbsp;실행했을 때 혹시 위와 같은 에러가 발생한다면 `webrick`을 추가합니다.

<br>

```sh
$ bundle add webrick
```

![image](https://github.com/user-attachments/assets/3bacf128-45fb-4f61-a0b0-be44397badec)
&nbsp;추가되었다면 다시 실행해봅니다.

<br>

![image](https://github.com/user-attachments/assets/057f7664-ddda-452b-a20d-f0fbbb62d033)
&nbsp;위와 같은 에러가 발생한다면 메인 페이지에서 `Gemfile.lock` 파일을 삭제하고 다시 실행합니다.

<br>

![image](https://github.com/user-attachments/assets/3bd18796-bad9-48dc-a75e-aeb6754fe76c)
&nbsp;위와 같이 뜨게되면 <a href="http://localhost:4000/">http://localhost:4000/</a> 또는 <a href="http://127.0.0.1:4000">http://127.0.0.1:4000</a>으로 접속하여 확인합니다.

<br>

![image](https://github.com/user-attachments/assets/e7851b68-4a93-4ba4-99cf-79b622e6c6c2)
&nbsp;위와 같이 뜨게 되면 정상적으로 처리된 것이고 이제 테마를 찾아서 적용합니다.

<br><br>

## 테마 적용하기
### 테마 선택하기
&nbsp;테마를 적용하기 전에 가장 먼저 해야할 것은 테마를 선택하는 것입니다.  
&nbsp;`Jekyll`을 이용해서 블로그를 꾸미는데 Jekyll은 텍스트를 이용해서 정적 사이트를 만들어주는 것입니다. Jekyll 테마를 고를 수 있는 사이트는 아래와 같이 다양하게 있습니다.  
* <a href="https://jamstackthemes.dev/">https://jamstackthemes.dev/</a>
* <a href="http://jekyllthemes.org/">http://jekyllthemes.org/</a>
* <a href="https://jekyllthemes.io/">https://jekyllthemes.io/</a>
* <a href="https://jekyll-themes.com/">https://jekyll-themes.com/</a>

<br><br>

### 테마 다운로드하기
![image](https://github.com/user-attachments/assets/e0c0be00-0586-4ad7-a120-9fe8f0ab78da)
&nbsp;원하는 테마를 선택했다면 해당 깃허브로 이동해서 소스를 다운로드합니다. 저는 `minimal-mistakes` 테마를 이용했습니다.  
&nbsp;해당 깃허브로 이동하여 ZIP 파일을 다운로드합니다.

<br><br>

### 소스 옮기기
![image](https://github.com/user-attachments/assets/486555ab-2568-4920-9e32-2cb0947d24fe)
&nbsp;해당 ZIP 파일을 받고 압축을 풀어 안에 있는 파일들을 우리가 clone받은 github.io에 복사합니다. (내용은 전부 덮어쓰기합니다.)

<br><br>

### bundle 재설치 및 실행
&nbsp;위에서 작업했던 `bundle`을 설치하고 실행하는 작업을 다시 진행합니다.

<br>

![image](https://github.com/user-attachments/assets/7c76d800-ebcd-4ae6-b74c-25bd336c3376)

<br>

```sh
$ bundle install
```

![image](https://github.com/user-attachments/assets/a7303002-f2ae-43ac-809f-ea3ccf03e1f9)
&nbsp;이제 완료되었으면 다시 <a href="http://127.0.0.1:4000">http://127.0.0.1:4000</a>으로 이동해보겠습니다.

<br>

![image](https://github.com/user-attachments/assets/2d08384a-e700-4026-9882-03172aa53bd6)
&nbsp;그러면 위에서 보듯이 `Minimal Mistakes` 테마가 적용된 것을 확인하실 수 있습니다. 테마를 적용했으면 각자에 맞는 내용으로 바꿔서 진행하면 됩니다.