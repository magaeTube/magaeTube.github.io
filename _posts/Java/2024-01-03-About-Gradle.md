---
title:  "[Java] Gradle이란"
date:   2024-01-03 00:00:00 +0900
categories:
  - Java
tags:
  - Java
  - Gradle
toc: true
toc_sticky: true
toc_label: "Gradle"
header-mask: true
---

&nbsp;안녕하세요 마개입니다.  
Build Tool로 많이 사용하는 Gradle에 대해 알아봅니다.
{: .notice--info}

<br><br>

![Gradle_logo](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/fdc4d8cb-bd62-484c-9835-6280edb7a8a3)


# Gradle이란

Gradle은 Build Tool 중에 하나로 Compile과 Test 등 여러 작업을 자동화하는 기능을 제공하며 여러 특징을 가지고 있습니다.
* JVM의 가장 유명한 빌드시스템이고 Android와 Kotlin의 기본 시스템임.
* 빠르고 확장 가능하고 여러 사이즈와 복잡한 프로젝트까지 빌드할 수 있음.
* 각 작업에 필요한 라이브러리들만 가져올 수 있도록 설정이 가능함.

<br>

# 지원가능한 프로그래밍 언어와 프레임워크

Gradle은 Android, Java, Kotlin, Groovy, Scala, Javascript, C/C++을 지원하고 IDE툴인 Android Studio, IntelliJ, Visual Studio Code, Eclipse, NetBeans와 호환이 됩니다.

<br>

# 핵심 개념

## Gradle Basics
Gradle은 빌드 스크립트를 통해 빌드, 테스트, 배포를 자동화합니다. Gradle의 아키텍처는 다음과 같습니다.

![gradle architecture](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/b91405c7-a4a8-4796-8837-71e321e2b38c){: .align-center}

### Core Concept
* **Projects** : Gradle 프로젝트는 애플리케이션이나 라이브러리와 같은 빌드할 수 있는 소프트웨어의 일부분으로 하나의 프로젝트에는 `root project` 라고 불리는 프로젝트가 빌드되고 다수의 프로젝트의 경우 하나의 `root project`와 여러 개의 `subprojects` 로 구성됩니다.
* **Build Scripts** : 프로젝트를 빌드하기 위해 필요한 단계를 지정할 수 있습니다. 각 프로젝트는 하나 이상의 Scripts를 가질 수 있습니다.
* **Dependency Management** : 외부 리소스를 선언하고 사용하기 위한 자동화된 기술입니다.
* **Tasks** : Task는 코드를 컴파일하거나 테스트를 실행하는 등의 기본적인 작업 단위를 말합니다.
* **Plugins** : Plugin은 Gradle의 기능을 확장하고 프로젝트에 작업을 기여하는데 사용됩니다.

<br>

### Gradle Project 구조
Gradle을 이용하면 프로젝트의 루트 디렉터리에 `gradlew`, `gradle.bat`이 존재하여 Gradle을 사용중이라는 것을 알 수 있습니다. 이외에 프로젝트 구조는 다음과 같습니다.

```shell
project
├── gradle                              
│   ├── libs.versions.toml              
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew                             
├── gradlew.bat                         
├── settings.gradle(.kts)               
├── subproject-a
│   ├── build.gradle(.kts)              
│   └── src                             
└── subproject-b
    ├── build.gradle(.kts)              
    └── src                             
```
* **gradle** : wrapper 파일들과 여러 파일들이 저장되는 디렉터리
* **libs.versions.toml** : dependency 관리를 위한 Gradle 버전 카탈로그
* **gradlew/gradlew.bat** : Gradle wrapper 스크립트
* **settings.gradle(.kts)** : root project와 subprojects를 지정
* **build.gradle(.kts)** : 두 개의 subproject들에서의 build script
* **src** : 소스 코드와 프로젝트를 위한 각종 파일들

<br><br>

## Gradle Wrapper Basics
Gradle을 빌드하는데 추천하는 방식은 Wrapper를 사용하는 것입니다.

