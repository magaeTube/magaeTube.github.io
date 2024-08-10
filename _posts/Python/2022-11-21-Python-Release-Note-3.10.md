---
title:  "[Python] Release Notes - 3.10"
date:   2022-11-21 00:00:00 +0900
categories:
  - Python
tags:
  - Python

toc: true
toc_sticky: true
toc_label: "Release Notes"
header-mask: true
---



&nbsp;안녕하세요 마개입니다.  
Python 3.10 버전에서의 변경사항에 대해 알아봅니다.
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/210123814-45c26950-c3d7-40a9-a966-ca0be1dc1596.png){: .align-center}{: width="80%" height="80%"} 

<br><br>

## 메이저 변경사항

### 새로운 구문

* **<a href="https://peps.python.org/pep-0634/">PEP 634</a>** (Structual Pattern Matching: Specification) : 파이썬 버전 Switch Case로 파이썬에서는 `match-case` 구문임.
* **<a href="https://peps.python.org/pep-0635/">PEP 635</a>** (Structual Pattern Matching: Motivation and Rationale) : `match-case` 구문의 동기와 이론
* **<a href="https://peps.python.org/pep-0636/">PEP 636</a>** (Structual Pattern Matching: Tutorial) : `match-case` 구문 튜토리얼
* **<a href="https://bugs.python.org/issue12782">BPO-12782</a>** (Parenthesized context managers are now officially allowed) : `with` 구문이 여러 줄의 괄호 구문을 지원

<br>

### 기본 라이브러리의 새로운 특징

* **<a href="https://peps.python.org/pep-0618/">PEP 618</a>** (Add Optional Length-Checking To zip) : `zip`함수 구문을 이용할 경우 데이터의 길이를 체크할 수 있음. `strict`를 True로 설정할 경우 이터러블 중 하나가 먼저 소진되면 에러 발생.

<br>

### Interpreter 개선

* **<a href="https://peps.python.org/pep-0626/">PEP 626</a>** (Precise line numbers for debugging and other tools) : 디버깅과 다른 툴들을 위한 정확한 line 번호로 trace를 실행했을 때 실행되는 코드의 모든 라인이나 실행되는 코드 라인을 보여주고 에러발생시 정확한 위치를 제공함.

<br>

### 새로운 typing 특징

* **<a href="https://peps.python.org/pep-0604/">PEP 604</a>** (Allow writing union types as `X | Y`) : `Union[X, Y]` 형태를 `|` 연산자를 이용해 `X | Y` 로 사용가능함. `isinstance` 및 `issubclass` 호출에 나올 수 있음.
* **<a href="https://peps.python.org/pep-0612/">PEP 612</a>** (Parameter Specification Variables) : `typing.ParamSpec` 과 `typing.Concatenate` 를 사용하면 호출 가능한 객체에 추상적인 타입 정의 정보를 주석달 수 있음.
* **<a href="https://peps.python.org/pep-0613/">PEP 613</a>** (Explicit Type Aliases) : 변수에 type을 이용해 선언할 수 있음. 잘못된 타입이 있을 경우 구별이 가능함.

<br>

### 중요한 지원 중단, 삭제, 제한 사항들

* **<a href="https://peps.python.org/pep-0623/">PEP 623</a>** (Deprecate and prepare for the removal of the wstr member in PyUnicodeObject)
* **<a href="https://peps.python.org/pep-0632/">PEP 632</a>** (Deprecate distutils module) : distutils 패키지 대신 Setuptools 패키지를 사용할 것.
* **<a href="https://peps.python.org/pep-0644/">PEP 644</a>** (Require OpenSSL 1.1. or newer) : OpenSSL 의 1.1.1 이상 버전을 지원
* **<a href="https://peps.python.org/pep-0624/">PEP 624</a>** (Remove Py_UNICODE encoder APIs) 
* **<a href="https://peps.python.org/pep-0597/">PEP 597</a>** (Add optional EncodingWarning)
