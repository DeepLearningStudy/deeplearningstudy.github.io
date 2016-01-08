---
title: C++ Example 2. Blob
tags: [caffe]
keywords: start, introduction, begin, install, build, hello world,
last_updated: August 12, 2015
summary: "두 번째 예제는 Caffe의 기본 구조체인 Blob에 대해서 좀 더 알아보겠습니다. "
---
By [koosy](https://www.facebook.com/Koosyong) on 2015.09.24

[Blob](http://caffe.berkeleyvision.org/tutorial/net_layer_blob.html)은 Caffe의 기본 데이터구조 입니다. 
예제파일은 [요기](https://github.com/DeepLearningStudy/caffe/tree/master/examples/ex2_blob)있습니다. 파일 [준비](http://deeplearningstudy.github.io/material/doc_caffe_example_1hellocaffe.html#준비)단계는 앞에서 설명했으므로 생략하구요, 바로 소스파일로 들어가 봅시다.  

## 소스파일
이번 예제에서는 두 개의 소스파일을 준비했습니다. `ex_blob.cpp`과 `ex_math.cpp` 인데요. CMake에 아직 익숙하지 않으신 분들을 위해서 한 프로젝트에서 두 개의 실행파일을 어떻게 만드는지 간단히 알아볼까요? `CMakeLists.txt`파일을 열어보면 다음과 같습니다. (프로젝트 파일을 여는 방법은 [여기](http://deeplearningstudy.github.io/material/doc_caffe_example_1hellocaffe.html#소스파일)을 보세요.)

```
cmake_minimum_required(VERSION 2.8.8)

project (ex_blob)

find_package(Caffe)
include_directories(${Caffe_INCLUDE_DIRS})
add_definitions(${Caffe_DEFINITIONS})

add_executable(ex_blob ex_blob.cpp)
target_link_libraries(ex_blob ${Caffe_LIBRARIES})

add_executable(ex_math ex_math.cpp)
target_link_libraries(ex_math ${Caffe_LIBRARIES})
```

척 보시면 아시겠죠? `add_executable()`와 `target_link_libraries()`를 그냥 두 번 써주면 됩니다. 참 쉽죠? 

먼저 첫 번째 `ex_blob.cpp` 파일에서는 blob의 초기화, 재형상화, 난수값 할당, 연산 등을 소개하고요. 특히 CPU에서 또는 GPU에서 선택하여 연산하는 방법과, 실행할 때의 계산 속도 차이를 한번 살펴보도록 하겠습니다. 

두 번째 `ex_math.cpp` 파일에서는 Caffe가 지원하는 blob에서의 연산 종류가 무엇이 있는지 알아보고, 그 중에 몇 가지는 한번 테스트 해보도록 하죠.. 

## blob 초기화 및 데이터 할당
여러분들의 [`ex_blob.cpp`](https://github.com/DeepLearningStudy/caffe/blob/master/examples/ex2_blob/ex_blob.cpp)파일을 한번 열어보세요. 헤더파일 및 선언부는 특별한건 없지만, 나중에 계산 속도를 측정해 보기 위해서 `COMPTIME()` 하나 만들어 봤어요. 

```
clock_t tStart, tEnd;
#define COMPTIME(X)          \
cout << "CompTime of "<< (X) <<": " << (double)(tEnd-tStart)/CLOCKS_PER_SEC<<endl;
```

그럼 `main()`함수 안에 있는 초기화 부분을 먼저 보도록 합시다. 

```
Blob<Dtype>* const blob = new Blob<Dtype>(20, 30, 40, 50);
```

이 부분은 첫 번째 예제와 같이 n=20, c=30, h=40, w=50 인 blob을 생성했습니다. 한번 생성된 blob은 다음 보시는 것처럼 크기를 재형상화(reshaping)할 수 있습니다.

```
blob->Reshape(50, 40, 30, 20);
```

이것은 나중에 기존에 만들어진 네트워크 모델에다가 새로운 데이터를 넣어서 prediction을 하거나, fine tuning 할 때 유용하게 사용할 수 있으니 잘 봐두세요.

그 다음에는 `UniformFiller`로 균일분포에서 셈플링한 난수를 blob에 채워봅시다.

```
FillerParameter filler_param;
filler_param.set_min(-3);
filler_param.set_max(3);
UniformFiller<Dtype> filler(filler_param);
filler.Fill(blob);
```

`UniformFiller`외에도 `Filler`에는 `ConstantFiller`, `GaussianFiller`, `PositiveUnitballFiller`, `XavierFiller` 등이 있습니다. 이름만 보셔도 감이 오시죠? 자세한 정보는 [`Filler.hpp`](https://github.com/BVLC/caffe/blob/master/include/caffe/filler.hpp)를 보세요. (Filler만 다루는 페이지를 하나 만들어야겠습니다.)

데이터로 채워진 blob 값을 보고 싶으시죠? 아니면 Filler를 쓰지 않고 각 셀단위로 데이터를 저장하고 싶으세요? 그건 dataset을 다루는 [다음 예제](https://github.com/DeepLearningStudy/caffe/tree/master/examples/ex3_dataset)에서 같이 알아보도록 하겠습니다.

## CPU/GPU blob 연산
이번에는 blob 안에 있는 데이터의 연산이 어떻게 이루어지는지 알아보도록 하겠습니다. blob은 여러가지 데이터 연산함수들을 제공하는데요. 자세한건 `ex_math.cpp`에서 다루고, 여기서는 `sumsq_data()` 함수 (sum of squares, 제곱합)를 예로 들어서 blob의 연산 메커니즘을 설명하도록 하겠습니다.

먼저 `sumsq_data()`함수가 무얼 하는 함수인지 아래 코드를 볼까요?

```
Dtype expected_sumsq = 0;
const Dtype* data = blob->cpu_data();
for (int i = 0; i < blob->count(); ++i) {
   expected_sumsq += data[i] * data[i];
}
cout<<endl;
cout<<"expected sumsq of blob: "<<expected_sumsq<<endl;
tStart = clock();
cout<<"sumsq of blob on cpu: "<<blob->sumsq_data()<<endl;
tEnd = clock();
COMPTIME("sumsq of blob on cpu");
```

수동으로 제곱합을 계산한 값과 `sumsq_data()` 함수를 이용한 값을 비교해 보았습니다. 같아야겠지요? 나중에 실행결과를 직접 확인해 보시구요. 여기서 눈여겨 봐야할 것은 특별한것(?) 없이 `blob->sumsq_data()`함수를 한번 콜 하면 이 연산은 CPU 위에서 이루어진다는 점입니다. 계산 시간은 제 컴퓨터로 0.001164초 걸리는군요. 

그럼 이번에는 이 연산을 GPU 위에서 해 볼까요? 그 아래 코드를 한번 봅시다.

```
tStart = clock();
blob->gpu_data(); // memcopy host to device (to_gpu() in syncedmem.cpp)
tEnd = clock();
COMPTIME("cpu->gpu time");

tStart = clock();
cout<<"sumsq of blob on gpu: "<<blob->sumsq_data()<<endl;
tEnd = clock();
COMPTIME("sumsq on gpu time");
```

계산시간을 측정하는 부분을 빼면 코드가 딱 두 줄 인데요. 첫 번째로 `blob->gpu_data();` 이 명령은 host (CPU)에 있는 메모리를 GPU 메모리로 카피하라는 명령입니다. 옆에 주석에 적어놓은것 처럼, [`syncedmem.cpp`](https://github.com/BVLC/caffe/blob/master/src/caffe/syncedmem.cpp)에서 이 명령을 수행하는데요. 한번 따라가 볼까요? 

```
const void* SyncedMemory::gpu_data() {
#ifndef CPU_ONLY
to_gpu();
return (const void*)gpu_ptr_;
#else
NO_GPU;
#endif
}

```

`gpu_data()`를 콜 하면, 여기에 `to_gpu()' 명령이 있고, 이 명령 안에는 `caffe_gpu_memset(size_, 0, gpu_ptr_);`와 `caffe_gpu_memcpy(size_, cpu_ptr_, gpu_ptr_);`가 있습니다. CUDA가 익숙하신 분들은 GPU에 메모리를 할당하고 host->device 데이터 복사하는 작업임을 알 수 있습니다. 

자, 그런데 문제는 데이터양이 크다면, 이 복사하는 작업도 만만치 않게 계산시간이 걸리다는 점이죠. 얼마나 걸리는지 `cpu->gpu time` 결과를 한번 볼까요? 

제 컴퓨터에서는 0.004674초가 걸렸습니다. 앗, 이거슨 위에서 CPU위에서 제곱합을 연산한 시간보다 자그마치 4배나 더 걸리는 시간이군요? 그럼 그 다음에 GPU위에서 제곱합을 연산한 시간은 0.000229초이네요. 확실히 연산 시간만 보면 GPU에서 연산한 것이 CPU보다는 5배정도 빠르지만 데이터 복사 시간에 훨씬 많은 리소스가 필요하군요.

## CPU/GPU 동기화
눈치채셨겠지만, blob연산의 특징은 CPU에서든 GPU에서든 명령어는 같다는 점입니다. 위에서 `sumsq_data()`는 동일하게 사용되는데, 단지 `gpu_data()`를 하고 나면 CPU데이터가 GPU로 복사가 되고, GPU에 데이터가 있으면 연산 명령은 자동으로 GPU에서 수행하게 되는 것이죠. 

그럼, 실수로 GPU에 데이터가 있는데도 `gpu_data()`를 여러번 호출하면 이 무거운 명령을 여러번 수행하게 될까요? blob은 똑똑하게도(당연하게도) GPU 데이터와 CPU 데이터의 갱신여부를 동기화해서 최신의 데이터를 사용하게 됩니다. 즉, GPU의 데이터가 최신이라면 `gpu_data()`를 호출해도, CPU에서 데이터를 가져오지 않고, 반대로 CPU 데이터가 최신이라면 복사를 수행하게 됩니다. 반대로 `cpu_data()`를 호출해도 마찬가지구요. 아래 코드로 한번 확인해 볼까요? 

```
cout<<endl;
tStart = clock();
blob->gpu_data();   // no data copy since both have up-to-date contents.
tEnd = clock();
COMPTIME("cpu->gpu time");
```

자, 위에 이어서 첫 번째 호출된 `gpu_data()`는 아무 동작을 하지 않습니다. 즉 계산 시간을 확인해보면 1e-06초 의미 없죠. 

그럼 이번에는 GPU위의 데이터에 변화를 주어 봅시다. 

```
// gpu data manipulation
const Dtype kDataScaleFactor = 2;
blob->scale_data(kDataScaleFactor); // change data on gpu
```

`scale_data()`는 blob안에 각 데이터에다가 곱하기를 하는 것이겠죠? 그러면 현재 GPU상의 데이터는 두 배씩 커졌는데, CPU상의 데이터는 예전 그대로 입니다. 여기서 GPU데이터를 CPU로 복사해봅시다.

```
tStart = clock();
blob->cpu_data();   // memcopy device to host (to_cpu() in syncedmem.cpp)
tEnd = clock();
COMPTIME("gpu->cpu time");
```

`cpu_data()` 역시 현재 GPU 메모리 값이 CPU 메모리보다 최신이면 복사를 수행하고, 아니면 수행하지 않습니다. 여기서는 복사가 되었겠죠? 계산 시간을 살펴보면 0.001744초로 아까 CPU에서 GPU로 복사할 때 보다는 2.5배정도 빠르군요. (이유는 묻지 마세요. 그냥 사실 확인만 ㅎㅎ)

그럼 마지막으로, 여기서 `sumsq_data()`를 호출하면 어디서 계산이 될까요? 현재 CPU로 데이터를 복사한 상태로 GPU값과 CPU값이 동일하니, 이 작업은 GPU에서 이루어집니다. 계산 시간을 확인해보면 알 수 있죠.

```
tStart = clock();
cout<<"sumsq of blob on gpu: "<<blob->sumsq_data()<<endl;   // this is done on gpu
tEnd = clock();
COMPTIME("sumsq on gpu time");
```

결과는 0.000169초로 아까 CPU에서 계산한 시간 0.001164과 GPU에서 계산한 시간 0.000229 중에 후자와 비슷하다는 것을 확인할 수 있습니다. 

## Blob 연산
이번에는 앞에서 알아본 `sumsq_data()` 제곱합 외에 다른 blob에서 가능한 연산을 알아보도록 하겠습니다. 여기서는 `mutable_gpu_data()` 라는 개념이 나오니 잘 봐주세요. 

`ex_math.cpp` 파일을 한번 열어보세요. 이번에는 blob을 생성한 후에 다른 방법으로 난수값을 할당해 보겠습니다.

```
Blob<Dtype>* blob_in = new Blob<Dtype>(20, 30, 40, 50);
Blob<Dtype>* blob_out = new Blob<Dtype>(20, 30, 40, 50);
int n = blob_in->count();

// random number generation
caffe_gpu_rng_uniform<Dtype>(n, -3, 3, blob_in->mutable_gpu_data());
```

여기서 `caffe_gpu_rng_uniform<type>()` 함수가 사용되었습니다. 그리고 특이한 점은 사용된 데이터의 주소값으로 `blob_in->mutable_gpu_data()`을 사용했는데요. CPU든 GPU든 blob의 데이터를 접근할 때, 데이터를 쓸 수 있게 하려면 앞에 `mutable`을 붙여야 합니다. 안붙이면 읽기전용이죠. 아마 Caffe에서 주소를 통한 데이터 직접 접근은 허용하되, 혹시 잘못 쓸까봐 보호 차원에서 쓰는 데이터는 더 귀찮게 해놓은것 같습니다. 

뒤에 보실 연산함수들도 모두 `caffe_gpu_`로 시작하는데요. 이 함수들은 util 안에 있는 [math_functions.hpp](https://github.com/BVLC/caffe/blob/master/include/caffe/util/math_functions.hpp)안에 정의되어 있습니다. 함수 구현은 CPU 연산은 [math_functions.cpp](https://github.com/BVLC/caffe/blob/master/src/caffe/util/math_functions.cpp), GPU 연산은 [math_functions.cu](https://github.com/BVLC/caffe/blob/master/src/caffe/util/math_functions.cu) 여기서 자세히 보실 수 있습니다. 

여기서는 GPU 연산만 해보도록 하겠습니다. 첫 번째로 절대갑합을 구해보죠. 

```
Dtype asum;
caffe_gpu_asum<Dtype>(n, blob_in->gpu_data(), &asum);
cout<<"asum: "<<asum<<endl;
```

결과는 1.79959e+06으로 뭐 의미가 있는 수 같네요. ㅎㅎ

그리고 아래처럼 다른 함수들도 한번 테스트 해보세요.

```
caffe_gpu_sign<Dtype>(n, blob_in->gpu_data(), blob_out->mutable_gpu_data());
caffe_gpu_sgnbit<Dtype>(n, blob_in->gpu_data(), blob_out->mutable_gpu_data());
caffe_gpu_abs<Dtype>(n, blob_in->gpu_data(), blob_out->mutable_gpu_data());
caffe_gpu_scale<Dtype>(n, 10, blob_in->gpu_data(), blob_out->mutable_gpu_data());
```

여기서는 `blob_in`은 읽기, `blob_out`은 쓰기 전용입니다. 

다음 예제에서는 난수말고, 의미있는 데이터셋에서 데이터를 읽어서 blob에 저장하고, 이 값들을 접근해서 시각화까지 한번 해보도록 하겠습니다. 