![Gradle Wrapper](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/08fe0684-df1e-45b2-911d-bd950f3bc49d){: .align_center}

Wrapper는 `gradlew`나 `gradle.bat` 파일을 통해 사용가능하고 프로젝트를 표준화하고 각기 다른 실행 환경에서도 동일하게 실행할 수 있게 합니다.

```shell
# 일반적인 Gradle 빌드
$ gradle build

# Wrapper 실행 (Linux or OSX)
$ ./gradlew build

# Wrapper 실행 (Windows)
$ .\gradlew.bat build
```

<br><br>

## CLI Basics

![Gradle CLI](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/08fe0684-df1e-45b2-911d-bd950f3bc49d){: .align_center}

IDE 환경에서 Gradle을 실행하기도 하지만 CLI를 이용하고자할 때는 다음과 같이 이용할 수 있습니다.

```shell
# 기본 실행 방법. task와 option은 순서가 바뀌어도 됩니다.
$ gradle [taskName...] [--option-name...]

# 여러 task를 수행하고자할 때
$ gradle [taskName1, taskName2]

# gradle 기능들을 확인하고자 할 때
$ gradle --help
$ gradle -h
```

<br><br>

## Settings File Basics

![Settings File](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/fd22d8a3-4474-40d0-9f24-efadfcffcc91){: .align_center}

설정 파일들은 Gradle 프로젝트의 시작점입니다. 하나의 프로젝트에서는 설정 파일이 옵션이지만 다수의 프로젝트에서는 필수이고 모든 서브 프로젝트들을 선언해야 합니다.

<br>

### Settings Script

설정 파일은 스크립트입니다. Groovy로 작성할 때에는 `settings.gradle`이라는 파일 이름으로 작성하고 Kotlin으로 작성할 때는 `settings.gradle.kts` 라는 파일 이름으로 작성합니다. Gradle 스크립트에서는 Groovy와 Kotlin만 사용 가능합니다. 설정 파일은 일반적으로 프로젝트의 root 디렉터리에서 찾을 수 있습니다.

```groovy
rootProject.name = 'root-project'

include('sub-project-a')
include('sub-project-b')
include('sub-project-c')
```

Groovy로 작성한 설정 파일의 모습입니다. 여기서 `rootProject.name`을 통해 프로젝트 명을 지정하고 `include`를 이용하여 모든 서브 프로젝트들을 포함하면 됩니다.

<br><br>

## Build File Basics

![Build File](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/e0a25e83-ed61-4772-9ac6-6c3a06c739f5){: .align_center}

Build 파일에서는 속성, task, plugin에 대한 내용을 작성하고 Gradle이 사용하는 라이브러리 또는 플러그인을 추가하고 소스 코드에서 사용하는 라이브러리들을 추가할 수도 있습니다.

### Build scripts

Build 스크립트는 Groovy로 작성할 때는 `build.gradle` 이라는 이름으로, Kotlin으로 작성할 때는 `build.gradle.kts` 라는 이름으로 작성합니다.

```groovy
plugins {
    id 'application'
}

application {
    mainClass = 'com.example.Main'
}
```

프로젝트에 넣고자 하는 플러그인을 `plugins` 안에 지정합니다. `application` 플러그인은 실행 가능한 JVM 애플리케이션을 생성해주는 기능입니다. `plugins` 안에 플러그인을 정하고 그 하단에 플러그인의 속성을 지정할 수 있습니다. 필요한 속성들을 지정해서 사용할 수 있습니다.

<br><br>

## Dependency Management Basics

![Dependency Management](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/34c97618-7073-4137-bb35-ecee10053f4b){: .align_center}

Dependency 관리는 프로젝트에서 요구되는 외부 리소스를 선언하고 이용하는 기능입니다. 프로젝트에 dependency를 추가하기 위해서는 `build.gradle(.kts)` 파일에 추가해야 합니다. 

```groovy
plugins {
    alias(libs.plugins.android.application)
}

dependencies {
    implementation(libs.google.material)
    testImplementation(libs.mockito.core)
}
```

