---
title:  "[DevOps] CI/CD 툴 비교"
date:   2022-03-10 00:00:00 +0900
categories:
  - DevOps
tags:
  - CI/CD
toc: true
header-img: https://user-images.githubusercontent.com/78892113/207373620-73129456-1d05-4457-9016-b22562cb39e5.png
header-mask: true
toc_sticky: true
toc_label: "CI/CD툴"
header-mask: true
---



&nbsp;안녕하세요 마개입니다.  
DevOps 분야에서 필요한 CI/CD 툴들에 대해 간단하게 정리를 하였습니다.
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/207373620-73129456-1d05-4457-9016-b22562cb39e5.png){: .align-center}{: width="80%" height="80%"} 


<br>
  
* <a href="https://ichi.pro/ko/hyeonjae-sayong-ganeunghan-choegoui-ci-cd-dogu-27gaji-194611649728144">참고 URL</a>

<br><br>

# Jenkins
* Java 기반이며 다양한 플랫폼에서 이용 가능함 (Windows, Linux, macOS)
* 무료, 오픈소스이며 GitHub의 16.3K Stars
* 확장성이 좋으며 클라우드 플랫폼과 통합됨
* 병렬 작업 수행하며 복잡한 CD 요구 사항 실현

<br>

# GitHub Action
* 소프트웨어 Workflow를 자동화할 수 있도록 도와주는 도구
* 가격 : Public Repo (무료, Limit제한은 있음), Private Repo (GitHub 종류에 따라 가격있음)
* <a href="https://docs.github.com/en/billing/managing-billing-for-github-actions/about-billing-for-github-actions">About billing for GitHub Actions</a>

<br>

# TeamCity
* Java 기반 CI/CD 파이프라인 도구이며 Windows, Linux 서버에 설치
* 오픈소스 프로젝트에 무료로 사용
* 확장성이 좋으며 클라우드 플랫폼과 통합됨 
* 병렬 빌드를 실행할 수 있으며 파이프라인은 Kotlin 기반 DSL을 사용하여 정의됨
* 가격 : TeamCity Cloud ($45/월), TeamCity Professional (무료, 100개 빌드 구성), TeamCity Enterprise ($1,999/년)
* <a href="https://www.jetbrains.com/ko-kr/teamcity/">TeamCity URL</a>

<br>

# Circle CI
* 다양한 플랫폼에서 이용 (Windows, Linux, macOS)
* 설정하기 쉽고 GitHub, Bitbucket 등 버전 제어 시스템과 함께 사용 가능
* CI/CD 파이프라인을 YAML을 이용하여 “워크플로우”를 제공함
* 병렬 빌드 가능하며 클라우드 플랫폼을 지원함
* 가격 : Cloud Free ($0, 빌드 최대 6000분/월), Cloud Performance ($15/월, 5 users), Cloud Scale ($2,000/월), Self-hosted Server (Custom)
* <a href="https://circleci.com/pricing/">Pricing</a>

<br>

# Travis CI
* 오픈 소스 프로젝트, 클로즈드 소스 프로젝트 지원함
* 파이프라인은 YAML 구문을 이용해 구성함
* 개인 정보 보호 및 보안 솔루션을 위해 자체 호스팅 도구인 Travis CI Enterprise를 선택할 수 있음
* 가격 : Free Trial (30 Day Free Trial), Core ($69/월), Enterprise (Custom)
* <a href="https://www.travis-ci.com/pricing/?_gl=1*1syk3rn*_ga*MTQ1MTEyMjkwOS4xNjQ2ODk5MDU5*_ga_XRYGSZFQ0P*MTY0Njg5OTA1OS4xLjAuMTY0Njg5OTA1OS42MA..">Pricing</a>

<br>

# Bamboo
* CI에 사용되는 도구로 Atlassian에서 개발한 제품 
* Jira, Bitbucket과 같은 도구에서 작동함 
* Repository의 변경 사항을 기반으로 빌드를 트리거할 수 있으며 Bitbucket에서 푸시 알림을 보낼 수 있음
* 가격 : $1200/년
* <a href="https://www.atlassian.com/ko/software/bamboo/pricing">Pricing</a>

<br>

# GoCD
* Thoughtworks에서 제공하는 오픈 소스 CI/CD 서버
* 다른 도구와 차별화 요소는 VSM (Value Stream Map) 기능으로 파이프라인 전반에 걸친 완전한 종단 간 보기
* YAML 및 JSON 형식으로 파이프라인을 구성함
* Jenkins보다는 플러그인이 많지 않다 
* <a href="https://www.gocd.org/">URL</a>

<br>

# CodeShip
* GitHub 프로젝트에서 직접 테스트, 빌드 및 배포를 지원함
* YAML을 이용하여 파이프라인을 구현함
* SSH를 사용하여 CI 환경 자체에서 빌드를 디버그할 수 있음

<br>

# GitLab CI/CD
* GitLab에 내장된 도구 
* Repository 루트 디렉터리에 있어야 하는 .gitlab-ci.yml YAML 파일에서 스크립트를 지정해야 함.
* API 제공 

<br>

# Jenkins X
* 주로 Docker 및 Kubernetes에서 클라우드 네이티브 애플리케이션을 위한 자동화된 CI/CD를 제공하는 오픈 소스 프로젝트
* 파이프라인을 사용한 자동화를 지원
* 내장된 GitOps 방법론은 환경을 관리함

<br>

# Codefresh
* Kubernetes에 구축된 CI/CD 플랫폼으로 완전한 GitOps 지원이 제공됨 
* Codefresh에서 제공하는 Kubernetes 대시보드를 사용하면 배포, 실행 등을 위해 모든 클러스터를 쉽게 모니터링이 가능함
* 각 단계는 컨테이너로 종속성에 대해 걱정할 필요 없이 파이프라인에서 동일한 단계를 구축할 수 있음
* GitHub, Bitbucket, GitLab, Slack뿐만 아니라 Terraform, Pulumi, Puppet 등과 통합될 수 있음

<br>