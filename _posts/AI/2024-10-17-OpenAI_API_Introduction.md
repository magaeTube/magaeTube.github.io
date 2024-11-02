---
title:  "[ChatGPT] OpenAI API 이용해보기"
date:   2024-10-17 00:00:00 +0900
categories:
  - AI
  - OpenAI
tags:
  - AI
  - OpenAI
  - API
toc: true
toc_sticky: true
toc_label: "OpenAI API"
header-img: https://github.com/user-attachments/assets/a6c27881-2240-4e24-96f5-a04ec1ff1700
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
&nbsp;개발을 하다가 모르는 것이 생길 때 매번 OpenAI의 ChatGPT 도움을 많이 받았었습니다. 하지만 IT 개발자로서(데이터 엔지니어지만..) 제대로 사용해보면 좋을 것 같아 API에 대해 알아보게 되었습니다. 해당 내용에 대해 기록을 남깁니다.   
{: .notice--info}

<br>

![openai](https://github.com/user-attachments/assets/a6c27881-2240-4e24-96f5-a04ec1ff1700){: .align-center}{: width="70%" height="70%"} 

<br><br>

## OpenAI API는?
&nbsp;ChatGPT라고 하면 모를 사람이 없을 정도로 유명한데 그 ChatGPT를 개발한 회사가 `OpenAI`입니다. OpenAI에는 여러 가지 AI 인공지능 모델이 있는데 `ChatGPT`는 그 중에 하나입니다.  
&nbsp;OpenAI는 ChatGPT말고도 `DALL.E`나 `Whisper`등 다양한 모델을 가지고 있습니다. 이것을 ChatGPT에서 바로 사용할 수 있지만 다른 애플리케이션과 연동하기 위해서는 API를 사용해야 합니다. OpenAI는 이미 이러한 API를 제공하고 있기 때문에 그저 사용하기만 하면 됩니다.

<br>

## 제공 모델
&nbsp;OpenAI는 API로 여러 모델을 제공하고 있는데 어떠한 모델들이 있는지 알아봅니다.

<br>

### GPT
&nbsp;가장 인기가 많은 GPT 모델입니다. `LLM (Large-Language Model)` 중 하나로 현재의 OpenAI를 있게 해준 것이라고 볼 수 있습니다. GPT 모델에는 지속적으로 신규 버전이 나오고 있는데 API로 제공하는 가장 최근 버전은 `GPT-4o`입니다. 그 이외에도 4o mini, 3.5 Turbo 등을 이용할 수 있습니다.

### DALL.E
&nbsp;`DALL.E`모델은 자연어 프롬프트를 받으면 이미지를 생성하거나 편집할 수 있는 모델입니다. AI를 이용해서 이미지를 생성하고 싶을 때 사용 가능한 모델입니다.

### TTS
&nbsp;`TTS`는 텍스트를 사운드로 변환시켜주는 모델이라고 보면 됩니다. TTS라는 용어 자체가 Text-To-Speech로 텍스트를 오디오 사운드로 변환시켜준다고 보면 됩니다.

### Whisper
&nbsp;`Whisper`는 TTS의 반대라고 보면 됩니다. 오디오 사운드를 텍스트로 변환시켜주는 모델입니다. 공용으로 사용하는 용어는 TTS의 반대인 STT로 Speech-To-Text입니다.

### Embeddings
&nbsp;`Embeddings`는 텍스트를 숫자형태로 변환시켜주는 모델이라고 합니다. 이건 정확히 어떤 개념인지는 잘 모르는데 어떻게 사용하는지도 궁금합니다. 추후에 기회가 되면 사용해보도록 하겠습니다.

### Moderation
&nbsp;`Moderation`은 텍스트가 민감한 정보이거나 안전하지 않은지 발견하는 모델입니다. 민감한 정보는 매우 중요한 문제이기 때문에 이를 발견해주는 모델인 것 같습니다.

<br>

## 가격
&nbsp;위에서 여러 모델들을 알아봤는데 API를 이용하려면 비용을 지불해야 합니다. 비용은 각 모델마다 다르고 모델 안에서 버전에 따라서도 다릅니다. `GPT`의 경우 input으로 들어오는 양에 따라 다르고 `DALL.E`의 경우 이미지 개수당 요금을 부여합니다. 상세한 내용은 <a href="https://openai.com/api/pricing">링크</a>에서 확인하실 수 있습니다.

<br>

## API 시작하기
### API Key 생성
&nbsp;API를 이용하기 위해서는 API Key가 필요합니다. 이번에는 이 API key를 발급받겠습니다.  

* <a href="https://platform.openai.com/api-keys">https://platform.openai.com/api-keys</a>

&nbsp;위 링크로 이동합니다.  

<br>

![api](https://github.com/user-attachments/assets/9fd16268-5716-4c64-8ae5-054eea317300)

&nbsp;위 페이지로 이동하면 다음과 같은데 로그인하도록 합니다. OpenAI 계정이 없다면 `Sign up`을 선택해서 계정을 생성합니다.  

<br>

![key](https://github.com/user-attachments/assets/474dfe6e-8d8b-46b8-86ce-59d78e95bd2f)

&nbsp;로그인이 되었다면 위와 같은 화면이 나오게 됩니다. 여기에서 `Create new secret key`를 선택합니다.

<br>

![key](https://github.com/user-attachments/assets/585e5f6d-f464-416c-b092-dee0c972b4b2)

&nbsp;그러면 위와 같은 팝업이 뜨는데 다음과 같이 각 정보에 해당하는 내용을 작성합니다.
* Name : 만들고자 하는 Key의 이름. 필수는 아니지만 원하는 이름을 작성합니다.
* Project : 해당 Key를 사용할 프로젝트를 선택합니다. 여기서는 테스트이기 때문에 `Default project` 그대로 놔두겠습니다.
* Permissions : 해당 Key에 대한 권한인데 테스트이기에 `All`로 설정합니다.
&nbsp;목적에 알맞게 작성했다면 `Create secret key`를 선택합니다. 그러면 생성된 key가 공개되는데 이를 `Copy`하고 잊지 않도록 어디에 잘 보관합니다. 이는 다른 사람들한테 공유하면 안됩니다. 이 Key는 이후에 개발하는데 필요한 정보입니다. 이제 해당 Key를 이용해서 잘 적용이 되었는지 확인해봅니다.

<br>

```sh
$ export OPENAI_API_KEY=[API KEY]
$ curl "https://api.openai.com/v1/chat/completions" \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $OPENAI_API_KEY" \
    -d '{
        "model": "gpt-4o-mini",
        "messages": [
            {
                "role": "system",
                "content": "You are a helpful assistant."
            },
            {
                "role": "user",
                "content": "Write a haiku that explains the concept of recursion."
            }
        ]
    }'
```

&nbsp;위와 같이 실행해봅니다.

<br>

![image](https://github.com/user-attachments/assets/89bb218a-0715-44d8-b453-df860c983240)

&nbsp;혹시 저와 같은 에러가 발생하는 분이 있다면 본인 계정이 비용이 있는지 확인하도록 합니다. 저는 아직 충전하지 않았기 때문에 비용이 없어서 발생한 문제입니다. 해당 문제의 경우 비용을 충전해놓고 이용하면 됩니다.

* 비용 충전 방법 : <a href="https://magaetube.github.io/ai/openai/OpenAI_API_Pricing/">https://magaetube.github.io/ai/openai/OpenAI_API_Pricing/</a>

<br>

![API](https://github.com/user-attachments/assets/e1698b5f-0473-4348-be9c-1bc7cc1932ae)
&nbsp;충전한 이후에 다시 진행해보면 다음과 같이 잘 된 것을 확인하실 수 있습니다.

<br><br>

## 공식 홈페이지
* <a href="https://platform.openai.com/docs/overview">https://platform.openai.com/docs/overview</a>
