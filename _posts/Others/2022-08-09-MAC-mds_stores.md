---
title:  "[MAC] mds_stores CPU 점유율 문제"
date:   2022-08-09 00:00:00 +0900
categories:
  - Others
header-mask: true
---

&nbsp;안녕하세요 마개입니다.  
2017년형 맥북을 사용 중인데 자주 CPU 점유율이 높아서 문제점을 찾은 내용을 공유합니다.
{: .notice--info}

<br><br>

2017년 맥북을 사용 중인데 프로그램을 많이 사용하지 않은 상태에서도 자주 CPU 점유율이 높아서 해당 문제에 대해 찾아보았습니다.  
맥북의 `[활성 상태 보기]`를 보니 **"mds_stores"**가 많은 양을 차지하고 있었습니다.  

이 **mds_stores**는 spotlight와 관련된 것인데 사용하고 있지 않기 때문에 이를 검색 인덱싱을 비활성화하였더니 점유율 문제가 해결이 되었습니다.

```sh
# 비활성화하기
sudo mdutil -a -i off

# 활성화하기
sudo mdutil -a -i on
```