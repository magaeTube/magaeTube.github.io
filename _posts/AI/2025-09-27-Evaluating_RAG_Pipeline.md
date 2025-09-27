---
title:  "[RAG 도전기] RAG 파이프라인 평가 방법"
date:   2025-09-27 00:00:00 +0900
categories:
  - AI
  - RAG
tags:
  - AI
  - RAG
  - RAGAS
toc: true
toc_sticky: true
toc_label: "RAG 파이프라인 평가"
header-img: 
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
&nbsp;RAG 파이프라인을 개발하다보면 부딪치는 문제 중 하나가 물어본 질문에 정확한 답을 잘 가져오는지 질문의 의도를 잘 파악하고 답을 하는지에 대한 것입니다. 이렇게 만들어놓은 RAG 파이프라인을 평가하는 방법에 대해서 공부합니다.        
{: .notice--info}

<br>

![RAG](/assets/images/ai/RAG.png){: .align-center}{: width="70%" height="70%"} 

<br><br>

# 평가 종류
&nbsp;RAG 파이프라인에서 데이터들을 벡터DB에 넣었다면 이후에 질문이 들어오면 임베딩하여 벡터DB에서 검색하고 나온 Document들로 답변을 생성하는 과정을 거칩니다. 이 과정 중에서 두 개의 평가 종류가 있습니다.
* `검색` (Retrieval) 평가 : 질문에 답하기 위해서 벡터DB로부터 올바른 Document를 찾아냈는가?
* `생성` (Generation) 평가 : 검색을 통해 찾아낸 Document를 바탕으로 알맞은 답변을 생성하는가? 찾아낸 Document 외에 Hallucination은 없이 답변을 생성하는가?

<br>

&nbsp;RAG 파이프라인을 제대로 평가하기 위해서는 두 가지 모두 진행을 해야 좋은 성능의 파이프라인을 구성할 수 있습니다. 

<br><br>

# 평가 주요 지표
&nbsp;두 가지 평가에 대해서 결과를 받기 위해 필요한 지표들에 대해 알아봅니다. 

## 검색 (Retrieval) 평가
&nbsp;들어온 질문을 임베딩하고 이를 벡터DB를 통해 Document를 가져오는데 제대로 된 Document를 가져와서 LLM에 전달해야 LLM도 제대로 된 답변을 생성하겠죠. 이때 필요한 지표들은 다음과 같습니다.

<br>

* `Context Precision` (컨텍스트 정확도) : 검색된 Document 중에서 실제로 질문과 관련된 문서의 비율은 얼마나 되는가? 즉, 정확도를 보는 지표입니다.
* `Context Recall` (컨텍스트 재현율) : 전체 Document에서 실제로 찾아낸 Document의 비율은 얼마나 되는가? 즉, 빠짐없이 문서를 다 가져왔는지를 봅니다.
* `Hit Rate` : 검색된 문서 중에 정답을 포함하는 문서가 하나라도 있는가?
* `MRR` (Mean Reciprocal Rank) : 가장 관련있는 문서가 검색 결과 목록 중에서 몇 번째에 나타났는가? 순위가 높을수록 잘 찾아낸 것이라고 볼 수 있습니다.

<br><br>

## 생성 (Generation) 평가
&nbsp;Retrieval을 통해 가져온 Document들을 LLM에게 전달했다면 LLM은 얼마나 답변을 잘 생성했는지에 대한 평가입닌다. 

<br>

* `Faithfulness` (충실성) : 생성된 답변이 검색된 Document에만 기반하고 있는가? Document 외의 Hallucination이 있는지에 대한 지표로 중요한 지표입니다.
* `Answer Correctness` (답변 정확성) : 생성된 답변이 실제 정답과 비교했을 때 얼마나 정확한가? 이를 비교하기 위해서는 실제 정답 (Ground Truth)이 필요합니다.
* `Answer Relevancy` (답변 관련성) : 생성된 답변이 사용자의 질문과 얼마나 관련이 있는가? 엉뚱한 답변을 하지는 않았는지를 보는 지표입니다.

