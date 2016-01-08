---
title: C++ Example 1. Hello Caffe
tags: [caffe]
keywords: start, introduction, begin, install, build, hello world,
last_updated: August 12, 2015
summary: "이 예제는 caffe 설치 후 처음으로 caffe 라이브러리를 사용하는 법을 익히는 hello caffe 예제입니다. 우분투를 기본 플렛폼으로 하여 설명합니다. 윈도우즈 및 맥 이용자께는 죄송하지만 소스 파일은 동일하므로 큰 어려움은 없으실걸로 기대합니다."
---
By [koosy](https://www.facebook.com/Koosyong) on 2015.05.27

##  준비
### 1. Git를 이용해 작업폴더 구축하기 
(이미 만드신 분은 2번으로 넘어가시면 됩니다.)

* 작업하고 싶은 위치에서 caffestudy 프로젝트 파일을 복사해 옵니다. 

```      
git clone https://github.com/DeepLearningStudy/caffe.git
```

* caffestudy 폴더가 잘 복사되었나 확인합니다. 

```      
cd caffestudy
```

### 2. CMakeLists.txt 파일 
본 예제에서는 (그리고 앞으로도) CMake를 이용해서 프로젝트를 구축할것 입니다. [CMake](http://www.cmake.org/)에 대한 설명은 생략하도록 하겠습니다. 잘 모르셔도 예제가 어렵지 않으니 따라오면서 익히시면 됩니다. 우선 hellocaffe 프로젝트를 정의한 [CMakeLists.txt](https://github.com/DeepLearningStudy/caffe/blob/master/examples/ex1_hellocaffe/CMakeLists.txt) 파일을 열어보겠습니다. 

```      
cd examples/ex1_hellocaffe
gedit CMakeLists.txt
```

첫 예제인만큼 한 줄 한 줄 같이 살펴보겠습니다. 다음 예제부터는 새롭게 추가된 부분만 살펴보도록 하겠습니다. 

* 본 프로젝트를 컴파일 하기 위한 cmake 최소버전을 명시 합니다.  

```
cmake_minimum_required(VERSION 2.8.8)
```

* 프로젝트 이름을 hellocaffe로 정합니다. 

```
project (hellocaffe)
```

* caffe를 설치할 때 자동으로 설정되는 라이브러리 및 헤더파일 페키지를 찾습니다. 

```
find_package(Caffe)
```

* 본 프로젝트에 caffe 헤더파일이 있는 경로를 삽입합니다. 설치한 플렛폼 별로 caffe 디렉토리 경로는 Caffe package 안에 정의된 Caffe_INCLUDE_DIRS 변수에 저장되어 있습니다. 

```
include_directories(${Caffe_INCLUDE_DIRS})
```

* 그 외 컴파일에 필요한 사전정의 값들을 읽어옵니다.

```
add_definitions(${Caffe_DEFINITIONS})
```

* 실행파일을 만들 때 필요한 정보를 알려줍니다. 여기서 실행파일 이름은 hellocaffe로 하였고, 실행파일을 생성하기 위한 소스 파일로는 main.cpp 파일이 있습니다.

```
add_executable(hellocaffe main.cpp)
```

* 프로젝트를 컴파일하고 빌드하기 위한 라이브러리들을 알려줍니다. 이 역시 Caffe package 안에 사전정의된 Caffe_LIBRARIES 변수를 추가하면 됩니다.

```
target_link_libraries(hellocaffe ${Caffe_LIBRARIES})
```

***

## 실행
### 1. CMake를 이용해 컴파일 및 빌드하기

* 원하시는 위치에 build 파일들을 생성할 폴더를 만듭니다. 

    본 예제에서는 프로젝트 폴더 caffestudy/examples/ex1_hellocaffe 안에 생성한다고 가정합니다. (혹시 나중에 Git으로 소스를 올려주실 분은 build 폴더는 삭제하고 올려주시기 바랍니다.)

```
mkdir build & cd build
```

* cmake 합니다. 

    cmake는 CMakeLists.txt 파일에 정의된 대로 프로젝트를 컴파일 하고 빌드하기 위한 make 파일들을 생성하는데요. 이 때 현재 사용하시는 플렛폼에 적합하도록 여러가지 설정을 통해 자동 생성합니다. 따라서 플렛폼 의존적인 make 파일을 매번 새로 만들 필요없이, CMakeLists.txt와 소스파일들만 가지고도 여러가지 플렛폼(리눅스, 윈도우즈, 맥OS 등) 위에서 컴파일이 가능한 프로젝트를 생성합니다. (다 아시는 얘기겠지만, 처음이니깐 이해바람.. ^^;) 요즘에는 당연한 일이지만, 예전에 CMake가 없을 때에 비하면 엄청난 기능이고, 오픈소스에 지대한 공헌을 한게 CMake 입니다. ccmake 등으로 프로젝트 설정을 쉽게 세팅할 수 있는데요, 생략하겠습니다. 이 단계는 앞으로는 '그냥 cmake 합니다.' 라고만 적고 넘어가겠습니다.
 
 CMakeLists.txt 소스가 다른 곳에 있는 경우는 cmake 다음에 경로를 적어줍니다. 

```
cmake ..
```

* make 합니다. 

   현재 폴더에 make 파일이 생성되어 있으므로, 경로 지정없이 make 하시면 컴파일 및 실행파일이 생성됩니다.

```
make
```

   다음 문구를 찾으시면 첫 번째 caffe를 이용한 프로그램이 문제없이 생성이 되었습니다. 

```
...
[100%] Built target hellocaffe
```

* hellocaffe를 실행합니다. 

```
./hellocaffe
```
   
   다음과 같은 실행결과를 보실 수 있습니다. (아래 1.80006e+06 숫자는 바뀔 수 있습니다.)

```
Size of blob: N=20 K=30 H=40 W=50 C=1200000
expected asum of blob: 1.80006e+06
asum of blob on cpu: 1.80006e+06
```
   이 결과가 무엇인지 궁금하시죠? 아래 소스 파일을 같이 살펴봅시다. 

***

## 소스파일
### 1. 프로젝트 소스 파일 열기
eclipse, codeblocks, vim 등 각자 선호하는 코딩 툴이 있을텐데요. 저는 개인적으로 QtCreator를 선호합니다. 본 예제는 QtCreator를 이용해서 프로젝트 소스 파일을 열어보겠습니다. (다른 툴을 이용하시는 분들은 각자의 툴로 main.cpp 파일을 여신 다음에 2번으로 넘어가시면 됩니다.)

* 우분투에서는 아래와 같이 QtCreator를 설치할 수 있습니다. 

```
   sudo apt-get install qtcreator
```

   다른 운영체제 QtCreator는 [여기](https://www.qt.io/download-open-source/)서 다운 받으실 수 있습니다. 

* ex1_hellocaffe 폴더로 이동하신 후에 아래처럼 프로젝트를 엽니다.

```
qtcreator CMakeLists.txt
```

아래와 같은 화면에서 빌드 파일을 생성할 폴더를 선택합니다. 위에서 생성한 ex1_hellocaffe/build 폴더를 선택하셔도 되고 따로 만드셔도 됩니다.

![project configuration](https://raw.githubusercontent.com/DeepLearningStudy/caffe/master/docs/wiki/ex1_hellocaffe/project.png)
폴더 선택 후에 configure project 버튼을 누르면, 아래 그림처럼 hellocaffe 이름으로 프로젝트가 열리고, CMakeLists.txt 파일 및 main.cpp 파일을 확인 또는 편집할 수 있습니다.

![project configuration](https://raw.githubusercontent.com/DeepLearningStudy/caffe/master/docs/wiki/ex1_hellocaffe/main.png)
> QtCreator Tip: Compile & Build는 Ctrl+B, 생성된 프로그램 실행은 Ctrl+R 입니다. 

### 2. main.cpp 파일
그럼 이제 `hellocaffe` 예제에 사용된 

[`main.cpp`](https://github.com/DeepLearningStudy/caffe/blob/master/examples/ex1_hellocaffe/main.cpp) 소스코드를 차례로 살펴보도록 하겠습니다. 

* 헤더파일 및 선언부

```
#include <stdio.h>

#include "caffe/caffe.hpp"
#include "caffe/util/io.hpp"
#include "caffe/blob.hpp"
#include "caffe/common.hpp"
#include "caffe/filler.hpp"

using namespace caffe;
using namespace std;
typedef double Dtype;
```

앞에서 `CMakeLists.txt` 파일에 헤더파일 경로를 추가했기 때문에, caffe관련 헤더파일은 `caffe/` 로 시작하면 됩니다. 그 외 `caffe`, `std` 라이브러리를 위한 `namespace`를 지정하고, 데이터 타입 (`Dtype`) 으로는 `double` 을 사용합니다. caffe 라이브러리는 템플릿으로 구성되어 있어서 이와 같이 임의의 데이터 형태를 선언하고 사용할 수 있습니다.

* blob 생성

```
Blob<Dtype>* const blob = new Blob<Dtype>(20, 30, 40, 50);
   if(blob){
      cout<<"Size of blob:";
      cout<<" N="<<blob->num();
      cout<<" K="<<blob->channels();
      cout<<" H="<<blob->height();
      cout<<" W="<<blob->width();
      cout<<" C="<<blob->count();
      cout<<endl;
   }
```
[blob](http://caffe.berkeleyvision.org/tutorial/net_layer_blob.html)은 caffe의 기초를 이루는 데이터구조 입니다. 다음 예제에서 자세히 설명하겠지만, 여기서는 간단히 어떻게 생성하는지만 알아보겠습니다. 

blob은 number N x channel K x height H x width W 로 구성된 4차원 배열입니다. Blob 템플릿을 통해 새로운 blob을 생성할 때, 다음처럼 데이터 타입(`Dtype`) 및 크기(20, 30, 40, 50)를 초기화 할 수 있습니다:`new Blob<Dtype>(20, 30, 40, 50)`.

앞의 예제에서는 `blob`은 생성된 blob의 주소값을 가지므로, `blob`이 잘 생성되었다면 null이 아닌 값을 가지므로 `if(blob)`으로 생성여부를 검사할 수 있습니다. 

생성된 blob의 각 차원 영역의 크기는 `num()`,`channels()`, `height()`, `width()`으로 얻을 수 있으며, 볼륨(N x K x H x W)은 `count()`으로 받습니다.

```
Size of blob: N=20 K=30 H=40 W=50 C=1200000
```

* filler를 이용한 blob 데이터 초기화

```
FillerParameter filler_param;
filler_param.set_min(-3);
filler_param.set_max(3);
UniformFiller<Dtype> filler(filler_param);
filler.Fill(blob);
```

filler 역시 다음 예제에서 더 자세히 설명할텐데요, 여기서는 생성된 blob에 임의의 초기값을 할당하는데 사용하였습니다. `UniformFiller`는 주어진 영역 (-3~3) 안에서 균일하게 임의의 값을 생성(균일분포에서 각 값을 샘플링)하여 blob을 모든 요소값을 채웁니다. 

* blob 절대값 합 계산

```
Dtype expected_asum = 0;
const Dtype* data = blob->cpu_data();
for (int i = 0; i < blob->count(); ++i) {
   expected_asum += fabs(data[i]);
}    
cout<<"expected asum of blob: "<<expected_asum<<endl;
cout<<"asum of blob on cpu: "<<blob->asum_data()<<endl;
```

blob은 cpu 또는 gpu 상에서 여러가지 연산을 할 수 있는 기능을 제공합니다. 위의 예제는 blob의 데이터를 불러와서 (`blob->cpu_data();`) 각 요소의 절대값을 모두 합한 `expected_asum` 값과, blob에서 제공하는 절대값의 합을 구하는 기능 (`blob->asum_data()`)을 이용한 결과가 같다는 것을 보입니다. 

```
expected asum of blob: 1.80006e+06
asum of blob on cpu: 1.80006e+06
```

위의 예제에서, `blob->cpu_data();`에서 굳이 왜 cpu를 명시하는지, 그럼 gpu 위에서는 어떻게 계산을 하는지, cpu와 gpu의 계산 속도 차이는 얼마나 되는지 등이 궁금하시죠? 그건 다음 예제에서 blob의 다른 기능들과 함께 설명하도록 하겠습니다. 