Andriod의 Gradle 플러그인을 `plugins`에 추가했고 `implementation`은 코드를 컴파일하고 수행할 dependency를 추가할 때 사용되고 `testImplementation`은 테스트 코드를 컴파일하고 수행할 때 사용됩니다.

```shell
$ ./gradlew :프로젝트명:dependencies
```

프로젝트에 존재하는 Dependency들을 확인하기 위해서는 위 명령어를 실행하면 됩니다.

<br><br>

## Task Basics

![Tasks](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/3d32a05e-baf1-4c1c-9d4f-cb7670434369){: .align_center}

Task는 클래스를 컴파일하거나 JAR를 생성하거나 등등 작업의 독립적인 단위입니다. `build` 명령어를 통해 정해진 tasks가 수행됩니다.

```shell
# 정해진 task로 gradle 수행
$ ./gradlew build

# 사용 가능한 task 확인
$ ./gradlew tasks
Application tasks
-----------------
run - Runs this project as a JVM application

Build tasks
-----------
assemble - Assembles the outputs of this project.
build - Assembles and tests this project.

...

Documentation tasks
-------------------
javadoc - Generates Javadoc API documentation for the main source code.

...

Other tasks
-----------
compileJava - Compiles main Java source.

...

# "run" task 수행 예시
$ ./gradlew run

> Task :app:compileJava
> Task :app:processResources NO-SOURCE
> Task :app:classes

> Task :app:run
Hello World!

BUILD SUCCESSFUL in 904ms
2 actionable tasks: 2 executed
```

<br><br>

## Plugins Basics

![Plugins](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/9720b45c-c657-4127-8e13-463247488f8e){: .align_center}

Plugin은 기능을 확장하고 Gradle을 커스터마이징하는데 사용합니다. 예를 들면 Spring Boot Plugin인 `org.springframework.boot`는 Spring Boot를 지원합니다. Gradle Shadow 플러그인인 `com.github.johnrengelman.shadow`는 기능을 다 포함하는 JARs를 만드는데 사용되는 플러그인입니다.  
플러그인은 3가지로 나뉩니다.
* Core plugins : Gradle이 개발하고 유지보수하는 플러그인
* Community plugins : Gradle 커뮤니티에서 공유하는 플러그인
* Local plugins : 유저가 직접 만들어서 사용하는 플러그인  

이러한 플러그인들은 추가하는 형태가 다릅니다.

```groovy
// Core Plugins
plugins {
    id("java")
}

// Community Plugins
plugins {
    id("org.springframework.boot") version "3.1.5"
}
```

플러그인을 확인하는 명령어도 있습니다.
```shell
$ ./gradlew tasks

> Task :tasks

------------------------------------------------------------
Tasks runnable from root project 'plugins'
------------------------------------------------------------

Jib tasks
---------
jib - Builds a container image to a registry.
jibBuildTar - Builds a container image to a tarball.
jibDockerBuild - Builds a container image to a Docker daemon.
```


<br>

# 설치

Gradle은 Linux, macOS, Windows등 주요 OS에서 설치가 가능하며 Gradle을 설치하기에 앞서 Java Development Kit (JDK) 8 버전 이상이 필요합니다. JDK의 버전은 아래와 같은 명령어로 확인하실 수 있습니다.

```shell
$ java -version
openjdk version "1.8.0_292"
OpenJDK Runtime Environment (AdoptOpenJDK)(build 1.8.0_292-b10)
OpenJDK 64-Bit Server VM (AdoptOpenJDK)(build 25.292-b10, mixed mode)
```
  
그러면 Gradle을 설치하도록 하겠습니다. `SDKMAN`, `Homebrew`, `MacPorts`와 같은 패키지 매니저를 이용하는 방법이 있고 직접 메뉴얼로 설치하는 방법이 있지만 저는 Homebrew를 이용해서 진행했습니다. 설치가 완료되면 버전을 확인해봅니다.

```shell
$ brew install gradle
$ gradle -v
```

![gradle version](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/9308f0b6-f52d-4242-ab95-385f9f481c09){: .align-center}{: width="70%" height="70%"} 

