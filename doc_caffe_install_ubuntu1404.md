---
title: Installation Ubuntu 14.04
tags: [caffe]
keywords: start, introduction, begin, install, build, hello world,
last_updated: August 12, 2015
---
By [neuralix](https://www.facebook.com/neuralix?fref=pb_other) on 2015.05.27

# 1. 영문 설치 가이드

[우분투 14.04에서의 Caffe 설치 (영어)](https://github.com/BVLC/caffe/wiki/Ubuntu-14.04-VirtualBox-VM)

안내: CPU 사용자인 경우 링크된 페이지 내 본문 중 Install build essentials: 부분 이후 바로 Install dependencies:부터 수행해나가야 함

# 2. 한글 설치 가이드 (CPU 버젼)

* Caffe 설치 성공한 환경
   * AWS(Amazon Web Service) EC2 인스턴스 (g2.xlarge)
   * Ubuntu 14.04
   * CPU (모델명 미조사)

> 리눅스에 익숙하지 않다면, 모든 명령의 앞에서 sudo를 넣기를 권장합니다.

* 우분투를 설치한다. (Kubuntu 권유)
http://cdimage.ubuntu.com/kubuntu/releases/trusty/release/kubuntu-14.04.1-desktop-amd64.iso
* 빌드 에센셜 설치:
  * `sudo apt-get install build-essential`
* 의존성 라이브러리 설치:
  * `sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libboost-all-dev libhdf5-serial-dev protobuf-compiler gfortran libjpeg62 libfreeimage-dev libatlas-base-dev git python-dev python-pip libgoogle-glog-dev libbz2-dev libxml2-dev libxslt-dev libffi-dev libssl-dev libgflags-dev liblmdb-dev python-yaml`
  * `sudo easy_install pillow`
* Caffe 다운로드:
  * `cd ~`
  * `git clone https://github.com/BVLC/caffe.git`
* Caffe를 위한 파이썬 라이브러리 설치:
  * `cd caffe`
  * `cat python/requirements.txt | xargs -L 1 sudo pip install`
* 두가지의 심볼릭 링크를 생성시킴:
  * `sudo ln -s /usr/include/python2.7/ /usr/local/include/python2.7`
  * `sudo ln -s /usr/local/lib/python2.7/dist-packages/numpy/core/include/numpy/ /usr/local/include/python2.7/numpy`
* `Makefile.config`을 복사해 생성함:
  * `cp Makefile.config.example Makefile.config`
  * `nano Makefile.config`
    * 다음이 있는 줄을 찾아 #를 지워 주석을 풀어줌 `# CPU_ONLY := 1`
    * `PYTHON_INCLUDE`의 밑에서, `/usr/lib/python2.7/dist-packages/numpy/core/include`를 다음으로 바꿔줌. `/usr/local/lib/python2.7/dist-packages/numpy/core/include` (i.e. `/local`를 더해주는 것.)
* Caffe 컴파일:
  * `make pycaffe`
  * `make all`
  * `make test`
* ImageNet Caffe 모델과 레이블 파일을 다운로드함:
  * `./scripts/download_model_binary.py models/bvlc_reference_caffenet`
  * `./data/ilsvrc12/get_ilsvrc_aux.sh`
* [비필수] `python/classify.py` 파일의 안에 `--print_results` 옵션을 추가해줌
  * 이때, `https://github.com/jetpacapp/caffe/blob/master/python/classify.py` (version from 2014-07-18) 를  `https://github.com/BVLC/caffe/blob/master/python/classify.py`와 비교하며 참고함
* 새끼고양이 이미지에 대해 ImageNet 모델을 돌려 설치가 잘 되었는지를 검사함:
  * `cd ~/caffe` (or whatever you called your Caffe directory)
  * `python python/classify.py --print_results examples/images/cat.jpg foo`
  * 정상 결과: `[('tabby', '0.27933'), ('tiger cat', '0.21915'), ('Egyptian cat', '0.16064'), ('lynx', '0.12844'), ('kit fox', '0.05155')]`
* MNIST 필기체 숫자 데이터세트에 대한 훈련 스크립트를 실행해 정상 설치를 검사함:
  * `cd ~/caffe` (or whatever you called your Caffe directory)
  * `./data/mnist/get_mnist.sh`
  * `./examples/mnist/create_mnist.sh`
  * `./examples/mnist/train_lenet.sh`
  * 추가정보는 http://caffe.berkeleyvision.org/gathered/examples/mnist.html 를 보십시요...

# 3. 한글 설치 가이드 (GPU 버젼)

* Caffe 설치 성공한 환경
   * AWS(Amazon Web Service) EC2 GPU 지원 인스턴스 (g2.xlarge)
   * Ubuntu 14.04

> 리눅스에 익숙하지 않다면, 모든 명령의 앞에서 sudo를 넣기를 권장합니다.

* 우분투를 설치한다. (Kubuntu 권유)
http://cdimage.ubuntu.com/kubuntu/releases/trusty/release/kubuntu-14.04.1-desktop-amd64.iso
* 빌드 에센셜 설치:
  * `sudo apt-get install build-essential`

## CUDA 설치 
* 참고 : http://tleyden.github.io/blog/2014/10/25/cuda-6-dot-5-on-aws-gpu-instance-running-ubuntu-14-dot-04/
* CUDA Installer 설치
  * `wget http://developer.download.nvidia.com/compute/cuda/6_5/rel/installers/cuda_6.5.14_linux_64.run`
* 압축해제
  * `chmod +x cuda_6.5.14_linux_64.run`
  * `mkdir nvidia_installers`
  * `./cuda_6.5.14_linux_64.run -extract=\`pwd\`/nvidia_installers`
* Nvidia driver installer 실행:
  * `cd nvidia_installers`
  * `./NVIDIA-Linux-x86_64-340.29.run`
* 다음과 같은 화면을 보게 됨. (이미지: http://tleyden-misc.s3.amazonaws.com/blog_images/install_cuda.png )
* 다음 에러를 만나게 됨:
  * `Unable to load the kernel module 'nvidia.ko'.  This happens most frequently when this kernel module was built against the wrong or improperly configured kernel sources, with a version of gcc that differs from the one used to build the target kernel, or if a driver such as rivafb, nvidiafb, or nouveau is present and prevents the NVIDIA kernel module from obtaining ownership of the NVIDIA graphics device(s), or no NVIDIA GPU installed in this system is supported by this NVIDIA Linux graphics driver release. Please see the log entries 'Kernel module load error' and 'Kernel messages' at the end of the file '/var/log/nvidia-installer.log' for more information.`
* 빠져나와 아래를 설치함:
  * `sudo apt-get install linux-image-extra-virtual`
* grub 변경을 물으면 “choose package maintainers version”를 선택.
* Reboot:
  * `reboot`

## Nouveau 해제
* nvidia 커널 모듈과 충돌 일으키므로 명시적으로 해제해줘야 함
* 파일을 연다. (주의. 리눅스 기본 문서 편집기 vi의 사용법을 알아야 합니다.)
  * `vi /etc/modprobe.d/blacklist-nouveau.conf`
* 다음을 추가시킨다.
  * `blacklist nouveau`
  * `blacklist lbm-nouveau`
  * `options nouveau modeset=0`
  * `alias nouveau off`
  * `alias lbm-nouveau off`
* 파일 저장하고 나옴.
* 커널 Nouveau를 해제함:
  * `echo options nouveau modeset=0 | sudo tee -a /etc/modprobe.d/nouveau-kms.conf`
* 리부트함:
  * `update-initramfs -u`
  * `reboot`

## 다시 반복하기
* 커널 소스 다운로드하고 설치하기:
  * `sudo apt-get install linux-source`
  * `sudo apt-get install linux-headers-3.13.0-37-generic`
* Nvidia driver installer를 다시 실행:
  * `cd nvidia_installers`
  * `sudo ./NVIDIA-Linux-x86_64-340.29.run`
* nvidia 커널 모듈의 로드:
  * `modprobe nvidia`
* CUDA + samples 인스톨러 실행:
  * `sudo ./cuda-linux64-rel-6.5.14-18749181.run`
  * `sudo ./cuda-samples-linux-6.5.14-18745345.run`
* CUDA 정상설치 여부 검사:
  * `cd /usr/local/cuda/samples/1_Utilities/deviceQuery`
  * `make`
  * `./deviceQuery  ` 
* 다음 출력 메시지가 나와야 함:
  * `deviceQuery, CUDA Driver = CUDART, CUDA Driver Version = 6.5, CUDA Runtime Version = 6.5, NumDevs = 1, Device0 = GRID K520 Result = PASS'

## 공통부분 설치
* 의존성 라이브러리 설치:
  * `sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libboost-all-dev libhdf5-serial-dev protobuf-compiler gfortran libjpeg62 libfreeimage-dev libatlas-base-dev git python-dev python-pip libgoogle-glog-dev libbz2-dev libxml2-dev libxslt-dev libffi-dev libssl-dev libgflags-dev liblmdb-dev python-yaml`
  * `sudo easy_install pillow`
* Caffe 다운로드:
  * `cd ~`
  * `git clone https://github.com/BVLC/caffe.git`
* Caffe를 위한 파이썬 라이브러리 설치:
  * `cd caffe`
  * `cat python/requirements.txt | xargs -L 1 sudo pip install`
* 두가지의 심볼릭 링크를 생성시킴:
  * `sudo ln -s /usr/include/python2.7/ /usr/local/include/python2.7`
  * `sudo ln -s /usr/local/lib/python2.7/dist-packages/numpy/core/include/numpy/ /usr/local/include/python2.7/numpy`
* `Makefile.config`을 복사해 생성함:
  * `cp Makefile.config.example Makefile.config`
  * `vi Makefile.config` (GUI환경의 터미널이라면 'nano Makefile.config`)
    * 다음이 있는 줄을 찾아 #를 지워 주석을 확인함 `# CPU_ONLY := 1` (주의. CPU 경우와 다름)
    * `PYTHON_INCLUDE`의 밑에서, `/usr/lib/python2.7/dist-packages/numpy/core/include`를 다음으로 바꿔줌. `/usr/local/lib/python2.7/dist-packages/numpy/core/include` (i.e. `/local`를 더해주는 것.)
* Caffe 컴파일:
  * `make pycaffe`
  * `make all`
  * `make test`
* ImageNet Caffe 모델과 레이블 파일을 다운로드함:
  * `./scripts/download_model_binary.py models/bvlc_reference_caffenet`
  * `./data/ilsvrc12/get_ilsvrc_aux.sh`
* [비필수] `python/classify.py` 파일의 안에 `--print_results` 옵션을 추가해줌
  * 이때, `https://github.com/jetpacapp/caffe/blob/master/python/classify.py` (version from 2014-07-18) 를  `https://github.com/BVLC/caffe/blob/master/python/classify.py`와 비교하며 참고함
* 새끼고양이 이미지에 대해 ImageNet 모델을 돌려 설치가 잘 되었는지를 검사함:
  * `cd ~/caffe`
  * `python python/classify.py --print_results examples/images/cat.jpg foo`
  * 정상 결과: `[('tabby', '0.27933'), ('tiger cat', '0.21915'), ('Egyptian cat', '0.16064'), ('lynx', '0.12844'), ('kit fox', '0.05155')]`
* MNIST 필기체 숫자 데이터세트에 대한 훈련 스크립트를 실행해 정상 설치를 검사함:
  * `cd ~/caffe`
  * `./data/mnist/get_mnist.sh`
  * `./examples/mnist/create_mnist.sh`
  * `./examples/mnist/train_lenet.sh`
  * 추가정보는 http://caffe.berkeleyvision.org/gathered/examples/mnist.html 를 보십시요...

> 미주. Anaconda는 Path 설정이 능숙하신 분이 아니면 Caffe 활용용도에 있어 설치를 권장하지 않습니다.
