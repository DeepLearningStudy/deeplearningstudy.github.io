---
title: Installation Ubuntu 12.04
tags: [caffe]
keywords: start, introduction, begin, install, build, hello world,
last_updated: August 12, 2015
---
By [garion9013](https://www.facebook.com/ohyounghwan?fref=pb_other) on 2015.08.26

개인적인 필요가 있어서 Neuralix님이 올려주신 [Ubuntu 14.04 설치법](install_ubuntu) 및
[caffe 사이트의 설치법](http://caffe.berkeleyvision.org/installation.html) 참조하여, cmake 빌드에 성공하였습니다.
이미 성공한 뒤에 막 올리는 설치법이기 때문에 CUDA에 대한 설치법은 적지 않겠으며, 기억에 의존해서 적는 방법들이기 때문에 성공을 장담하지는 못합니다.
매우 친절하지 못한 설치법입니다만, 제 환경에서 어떤 방식으로 설치하였는지 요약해놓을 겸, 필요하신 분이 있을 것 같아서 올려둡니다.
더 구체적인 부분은 추후에 업데이트하거나, 시도하시는 다른 분들이 자유롭게 업데이트 해주시리라 믿고 진행하겠습니다. 

***

# Caffe 설치 성공한 환경
   * Ubuntu 12.04 (3.2.0-84-generic)
   * Intel(R) Core(TM) i5-2500 CPU @ 3.30GHz
   * Nvidia GeForce GTX 650

# 본 문서가 설명하는 Caffe 설정 및 주의사항
   * GPU 사용가능
   * CUDA 7.0, cuDNN(R2) 설치함
   * BLAS 라이브러리로 atlas 사용
   * Boost 1.55 사용
   * OpenCV 2.4 이상 사용
   * Cmake 3.3 사용

# 설치 과정
### 1) 의존성 패키지 설치
[caffe 사이트](http://caffe.berkeleyvision.org/installation.html)와 일치하는 의존성 패키지 목록들 입니다.
```
sudo apt-get install build-essential
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libhdf5-serial-dev protobuf-compiler
sudo apt-get install libatlas-base-dev
```

### 2) 12.04에서 apt-get으로 지원하지 않는 의존성 패키지 설치
14.04에는 포함되어 있으나 12.04에는 포함되지 않은 의존성 패키지들을 다운받아 설치합니다.
caffe 사이트의 설치법과 동일합니다.
```
# glog
wget https://google-glog.googlecode.com/files/glog-0.3.3.tar.gz
tar zxvf glog-0.3.3.tar.gz
cd glog-0.3.3
./configure
make && make install
# gflags
wget https://github.com/schuhschuh/gflags/archive/master.zip
unzip master.zip
cd gflags-master
mkdir build && cd build
export CXXFLAGS="-fPIC" && cmake .. && make VERBOSE=1
make && make install
# lmdb
git clone https://github.com/LMDB/lmdb
cd lmdb/libraries/liblmdb
make && make install
```

### 3) Caffe 홈페이지와 일치하지 않는 의존성 패키지(OpenCV, boost) 설치
- Boost 1.55<br>
caffe 홈페이지에서 boost 1.55 이상을 권장하고 있습니다만, 적혀있는 설치법대로 하면 하위 버전이 설치되는 것 같아서
패키지 이름을 바꿔서 시도했습니다.
```
sudo apt-get install libboost1.55-all-dev
```
- OpenCV 2.4 이상<br>
Ubuntu 12.04에서는 OpenCV의 최신 버젼을 다운 받을 수 없고, 기존 버전은 버그가 나기 때문에 ppa를 통해 OpenCV 2.4버젼을 설치합니다.
ppa를 이용하면, "출처가 확인되지 않은" pre-built package들을 설치할 수 있습니다. 사용시 주의바랍니다.
설치시 2.4 버젼이 깔리는지 확인합니다.
```
sudo add-apt-repository ppa:yjwong/opencv2
sudo apt-get update
sudo apt-get install libopencv-dev
```

### 4) Anaconda python 설치
HDF5 등 python 의존성 라이브러리가 [Anaconda python](https://store.continuum.io/cshop/anaconda/)을 통해 설치됩니다.
그냥 python을 가지고 진행해도 되지만, 후에 혹시 문제가 생길까봐 Anaconda python을 설치했습니다.
구체적인 과정은 간단하므로 생략하고, CMAKE의 Path 설정을 해줘야합니다. ~/.bashrc파일을 열어서 다음과 같은 내용을 추가합니다.
```
export PATH=/home/Username/anaconda/bin:$PATH (자동으로 추가되기도 합니다.)
export CMAKE_PREFIX_PATH=/home/Username/anaconda:$CMAKE_PREFIX_PATH
```

### 5) Python 의존성 라이브러리 설치
numpy, pandas 등 python에서 필요한 라이브러리를 설치합니다.
```
cd $CAFFEROOT/python
for req in $(cat requirements.txt); do pip install $req; done
```

### 6) CUDA 설치
생략 (설치 디렉토리 /usr/local/cuda)

### 7) cuDNN 설치
생략 (설치 디렉토리 /usr/local/cuda/lib64 및 include에 직접 복사함.)

### 7) Cmake 3.3 설치
생략

### 8) Caffe 설치
- Github에서 caffe를 다운 받습니다.
```
  git clone https://github.com/BVLC/caffe.git
```

- caffe의 루트디렉토리에서 build 디렉토리를 생성하고 빌드합니다.
```
mkdir build
cd build
cmake ..
make
```

- compile된 Caffe가 정상적으로 동작하는지 확인하기 위해 test를 수행합니다.
```
  make runtest
```

저는 아래와 같은 결과가 나왔습니다.
여기까지 되었으면 성공입니다.
```
[----------] Global test environment tear-down
[==========] 1160 tests from 198 test cases ran. (242354 ms total)
[  PASSED  ] 1160 tests.

  YOU HAVE 2 DISABLED TESTS
```
