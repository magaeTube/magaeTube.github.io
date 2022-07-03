---
title:  "TensorFlow GPU 세팅하기"
date:   2022-04-28 00:00:00 +0900
categories:
  - Data
tags:
  - TensorFlow
  - GPU
  - NVIDIA
toc: true
toc_sticky: true
header-img: https://user-images.githubusercontent.com/78892113/166944077-ca6a7bbf-f71e-4bb4-910e-c244124b3013.png

header-mask: true

---



&nbsp;안녕하세요 마개입니다.  
​이번에는 NVIDIA 그래픽카드가 달린 PC에 TensorFlow GPU를 세팅해서 사용해 보겠습니다.
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/166944077-ca6a7bbf-f71e-4bb4-910e-c244124b3013.png){: .align-center}{: width="80%" height="80%"}

<br><br>

# 내 그래픽카드 확인하기

<br>

저의 PC 환경은 다음과 같습니다.  
**OS :** Ubuntu 20.04 LTS (Focal Fossa)  
**그래픽카드 :** NVIDIA GeForce RTX 3080  

그래픽카드가 무엇인지 정확하게 모르신다면 아래와 같이 명령어로 확인을 해보시면 됩니다. (**<span style="color:red">Ubuntu</span>** 기준입니다.)  

```bash
$ lspci | grep VGA
```

