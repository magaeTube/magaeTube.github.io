---
title:  "[ChatGPT] OpenAI API Python 테스트"
date:   2024-11-06 00:00:00 +0900
categories:
  - AI
  - OpenAI
tags:
  - AI
  - OpenAI
  - API
toc: true
toc_sticky: true
toc_label: "Python 테스트"
header-img: https://github.com/user-attachments/assets/a6c27881-2240-4e24-96f5-a04ec1ff1700
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
&nbsp;준비가 된 OpenAI API를 이용해서 파이썬으로 테스트해봅시다.   
{: .notice--info}

<br>

![openai](https://github.com/user-attachments/assets/a6c27881-2240-4e24-96f5-a04ec1ff1700){: .align-center}{: width="70%" height="70%"} 

<br><br>

## Python Package 설치

```sh
$ pip install openai
```
&nbsp;OpenAI에서는 Python을 위한 패키지 `openai`를 제공합니다. 해당 패키지를 설치해서 API를 이용합니다.

<br><br>

## Python 테스트

### Generate Text

```python
from openai import OpenAI
client = OpenAI()

completion = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {
            "role": "user",
            "content": "Write a haiku about recursion in programming."
        }
    ]
)

print(completion.choices[0].message)
```

&nbsp;OpenAI에서 제공하는 기본 코드로 테스트해봅니다. 위의 코드를 실행합니다.

<br>

![image](https://github.com/user-attachments/assets/0cd826ee-3be4-46b0-ba3c-a05e303a18db)
&nbsp;저와 같이 위의 에러가 발생한다면 API_KEY가 제대로 세팅되지 않은 것입니다. 이전에서 봤던 `export OPENAI_API_KEY=[KEY값]`로 세팅을 하던지 아니면 코드에서 `OpenAI()`에 파라미터로 `api_key`를 추가합니다. 각자 편한 방법으로 진행합니다.

<br>

```
ChatCompletionMessage(content="Functions calling back,  \nLayers deep in logic's dance,  \nEndless loops of thought.", refusal=None, role='assistant', audio=None, function_call=None, tool_calls=None)
```

&nbsp;실행이 완료되면 위와 같은 모습을 볼 수 있습니다.

<br><br>

### Generate Image

```python
from openai import OpenAI
client = OpenAI()

response = client.images.generate(
    prompt="A cute baby sea otter",
    n=2,
    size="1024x1024"
)

print(response.data[0].url)
```

&nbsp;이미지를 생성하는 것은 `DALL.E`모델을 이용하게 됩니다. 마찬가지로 API KEY는 본인의 환경에 맞게 세팅하고 실행합니다.

<br>

![sea otter](https://github.com/user-attachments/assets/60baba6b-eadc-46ef-8191-a16d9c6561f2)
&nbsp;그러면 결과로 URL이 나오게 되고 들어가보면 저와 같이 수달의 모습이 나오게 됩니다. 정확한 이미지는 저와 다릅니다.