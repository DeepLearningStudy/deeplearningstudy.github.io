---
title: Installation Windows 7 64-bit
tags: [caffe]
keywords: start, introduction, begin, install, build, hello world,
last_updated: August 12, 2015
---
By [HotaekHan](https://www.facebook.com/hotack.han?fref=pb_other) on 2015.05.27

우선 [WinCaffe](https://github.com/niuzhiheng/caffe)
해당 프로젝트 설명에서 나오는 설명대로 진행하면 대부분 빌드가 되시리라 생각합니다.
저 또한 그냥 나와있는데로 차근차근 몇번(?)하다 보니 되었습니다.
wiki page 작성이 처음이라, 편집이 조금 부족할 수 있음을 양해드립니다.

아래에 언급되는 WinCaffe는 [WinCaffe](https://github.com/niuzhiheng/caffe)
해당 프로젝트임을 알려드립니다.

1. 요구사항

Windows 64-bit

MS Visual Studio 2012

CUDA toolkit 6.5

Other dependencies


요구사항이 개인적인 생각으론 조금 까다롭습니다.
저는 Win 7 64bit에서 진행하였고 VS2012는 일단 평가판을 받아서 사용하였습니다.
CUDA toolkit 또한 최근 버전이 아닌 6.5를 설치하셔야 되며(for 64bit), 
그 외의 다른 연관파일은 WinCaffe페이지 중 Prerequisites항목에서 다운받으시면 될거같습니다.

 모든 환경을 다 갖추셨으면 우선 VS2012를 실행시켜봅니다. 만약 호환성문제가 발생한다면
[VS 2012 update](http://www.microsoft.com/en-us/download/details.aspx?id=36020)를 설치한 언어에 맞게
다운받으셔서 설치를 하고 다시 VS 2012를 실행하여 호환성 문제가 발생하지 않음을 확인합니다.

2. 빌드

  1) WinCaffe프로젝트 페이지에서 받은 dependencies파일들의 압축을 풀어서 WinCaffe폴더에 덮어씁니다.

  2) WinCaffe의 디렉토리중 build/MSVC 폴더 아래에 MainBuilder.sln(solution)파일을 실행

  3) 프로젝트가 정상적으로 로드됨을 확인하고, 타겟 플랫폼을 x64로 변경

  4-1) 프로젝트의 빌드가 상~~당히 오래걸립니다. 그냥 F7을 눌러서 빌드하셔도 되지만,

  4-2) 빌드->솔루션 정리 이후 F7을 눌러 빌드하시길 권장드립니다.(사실 근데 둘다 오래걸려요..)

  5) 빌드가 다 되었음을 확인하고 기뻐합니다.


3. HelloCaffe 실행


  1) 우선 저희 study 프로젝트를 다운 후 examples/ex1_hellocaffe/main.cpp를 WinCaffe/tools 폴더로 복사

  2) VS2012에서 MainCaller.cpp를 찾은 후 열어보시면 이미 주석으로 tools 폴더 아래에 다양한 .cpp파일들을 include한 것을 확인할 수 있음. 이와 동일하게 방금 복사한 main.cpp의 경로를 include시킴

  3) asum_data()라는 함수가 없다는 에러가 발생. 이를 주석처리하고 실행시키면 정상적으로 실행되는 것을 확인할 수 있습니다.

끝!

감사합니다 :)
