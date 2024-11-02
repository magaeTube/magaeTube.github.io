---
title:  "[ChatGPT] OpenAI API 비용 충전하기"
date:   2024-11-02 00:00:00 +0900
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
&nbsp;ChatGPT에서 제공해주는 API를 사용하기 위해 비용을 충전해봅니다.   
{: .notice--info}

<br>

![openai](https://github.com/user-attachments/assets/a6c27881-2240-4e24-96f5-a04ec1ff1700){: .align-center}{: width="70%" height="70%"} 

<br><br>

## OpenAI Platform 이동

![Platform](https://github.com/user-attachments/assets/7a9c1531-20b7-4f1b-8981-391b39d80668)
&nbsp;OpenAI API를 이용하기 위해서는 이용할 금액이 있어야 합니다. 이를 충전하기 위해서 <a href="https://platform.openai.com/docs/overview">OpenAI Platform</a>으로 이동합니다. 페이지에 접속했다면 우측 상단에 있는 `Log In` 버튼을 클릭하여 로그인합니다.

<br>

![Settings](https://github.com/user-attachments/assets/1ddff7d0-b804-4d9e-8e13-b61f2363daaa)
&nbsp;로그인을 했다면 우측 상단에 톱니바퀴(Settings)를 클릭합니다.

<br>

![Billing](https://github.com/user-attachments/assets/a3e17a4a-b5a0-41e7-bd70-c2a9e7707a2e){: .align-center}{: width="70%" height="70%"}
&nbsp;Settings 메뉴로 넘어오면 왼쪽 메뉴에 다음과 같이 있는데 여기에서 `ORGANIZATION` - `Billing` 메뉴를 선택합니다.

<br>

![Billing](https://github.com/user-attachments/assets/99633de1-e8ce-42ce-bbfc-1f3eefd408bc)
&nbsp;"Billing"을 선택하면 위와 같은 화면이 뜨는데 저는 아직 등록해놓은 것이 없기 때문에 `Add payment details` 버튼을 선택해서 지불 방식을 등록합니다.

<br>

![Add payment details](https://github.com/user-attachments/assets/0a47c9ec-c65d-4391-b24c-0df8302d07ec){: .align-center}{: width="70%" height="70%"}
&nbsp;여기에서는 각자에 맞는 목적에 따라 정합니다. 저는 `Individual`을 선택했습니다.

<br>

![Payment Details](https://github.com/user-attachments/assets/36152268-b175-4dab-a72a-2c321d4643fd){: .align-center}{: width="70%" height="70%"}
&nbsp;이제 여기에는 본인의 카드 정보를 등록합니다. 무조건 신용카드로 등록해야 합니다.

<br>

![Charge](https://github.com/user-attachments/assets/435f39f2-6bc2-4c71-9457-90f40fd6779f){: .align-center}{: width="70%" height="70%"}
&nbsp;카드 정보를 등록하면 위와 같이 충전에 대한 내용이 나옵니다. 처음 충전하는 금액(`Initial credit purchase`)에 대해서는 5~100 달러로 원하는 만큼 설정합니다. 전 10달러로 진행해보겠습니다.
&nbsp;그 아래에는 `Would you like to set up automatic recharge?` 라고 질문을 하는데 남은 금액이 내가 설정해놓은 금액 이하로 떨어지면 자동으로  충전할지에 대한 내용입니다. 실제 사업화해서 사용하는 단계가 아니라면 해제하는 것이 좋을듯합니다. 안그러면 본인도 모르게 API를 사용하면서 돈이 계속 나갈 수도 있습니다. 저는 이것을 해제하겠습니다. 이후에 `Continue` 버튼을 클릭합니다.  

&nbsp;그러면 세금이 붙는 총 금액이 나오는데 `Confirm payment` 버튼을 클릭하여 충전합니다.

<br><br>

## 확인

![Payment](https://github.com/user-attachments/assets/90135de3-a68c-4b5a-9c43-2f6641cf5780){: .align-center}{: width="70%" height="70%"}
&nbsp;충전이 되었으면 위와 같이 금액이 충전되어있는 것을 보실 수 있습니다. 이제 이전에 진행해서 얻었던 API Key를 이용해서 확인해봅니다.

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

![API](https://github.com/user-attachments/assets/e1698b5f-0473-4348-be9c-1bc7cc1932ae)
&nbsp;그러면 이전처럼 error 메시지는 나오지 않고 다른 결과가 나오면서 성공한 것을 확인하실 수 있습니다.
