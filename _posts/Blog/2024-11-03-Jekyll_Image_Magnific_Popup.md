---
title:  "[Jekyll] GithubIO 이미지 확대 기능 추가하기"
date:   2024-11-03 00:00:00 +0900
categories:
  - Blog
tags:
  - Jekyll
toc: true
toc_sticky: true
toc_label: "이미지 확대 기능"
header-img: https://github.com/magaeTube/magaeTube.github.io/assets/78892113/33121d67-2121-4304-8bcc-a75ef78d592d
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
&nbsp;GithubIO를 이용하여 블로그를 운영하고 있는데 보다보니 중요한 사실을 잊었습니다. 블로그의 모든 글들에는 이미지를 넣었지만 해당 이미지가 확대되지 않았다는 점입니다. 어떠한 이미지는 블로그에서 볼 때 작게 보이는데 이게 확대가 안되기 때문에 문제가 되는 것이었습니다. 이를 해결해봅니다.
{: .notice--info}

<br>

![Github](https://github.com/user-attachments/assets/adef3fc3-b582-451e-b53c-83fb00ed0f68){: .align-center}

<br><br>

## 현재 문제
&nbsp;다른 블로그에는 이미지를 클릭하면 팝업이 뜨면서 이미지가 확대되어 보이는데 제 블로그에 있는 이미지는 클릭을 할 수도 없고 확대가 되지 않아 가독성에 문제가 있었습니다. 그렇기에 조사를 해보니 `Magnific Popup`을 이용하면 된다는 것을 알았습니다.

<br><br>

## Magnific Popup
### 필요한 파일 확인
&nbsp;제 블로그는 Minimal Mistake를 이용중이기 때문에 이 기능을 이용하기로 했습니다. Minimal Mistake를 이용중이시라면 Magnific-Popup에 대한 파일을 이미 있습니다. 

<br>

![File](https://github.com/user-attachments/assets/9a6d0ad8-ed56-4eb6-ab88-be7e896209d2){: .align-center}{: width="70%" height="70%"}
&nbsp;`assets` - `js` - `plugins` 디렉토리 안에 `jquery.magnific-popup.js` 파일이 있는지 확인합니다.

<br>

&nbsp;파일이 있다면 `_config.yml` 파일에서 `exclude:` 구문을 찾습니다. 해당 구문은 블로그가 컴파일될 때 지정해놓은 부분을 제외하고 컴파일한다는 것입니다.

<br>

![config](https://github.com/user-attachments/assets/86c00c11-839a-4823-a6af-9bad850ed7d8){: .align-center}{: width="70%" height="70%"}
&nbsp;`exclude:` 구문을 찾았다면 다음 두 가지를 주석처리합니다.
* assets/js/plugins
* assets/js/vendor

<br>

&nbsp;만약에 위에서 봤던 해당 파일이 없다면 먼저 `Magnific-Popup` 페이지 (<a href="https://github.com/dimsemenov/Magnific-Popup">https://github.com/dimsemenov/Magnific-Popup</a>)로 이동해서 파일을 다운로드 받습니다.

<br>

![Magnific Popup](https://github.com/user-attachments/assets/8c2e4d66-7403-45ed-acdc-64222be8e2d4){: .align-center}{: width="70%" height="70%"}
&nbsp;소스코드 파일을 다운받아서 압축을 해제하면 다음과 같이 있는데 여기서 봐야할 것은 두 가지입니다.
* dist/jquery.magnific-popup.js
* libs/jquery/jquery.js

<br>

![jquery](https://github.com/user-attachments/assets/733590ff-41e9-4e01-8ff7-5a0953771756){: .align-center}{: width="70%" height="70%"}
&nbsp;두 개의 파일을 `assets/js` 디렉토리에 추가합니다. 저의 경우에는 기존에 Minimal Mistakes에서 제공한 파일의 버전이 너무 낮아서 새로 다운로드받아서 진행했습니다.

<br>

### magnific-popup-setting.js 생성
&nbsp;`assets/js` 디렉토리에 `magnific-popup-setting.js` 파일을 생성하고 다음 코드를 추가합니다.

<br>

```javascript
$(document).ready(function() {

    // 2.1. id가 magnific인 경우에만 magnific-popup 적용.
    //$('.page__content img[id="magnific"]').wrap( function(){
    $('.page__content img').wrap( function(){
		
        // 2.2. magnific-popup 옵션 설정.
        $(this).magnificPopup({
            type: 'image',
            closeOnContentClick: true,
            showCloseBtn: false,
            items: {
              src: $(this).attr('src')
            },
        });
				
        // 2.3. p 태그 높이를 내부 컨텐츠 높이에 자동으로 맞추기.
        // $(this).parent('p').css('overflow', 'auto');
				
        // 2.4. 이미지를 감쌀 태그 설정.
        // return '<a href="' + $(this).attr('src') + '" style="width:' + $(this).attr('width') +'px; float: left;"><figure> </figure>' + '<figcaption style="text-align: center;" class="caption">' + $(this).attr('alt') + '</figcaption>' + '</a>';
    });
});
```
여기에서 봐야할 부분은 다음과 같습니다.
* 전체 이미지에 적용하지 않을 것이라면 "2.1" 부분에서 첫번째 줄을 주석해제하고 두번째 줄을 주석처리합니다.
* "2.2" 옵션을 변경하고자 하면 해당 <a href="https://dimsemenov.com/plugins/magnific-popup/documentation.html#options">링크</a>에서 찾아서 적용하면 됩니다.
* "2.3" 옵션은 여러 이미지를 일렬로 배치할 때 남는 공간에 글자가 들어오는 것을 방지하는 작업인데 일렬로 배치할 필요가 없으면 저처럼 주석처리 합니다.
* "2.4" 옵션은 이미지에 태그를 나타내는 것인데 저는 필요하지 않기 때문에 주석처리했고 이 기능을 쓰고 싶으면 주석 해제해서 이용하면 됩니다. 추가로 여러 이미지를 일렬 배치하지 않는다면 `float: left;` 구문은 삭제합니다.

<br><br>

## default.html 파일에 해당 js 파일들 추가
&nbsp;위에서 작성한 js를 적용하기 위해서 `_layouts` 디렉토리에 있는 `default.html` 파일을 확인합니다.

<br>

```html
<script src="../../assets/js/jquery.js"></script>
<script src="../../assets/js/jquery.magnific-popup.js"></script>
<script src="../../assets/js/magnific-popup-setting.js"></script>
```
&nbsp;해당 파일에서는 위와 같이 추가합니다. `src`에 들어가는 경로는 각자의 블로그 경로에 맞게 수정하면 됩니다.

<br><br>

## 게시글에 추가
&nbsp;위에 `magnific-popup-setting.js`파일에서 "2.1"부분에 id를 설정했다면 팝업 기능을 쓰고 싶은 이미지에 적용하려면 다음과 같이 해야 합닏다.

```markdown
<!-- MARKDOWN -->
<!-- id 추가 -->
![image](이미지 파일){: #magnific width="200"}
```

```html
<!-- HTML -->
<!-- id 추가 -->
<img src="이미지 경로" id="magnific" width="200">
```

<br><br>

## 참고 사이트
* <a href="https://terry1213.com/blog/jekyll-magnific-popup/">https://terry1213.com/blog/jekyll-magnific-popup/</a>