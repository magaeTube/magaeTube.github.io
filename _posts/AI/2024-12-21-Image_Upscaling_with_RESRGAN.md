---
title:  "Real-ESRGAN 기술을 이용하여 이미지 업스케일링하기"
date:   2024-12-21 00:00:00 +0900
categories:
  - AI
  - Rembg
tags:
  - AI
  - Rembg
  - R-ESRGAN
toc: true
toc_sticky: true
toc_label: "Real-ESRGAN"
header-img: 
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
&nbsp;AI로 만든 이미지에 대해서 품질을 향상하기 위해 시도해보고 있는 방법을 작성합니다.   
{: .notice--info}

<br>

![Real-ESRGAN](https://github.com/user-attachments/assets/5c223940-ae0a-48f0-819a-8f31c7e29ede){: .align-center}{: width="70%" height="70%"} 

<br><br>

## 현재의 상황

![winter glove](https://github.com/user-attachments/assets/f0824bd5-9302-44d7-8e58-1fcac936c118){: .align-center}{: width="70%" height="70%"} 

<br>

&nbsp;AI로 위와 같은 이미지를 생성해보았습니다. AI로 이미지 자체는 어느 정도 잘 만들어졌지만 사이즈도 작고 디테일한 부분에서 품질은 떨어져보여서 업스케일링을 진행했습니다.

<br><br>

## 이미지 업스케일링
&nbsp;파이썬으로 이미지를 업스케일링하는 것은 몇 가지 방법이 있는데 저는 아래 두 가지 방법을 순차적으로 이용했습니다.

* PIL의 resize 이용
* Real-ESRGAN 이용

<br>

### PIL의 resize 이용
&nbsp;처음으로 `PIL`을 사용한 이유는 이미 AI로 만든 이미지를 PIL을 이용하여 Crop하고 파일 포맷을 Convert하고 있었기 때문입니다. 이미 사용중이었기 때문에 제공해주는 기능을 이용했습니다.

<br>

```python
from PIL import Image

input_image = "수정할 이미지 파일명"

image = Image.open(input_image)

# resize하고 하는 가로 크기
target_width = 4000

# 기존 이미지의 비율을 그대로 이용
original_width, original_height = image.size

aspect_ratio = original_height / original_width
target_height = int(target_width * aspect_ratio)

# resize를 이용하여 이미지 사이즈 조정
resized_img = image.resize((target_width, target_height))
```

&nbsp;위와 같이 작업하고 실행하면 이미지 사이즈가 커진 것을 확인하실 수 있습니다.

<br>

![PIL resize](https://github.com/user-attachments/assets/6b309015-1d84-45ed-a0fc-78fac6cdc3e3){: .align-center}  
&nbsp;다만 이미지를 확대해서 보면 이렇게 선명하지 않은 부분이 있다는 것을 확인할 수 있습니다. 그렇기에 이러한 부분을 더 선명하고 좋은 품질을 가질 수 있도록 하는 방법에 대해 알아보다가 알게 된 것이 `Real-ESRGAN`입니다.

<br><br>

### Real-ESRGAN 이용
&nbsp;Real-ESRGAN은 고품질 이미지 업스케일링과 복원에 사용되는 딥러닝 기반 모델입니다. 주로 저해상도 이미지나 품질이 낮은 이미지를 높은 해상도로 변환시키고 복원하는데 사용됩니다.
* 공식 사이트 : <a href="https://github.com/xinntao/Real-ESRGAN">https://github.com/xinntao/Real-ESRGAN</a>

<br>

#### 사용해보기
&nbsp;Real-ESRGAN은 Windows, Linux, MacOS 3가지 OS에 대해서 모두 지원을 하며 윈도우의 `exe`와 같이 실행 파일을 같이 제공합니다. 하지만 저는 제가 만들고 있는 파이썬 프로젝트에서 이용할 것이라서 `파이썬`으로 실행해보도록 하겠습니다.  
&nbsp;그리고 해당 모델은 딥러닝 기반 모델이기 때문에 `GPU`를 요구합니다. GPU의 성능이 좋으면 좋을수록 빠르게 처리됩니다. 저의 경우 NVIDIA GeForce 4070 Ti를 사용 중이고 사전에 CUDA 설치를 완료했습니다. 이 부분은 생략합니다.  
  
&nbsp;위에서 제공하는 사이트에 접속하면 설치 방법과 실행 방법을 알려줍니다.

```sh
$ git clone https://github.com/xinntao/Real-ESRGAN.git
$ cd Real-ESRGAN
```

&nbsp;먼저 해당 소스코드를 직접 다운로드(clone) 받고 해당 프로젝트로 이동합니다.

<br>

```sh
# Install basicsr - https://github.com/xinntao/BasicSR
# We use BasicSR for both training and inference
$ pip install basicsr
# facexlib and gfpgan are for face enhancement
$ pip install facexlib
$ pip install gfpgan
$ pip install -r requirements.txt
$ python setup.py develop
```
&nbsp;그리고 해당 코드를 실행하기 위한 `Dependencies`를 설치합니다. 패키지는 본인이 이용하고 있는 파이썬에 설치합니다. 저의 경우 `venv`를 이용하여 프로젝트별로 패키지 관리를 하기 때문에 `venv`를 따로 만들고 설치하겠습니다.

<br>

```sh
# Download pre-trained models
$ wget https://github.com/xinntao/Real-ESRGAN/releases/download/v0.1.0/RealESRGAN_x4plus.pth -P weights

# Execute
$ python inference_realesrgan.py -n RealESRGAN_x4plus -i inputs --face_enhance
```
&nbsp;실행을 하기 위해서 이미 훈련이 된 모델을 다운로드합니다. 모델의 경우 기본적으로는 2배, 4배가 있고 영상을 위한 모델도 있습니다. 이 <a href="https://github.com/xinntao/Real-ESRGAN/releases">페이지</a>에서 확인하실 수 있습니다.  
&nbsp;다운로드 받은 모델을 이용해서 위 명령어로 실행할 수 있습니다. 여기에서 `-n` 파라미터는 모델명을, `-i`는 input 경로 또는 파일을, `--face_enhance`는 `GFPGAN`을 사용할지 말지 정하는 파라미터입니다. 위의 예시에는 `inputs`라는 폴더 안에 있는 이미지들을 대상으로 업스케일링을 진행합니다. 그 외에 파라미터들은 다음과 같습니다.

```sh
Usage: python inference_realesrgan.py -n RealESRGAN_x4plus -i infile -o outfile [options]...

A common command: python inference_realesrgan.py -n RealESRGAN_x4plus -i infile --outscale 3.5 --face_enhance

  -h                   show this help
  -i --input           Input image or folder. Default: inputs
  -o --output          Output folder. Default: results
  -n --model_name      Model name. Default: RealESRGAN_x4plus
  -s, --outscale       The final upsampling scale of the image. Default: 4
  --suffix             Suffix of the restored image. Default: out
  -t, --tile           Tile size, 0 for no tile during testing. Default: 0
  --face_enhance       Whether to use GFPGAN to enhance face. Default: False
  --fp32               Use fp32 precision during inference. Default: fp16 (half precision).
  --ext                Image extension. Options: auto | jpg | png, auto means using the same extension as inputs. Default: auto
```

<br>

#### 결과
&nbsp;해당 모델을 이용해서 위에서 봤던 장갑에 대해 실행해보고 결과를 출력해봤습니다. 결과는 `results`폴더에서 확인하실 수 있습니다.

![image](https://github.com/user-attachments/assets/07d7ad8b-f6bf-40d3-94ed-2a9b4459c8ce){: .align-center}  
  
&nbsp;이미지에서 볼 수 있듯이 똑같이 확대를 했지만 위에서 봤던 이미지보다 더 선명한 이미지를 볼 수 있습니다.

<br>

#### TroubleShooting
&nbsp;Real-ESRGAN을 설치하고 이용하는 과정에서 겪은 문제와 해결과정을 정리합니다. 

**ImportError: ....../site-packages/torch/lib/../../nvidia/cusparse/lib/libcusparse.so.12: undefined symbol: __nvJitLinkComplete_12_4, version libnvJitLink.so.12**
&nbsp;우리가 설치한 파이썬 패키지 중에 nvidia 관련 패키지가 있는데 그것이 `LD_LIBRARY_PATH`에 등록되어 있지 않아서 발생한 에러입니다. 해당 환경변수는 공유 라이브러리의 경로를 지정하는데 사용되는 환경 변수로 CUDA환경에서는 CUDA 라이브러리와 종속성 라이브러리를 로드하는데 사용됩니다.  
&nbsp;이미 CUDA를 설치하면서 해당 환경변수에 설정을 했지만 파이썬 패키지도 같이 설정해줘야 합니다.  

```sh
$ export LD_LIBRARY_PATH=$PWD/venv/lib/python3.10/site-packages/nvidia/nvjitlink/lib:$LD_LIBRARY_PATH

# 영구적으로 설정 (.bashrc 또는 .zshrc)
$ echo 'export LD_LIBRARY_PATH=$PWD/venv/lib/python3.10/site-packages/nvidia/nvjitlink/lib:$LD_LIBRARY_PATH' >> ~/.bashrc
$ source ~/.bashrc
```
&nbsp;저의 경우 `venv`를 사용해서 파이썬 패키지를 설치했기에 위와 같이 했으니 각자의 위치에 맞게 설정해주시면 됩니다.

<br>

**ModuleNotFoundError: No module named 'torchvision.transforms.functional_tensor'**
```sh
...
...
...
  File "/home/.../venv/lib/python3.10/site-packages/basicsr/data/realesrgan_dataset.py", line 11, in <module>
    from basicsr.data.degradations import circular_lowpass_kernel, random_mixed_kernels
  File "/home/.../venv/lib/python3.10/site-packages/basicsr/data/degradations.py", line 8, in <module>
    from torchvision.transforms.functional_tensor import rgb_to_grayscale
ModuleNotFoundError: No module named 'torchvision.transforms.functional_tensor'
```
&nbsp;다음으로 위와 같은 에러가 발생할 수 있는데 위에 보이는 `degradations.py`에서 해당 모듈을 찾을 수 없다는 에러입니다.

<br>

![source code](https://github.com/user-attachments/assets/e33d3ade-f32d-4171-97fd-fc03f4aef996)
  
&nbsp;위와 같이 8번째 줄에 `torchvision.transforms.functional_tensor` 모듈이 들어가있는 것을 확인할 수 있습니다. 이를 다음과 같이 수정합니다.

<br>

```python
from torchvision.transforms.functional import rgb_to_grayscale
```

![source code](https://github.com/user-attachments/assets/d62261f3-9604-499b-b35b-b2ef63a40ee2)
  
&nbsp;위와 같이 코드를 변경하고 다시 실행하면 됩니다.

<br>

## 결론
&nbsp;PIL로 단순하게 리사이즈 작업을 하는 것에는 품질이 떨어지는 딥러닝 모델인 Real-ESRGAN 모델을 이용하니까 한껏 선명해진 것을 알 수 있었습니다. 해당 모델을 이용했을 때 `선명한 품질`을 가져올 수 있지만 `GPU`가 필요하고 GPU의 성능에 따라 속도가 차이가 있다는 단점이 있습니다. 