![image](https://user-images.githubusercontent.com/78892113/166945001-35b8a390-efcc-42d1-988a-266df821c0e4.png){: .align-center}

<br>

저와 같이 위의 명령어로 바로 확인이 가능하면 다행인데 만약에 그래픽카드 이름이 잘 안 보이는 경우에는 아래 명령어를 추가로 진행한 후에 다시 명령어를 실행합니다.  
  
```bash
$ sudo update-pciids
```

![image](https://user-images.githubusercontent.com/78892113/166945127-95371fb7-bd10-498c-a7ba-e274aed60aba.png){: .align-center}

<br>

이렇게 확인이 되면 이제 TensorFlow GPU를 세팅해 보겠습니다.

<br><br>

# TensorFlow에서 사용 가능한 디바이스 체크하기

<br>

저는 파이썬을 이용해서 머신러닝, 딥러닝을 하는데 파이썬을 이용해서 GPU 세팅이 되어있는지 먼저 확인해 보겠습니다.  

```python
from tensorflow.python.client import device_lib

print(device_lib.list_local_devices())
```

위와 같이 파이썬 코드를 작성하고 실행합니다.  

![image](https://user-images.githubusercontent.com/78892113/166945857-60e5684a-364e-497e-b388-5d1accbc64a0.png){: .align-center}

그러면 위와 같은 결과가 나오게 되고  

```bash
Skipping registering GPU devices...
[name: "/device:CPU:0"
device_type: "CPU"
memory_limit: 268435456
locality {
}
incarnation: 12676874737017668286
xla_global_id: -1
]
```

마지막은 위와 같은 결과가 있는 것을 볼 수 있습니다. 이를 통해 아직은 CPU만 사용이 가능한 것을 알 수 있습니다.

<br><br>

# NVIDIA GPU 드라이버 설치 

<br>

위에서 확인했던 나의 NVIDIA 그래픽카드의 종류를 확인하고 NVIDIA 홈페이지에서 드라이버를 다운로드합니다.  

<a href="https://www.nvidia.com/download/index.aspx?lang=en-us">NVIDIA 드라이버 다운로드</a>  

![image](https://user-images.githubusercontent.com/78892113/166946526-b9970fd2-85c1-47db-bfed-97fdc97784ce.png){: .align-center}

<br>

각자에 맞는 버전을 세팅하고 검색합니다. 이후에 나오는 결과 파일을 다운로드해서 설치합니다.
설치가 완료되면 다음과 같은 명령어로 설치가 잘 되어있는지 확인합니다.

```bash
$ nvidia-smi
```

![image](https://user-images.githubusercontent.com/78892113/166946708-95daf046-cc45-4a88-b08a-ebf9ec87af83.png){: .align-center}{: width="80%" height="80%"}

위와 같이 잘 뜨는 것을 확인할 수 있습니다. 여기서 봐야 할 것은 CUDA Version인데 저의 경우 **<span style="color:red">11.4</span>**로 나와있는 것을 보실 수 있습니다.

​<br><br>

# CUDA Toolkit 설치

<br>

이제 나의 CUDA 버전을 확인하였으니 Toolkit을 설치합시다.

<a href="https://developer.nvidia.com/cuda-toolkit-archive">CUDA Toolkit</a> 

![image](https://user-images.githubusercontent.com/78892113/166947159-933664a0-ffad-4ce3-b1ca-0aa2db64a21a.png){: .align-center}

위의 사이트로 들어가면 굉장히 많은 버전의 CUDA Toolkit이 있는 것을 보실 수 있는데 각자의 CUDA 버전에 맞는 것을 확인하여 다운로드 진행합니다. 저의 경우 11.4.0 버전을 선택하였습니다.

![image](https://user-images.githubusercontent.com/78892113/166947256-3d7f94b1-3dc2-4c9f-8a1e-5c93c25aff66.png){: .align-center}

그러면 위와 같이 다음 화면이 나오는데 각자의 환경에 맞게 선택을 합니다. 

저의 경우 Ubuntu 20.04를 이용하고 있기 때문에 <u>[Linux] - [x86_64] - [Ubuntu] - [20.04] - [deb(local)]</u> 을 선택하였습니다. 

그러면 저의 경우 아래와 같이 명령어 가이드가 나오게 되는데 이를 하나씩 실행하겠습니다.

```bash
$ wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-ubuntu2004.pin
$ sudo mv cuda-ubuntu2004.pin /etc/apt/preferences.d/cuda-repository-pin-600
$ wget https://developer.download.nvidia.com/compute/cuda/11.4.0/local_installers/cuda-repo-ubuntu2004-11-4-local_11.4.0-470.42.01-1_amd64.deb
$ sudo dpkg -i cuda-repo-ubuntu2004-11-4-local_11.4.0-470.42.01-1_amd64.deb
$ sudo apt-key add /var/cuda-repo-ubuntu2004-11-4-local/7fa2af80.pub
$ sudo apt-get update
$ sudo apt-get -y install cuda
```

설치가 완료되면 다음 단계로 환경 변수를 설정합니다.

<br><br>

# CUDA 환경 변수 설정

<br>

다음으로는 CUDA에 대한 환경 변수를 설정해야 합니다.

```bash
$ export PATH=$PATH:/usr/local/cuda-11.4/bin
$ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-11.4/lib64
$ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-11.4/include
$ export CUDADIR=/usr/local/cuda-11.4

$ source /etc/profile
```

<br>

다음으로 nvcc 명령어를 통해 CUDA driver 버전을 확인합니다.

```bash
$ nvcc -V
```

![image](https://user-images.githubusercontent.com/78892113/166947745-e4116405-7d25-4391-b4b3-fda7311b9420.png){: .align-center}


<br><br>

# cuDNN 설치

<br>

CUDA를 설치한 이후에는 cuDNN SDK를 다운로드해야 합니다. 

<a href="https://developer.nvidia.com/cudnn">cuDNN SDK 다운로드</a> 

![image](https://user-images.githubusercontent.com/78892113/166948118-178d4027-f8bd-43c0-9043-49c620648764.png){: .align-center}

<br>

위의 사이트로 이동하여 **<span style="color:red">Download cuDNN</span>** 버튼을 선택합니다.

![image](https://user-images.githubusercontent.com/78892113/166948269-b17fbe38-dba4-4b87-bf86-90eb7448715d.png){: .align-center}

<br>

그러면 위와 같은 화면이 나오는데 회원가입을 합니다. (계정이 이미 있다면 로그인합니다.) 

![image](https://user-images.githubusercontent.com/78892113/166948334-b6e0c38b-fb38-4037-a77d-0e08ad3a7876.png){: .align-center}

<br>

회원가입 후 간단한 설문조사를 한 후 다운로드를 진행합니다. 저는 **<span style="color:red">Archived cuDNN Releases</span>**를 선택했습니다.

![image](https://user-images.githubusercontent.com/78892113/166948518-e0d9bbde-4f72-40b6-a273-c41847152e3f.png){: .align-center}{: width="80%" height="80%"}

<br>

그러면 위와 같이 나오는데 각자의 버전에 맞는 것을 선택해 주세요

저는 **<span style="color:red">Download cuDNN v8.2.4 [September 2nd, 2021], for CUDA 11.4</span>**를 선택했습니다.

![image](https://user-images.githubusercontent.com/78892113/166948666-8c8ed803-46c1-446d-86f4-4e95a1e8b43e.png){: .align-center}{: width="80%" height="80%"}

그리고 **<span style="color:red">cuDNN Library for Linux [x86_64]</span>**를 선택해서 다운로드합니다.

​다운로드가 완료되면 압축을 풀고 파일을 복사합니다.  

<br>

```bash
# 압축 해제
$ tar -xvzf cudnn-11.4-linux-x64-v8.2.4.15.tgz

# 파일 복사
$ sudo cp cuda/include/cudnn* /usr/local/cuda/include
$ sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64

# 권한 변경
$ sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```

이렇게 파일을 복사했다면 이제 링크를 다시 걸어야 합니다.

<br>

```bash
$ sudo ln -sf /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_adv_train.so.8.2.4 /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_adv_train.so.8
$ sudo ln -sf /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_ops_infer.so.8.2.4 /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_ops_infer.so.8
$ sudo ln -sf /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_cnn_train.so.8.2.4 /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_cnn_train.so.8
$ sudo ln -sf /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_adv_infer.so.8.2.4 /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_adv_infer.so.8
$ sudo ln -sf /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_ops_train.so.8.2.4 /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_ops_train.so.8
$ sudo ln -sf /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_cnn_infer.so.8.2.4 /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_cnn_infer.so.8
$ sudo ln -sf /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn.so.8.2.4 /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn.so.8
$ sudo ldconfig
```

<br>

디렉터리나 파일명은 각자의 버전에 맞게 변경해서 진행합니다.  
제대로 지정이 되었는지 확인해 보겠습니다.

```bash
$ ldconfig -N -v $(sed 's/:/ /' <<< $LD_LIBRARY_PATH) 2>/dev/null | grep libcudnn
```

![image](https://user-images.githubusercontent.com/78892113/166949080-9a8954cc-c5be-4583-ab3d-e03c1a51406f.png){: .align-center}

위와 같이 나왔다면 설정이 된 것입니다.

<br><br>

# GPU 확인

<br>

이제 다시 처음으로 돌아가 위에서 만들었던 파이썬 코드를 실행해서 결과를 확인해 보겠습니다.

![image](https://user-images.githubusercontent.com/78892113/166949554-5b4c88c4-8a65-4e3c-9f49-30883a82386b.png){: .align-center}

파이썬을 실행하면 위와 같은 화면이 나오고 하단에 다음과 같은 결과가 나온 것을 확인할 수 있습니다.

```bash
[name: "/device:CPU:0"
device_type: "CPU"
memory_limit: 268435456
locality {
}
incarnation: 16217097843607302077
xla_global_id: -1
, name: "/device:GPU:0"
device_type: "GPU"
memory_limit: 7994736640
locality {
  bus_id: 1
  links {
  }
}
incarnation: 3263077643678180369
physical_device_desc: "device: 0, name: NVIDIA GeForce RTX 3080, pci bus id: 0000:01:00.0, compute capability: 8.6"
xla_global_id: 416903419
]
```


이제는 CPU뿐만 아니라 GPU도 정상적으로 나오는 것을 확인할 수 있습니다.

