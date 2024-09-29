---
title:  "[Airflow] Release Notes - 2.8.x"
date:   2024-09-29 00:00:00 +0900
categories:
  - Data
  - Airflow
tags:
  - Data
  - Airflow
toc: true
toc_sticky: true
toc_label: "Release Notes"
header-img: https://user-images.githubusercontent.com/78892113/209359696-bdb63ef2-8e14-41f2-8e04-2c29049aeabf.png
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
2023년 12월 18일에 적용된 Airflow 2.8 버전에서의 변경사항은 무엇이 있었는지 공식 홈페이지를 통해 알아봅니다. 메이저한 변경사항에 대해만 알아보고 버그 수정 부분은 생략합니다. 자세한 것은 공식 홈페이지를 통해 확인합니다.  
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/209359696-bdb63ef2-8e14-41f2-8e04-2c29049aeabf.png){: .align-center}{: width="70%" height="70%"} 

<br><br>

* 참고 URL : <a href="https://airflow.apache.org/docs/apache-airflow/stable/release_notes.html#airflow-2-8-0-2023-12-18">공식 홈페이지</a>

<br>

## 메인 변경사항
### 1. DAG docs와 params에 있는 HTML 코드는 기본으로 이용 불가합니다. (2.8.0)
* `webserver.allow_raw_html_descriptions` 속성의 디폴트 값이 False인데 DAG 작성자의 코드를 신뢰하고 descriptions과 params을 사용하고자 하면 이 값을 `True`로 지정해야 합니다.

<br>

### 2. 핵심 디펜던시인 "pemdulum" 패키지가 3 버전으로 변경되었습니다. (2.8.1)
* Pendulum 3은 예를 들어 날짜를 렌더링하는데 있어 "T" 문자가 없어졌습니다.

<br>

### 3. Airflow 패키지 사양이 최신 Python 패키지 기준을 따라갑니다. (2.8.1)
* `pyproject.toml`을 이용하여 파이썬 패키지의 최신 개발을 따라가도록 설정했습니다.
  * [PEP-440](https://peps.python.org/pep-0440/), [PEP-517](https://peps.python.org/pep-0517/), [PEP-518](https://peps.python.org/pep-0518/), [PEP-561](https://peps.python.org/pep-0561/), [PEP-621](https://peps.python.org/pep-0621/), [PEP-660](https://peps.python.org/pep-0660/), [PEP-685](https://peps.python.org/pep-0685/)

<br>

### 4. Graphviz 종속성은 필수가 아닌 옵션입니다. (2.8.1)
* `graphviz`는 Airflow가 필요한 종속성, 특히 ARM 기반의 설치에 필요합니다.

<br>

### 5. AirflowTimeoutError는 Exception을 통한 경우를 제외하고 더 이상 발생하지 않습니다. (2.8.2)
* AirflowTimeoutError는 AirflowException 대신 `BaseException`을 상속합니다.

<br><br>
