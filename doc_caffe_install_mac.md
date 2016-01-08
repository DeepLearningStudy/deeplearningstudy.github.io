---
title: Installation Mac 10.10.2
tags: [caffe]
keywords: start, introduction, begin, install, build, hello world,
last_updated: August 12, 2015
---
By [garion9013](https://www.facebook.com/ohyounghwan?fref=pb_other) on 2015.08.26

이렇게 하면 됩니다~까지는 아니고, 아주 간단하게 제 Mac에서 설치된 사례를 올려놓겠습니다.<br>
대부분의 설치법은 [hoondy님의 블로그](http://goo.gl/pvVjTh)를 참고했으며,
저는 중간에 조금 삽질을 하다가 성공한 케이스로, 이렇게 하면 된다고 장담할 수 없는 부분이 있습니다.<br>
다른 고수분들이 시도해보시면서 직접 피드백 및 보완해 주시리라 믿습니다^^;

***

# Caffe 설치 성공한 환경
   * MacBook Pro (Retina, 15-inch, Mid 2014)
   * OSX Yosemite 10.10.2
   * 2.5GHz Intel Core i7
   * Nvidia GeForce GT 750M 2048MB

# 본 문서가 설명하는 Caffe 설정 및 주의사항
   * GPU 사용가능(cuDNN은 제외<sup>1</sup>)
   * BLAS 라이브러리로 vecLib 사용(다만, 안정성에 문제가 있다고 알려져 있음.<sup>2</sup>)
   * Boost 1.55 사용

# 설치 과정
### 1) Homebrew 설치
[Homebrew.](http://brew.sh) OSX의 패키지 관리 툴입니다. Mac 유저면 대부분 아실것 같아서 구체적인 설치법은 생략합니다.

### 2) Anaconda 파이썬 설치
[Anaconda Python.](https://store.continuum.io/cshop/anaconda) 파이썬의 한 종류인데, 여기서 설치되는 hdf5가 Caffe에서 사용된다고 합니다.

-  사이트 들어가셔서 패키지 다운받고, 홈 폴더에 설치합니다.
- .bash_profile에 PATH 추가합니다. 패키지 설치할 때, 자동으로 추가해주기도 합니다.
```
  export PATH=~/anaconda/bin:$PATH
```
- Cmake를 이용해 컴파일하기위해 annaconda python의 경로를 추가합니다.(HDF5 라이브러리 등을 CMAKE에서 인식하기위함.)
```
export CMAKE_PREFIX_PATH=~/anaconda:$CMAKE_PREFIX_PATH
```

### 3) CUDA 7 설치
- [Nvidia CUDA 7](https://developer.nvidia.com/cuda-downloads)에서 패키지 다운받고, 설치합니다.
- .bash_profile에 PATH 및 Library PATH 추가
```
  export DYLD_LIBRARY_PATH=/usr/local/cuda/lib
  export PATH=/usr/local/cuda/bin:$PATH
```

### 4) cuDNN 설치
일단, 저는 cuDNN을 설치 해보았으나 CUDA 7과 호환이 되지 않아 예제 프로그램을 돌릴 수가 없었고,
결과적으로는 caffe 설치 시에 cuDNN을 사용하지 않았습니다.
안되는 이유를 잠깐 구글링 해봤는데, OSX에서 CUDA 7과 cuDNN 6.5가 호환이 되지 않는다고 합니다. [참고](https://groups.google.com/forum/#!topic/caffe-users/7vYcs7VYUX8)

### 5) Homebrew를 통한 나머지 Dependent 라이브러리 설치
- 편집기로 opencv의 formula 설정을 엽니다.
```
  brew edit opencv
```
- 편집기로 opencv.rb가 열립니다. brew에서 패키지 관리할 때, 각 패키지별로 내부적으로 사용되는 명령어들 같습니다.
다음 라인을 찾아서
```
  args << "-DPYTHON#{py_ver}_LIBRARY=#{py_lib}/libpython2.7.#{dylib}"
  args << "-DPYTHON#{py_ver}_INCLUDE_DIR=#{py_prefix}/include/python2.7"
```
아래 처럼 바꿔주시면 됩니다.
```
  args << "-DPYTHON_LIBRARY=#{py_prefix}/lib/libpython2.7.dylib"
  args << "-DPYTHON_INCLUDE_DIR=#{py_prefix}/include/python2.7"
```
- Boost 1.55를 다운받도록 brew의 Formula를 수정합니다.
```
  cd /usr/local
  git checkout a252214 /usr/local/Library/Formula/boost.rb
```
- 일단, boost를 제외한 라이브러리를 설치합니다.
```
  brew install --fresh -vd snappy leveldb gflags glog szip lmdb homebrew/science/opencv```
  brew install --build-from-source --with-python --fresh -vd protobuf
```
- Boost 1.55를 잘(?) 설치해야합니다.
이 과정에서 삽질을 많이 하였는데, 아래 명령어를 실행한 뒤에 1.55가 설치되는지 확인해보시구요.
다음 두 가지 중 어떤 것을 시도해서 성공했는지, 차이가 뭔지 잘 기억이 나지 않습니다. 워낙 지우고 다시 깔고를 반복하다보니..

1) **Hoondy님 블로그의 방법**
```
  brew install --build-from-source --fresh -vd boost boost-python
```
2) **구글링한 방법**
```
  brew install --build-from-source --with-python --fresh -vd boost
```

### 6) Caffe 설치
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
여기까지 되었으면 일단 성공적입니다.
```
[----------] Global test environment tear-down
[==========] 1160 tests from 198 test cases ran. (242354 ms total)
[  PASSED  ] 1160 tests.

  YOU HAVE 2 DISABLED TESTS
```

- Python에서 caffe를 이용하시려는 분들은 아래와 같이 빌드 명령어를 실행합니다.
```
  make pycaffe
```
이후에 PYTHONPATH를 지정해주어야 합니다.
```
vi ~/.bash_profile
PYTHONPATH = CAFFE ROOT 디렉토리/python
source ~/.bash_profile
```
파이선에서 잘 돌아가는지 확인하기 위해, python을 실행한 뒤에
```
  import caffe
```
를 수행해봐서 정상적으로 라이브러리가 import되는지 확인해보시면 됩니다.
(저 같은 경우에는 homebrew를 이용해서 설치하다보니 /usr/local/Cellar에 protobuf 등의 파이썬 라이브러리가 설치되는 바람에 PYTHONPATH에 protobuf의 라이브러리 위치를 더 추가해야했습니다.)

# Footnotes
1. 문서에서는 cuDNN을 사용한 caffe가 가장 빠르다고 되어있으나, cuDNN 6.5은 현재 OSX에서 CUDA 7.0과 호환이 되지 않는다는 이야기가 있어서 제외했습니다. CUDA 7.0을 설치하고, cuDNN만 써봤는데 예제 코드가 돌아가지 않았습니다.
2. Apple에서 기본적으로 제공하는 BLAS 라이브러리입니다. 하지만, caffe repository에서는 vecLib이 약간의 문제가 있다고 합니다. 때문에, 다른 BLAS 라이브러리(Intel MKL 등)를 설치하는 방법이 있는데, 이와 관련해서는 [hoondy님의 블로그](http://goo.gl/pvVjTh)를 참고하시면 될 것 같습니다.
