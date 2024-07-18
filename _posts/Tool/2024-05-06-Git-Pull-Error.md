---
title:  "[Git] Pull할 때의 에러"
date:   2024-05-06 00:00:00 +0900
categories:
  - Tool
tags:
  - Tool
  - Git
header-img: https://github.com/magaeTube/magaeTube.github.io/assets/78892113/f42a15cb-4401-4810-88e6-d777ee29ee52
toc: true
toc_sticky: true
toc_label: "GIT Error"
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
&nbsp;Git을 이용하여 버전관리를 하고 있는데 Pull을 할 때의 발생하는 에러들을 모아놓습니다.
{: .notice--info}

<br>

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/f42a15cb-4401-4810-88e6-d777ee29ee52){: .align-center}{: width="80%" height="80%"} 

<br>

# fatal: Need to specify how to reconcile divergent branches.

![image](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/b6ca80e8-67e7-4314-9c82-0eab9050f925)
&nbsp;Pull을 통해 Push된 내용을 내려받고자 할 때 위와 같은 에러가 발생하여 진행되지 않았습니다. 원인에 대해 생각을 해보니 저는 두 개의 PC에서 같은 브랜치로 해당 레파지토리를 이용중인데 A라는 PC에서 Commit & Push하고 B라는 PC에서 Pull할 때 해당 문제가 발생했고 확인을 해보니 B에서 전에 Commit하고 Push하지 않은 이력이 있어서 문제가 발생한 것이었습니다. 원인은 파악이 되었고 해결 방법에 대해 위 이미지에서 보듯이 3가지 방법을 제안하고 있습니다. 

<br>

## Pull 전략
* git config pull.rebase false : rebase를 하지 않고 pull 진행. 즉, merge와 동일
* git config pull.rebase true : rebase를 한 후에 pull 진행
* git config pull.ff only : fast-forward일 때만 pull 진행

&nbsp;각자의 상황이 다르기 때문에 나의 상황에 맞춰 선택하여 진행하도록 합니다.