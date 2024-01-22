## 1. Emscripten란
- Emscripten SDK(EmSDK)는 웹어셈블리(asm.js 및 WebAssembly)를 생성하는 데 사용되는 도구와 라이브러리를 포함하는 개발 환경으로 Emscripten은 LLVM(컴파일러 및 코드 최적화 도구)을 기반으로 하며, C, C++, 및 다른 언어로 작성된 코드를 웹어셈블리로 변환할 수 있도록 지원한다.

- Emscripten SDK에는 다양한 도구와 라이브러리가 포함되어 있다.
1. **emcc**: Emscripten 컴파일러로 C 및 C++ 코드를 웹어셈블리로 변환한다. 
2. **emmake** 및 **emconfigure**: 일반적인 Makefile 빌드 시스템을 사용하여 프로젝트를 빌드하거나 설정하는 데 사용된다.
3. **nodejs**: Emscripten에서 생성된 웹어셈블리 코드를 실행하기 위한 Node.js 환경이 제공된다. 
4. **Emscripten 명령어 및 옵션들**: 코드를 컴파일하고 빌드하기 위한 다양한 옵션 및 명령어가 포함되어 있다.
5. **Emscripten 라이브러리**: Emscripten에서 생성된 코드에서 사용할 수 있는 여러 라이브러리가 있다.    


## 2. Emscripten SDK 설치
- Emscripten SDK를 설치하면 이러한 도구와 라이브러리를 사용하여 C 및 C++ 코드를 효과적으로 웹어셈블리로 변환할 수 있다. SDK를 설치하려면 Emscripten 공식 웹사이트에서 다운로드 및 설치 지침을 참고하자.
- [Emscipten 공식 페이지](https://emscripten.org/docs/getting_started/downloads.html)
<img src="https://github.com/JaeyeoneeJ/TIL/assets/77138259/749d8554-a9f9-49ec-9f38-75b495416b5b" alt="Emscripten SDK 설치" />
- 페이지에 접속하면 상세한 설치 가이드가 제공되어 있다.

1. 우선 터미널을 열고 설치하고자 하는 위치에 접속 후 아래 명령어를 통해 SDK를 설치한다.
```bash
git clone https://github.com/emscripten-core/emsdk.git
cd emsdk
./emsdk install latest
./emsdk activate latest
source ./emsdk_env.sh
```

> 주의: Windows 사용자는 `source ./emsdk_env.sh` 대신 `emsdk_env.bat`를 실행

2. Emscripten SDK를 설치한 후, 도구를 최신 버전으로 업데이트 한다.
```bash
./emsdk update
# You seem to have bootstrapped Emscripten SDK by cloning from GitHub. In this case, use "git pull" instead of "emsdk update" to update emsdk. (Not doing that automatically in case you have local changes)
```
- git에서 pull 받은 후 update하라는 설명이 뜬다. 우리는 방금 pull 받았기 때문에 현재 버전이 최신 버전이다.

3. 설치 확인
```bash
emcc --version
# emcc (Emscripten gcc/clang-like replacement + linker emulating GNU ld) 3.1.51 (c0c2ca1314672a25699846b4663701bcb6f69cca)
#
# Copyright (C) 2014 the Emscripten authors (see AUTHORS.txt)
#
# This is free and open source software under the MIT license.
#
# There is NO warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```
- 위 명령어는 현재 버전을 출력한다.

> 주의: `command not found: emcc`와 같은 메세지가 표시될 경우, emsdk가 설치된 폴더로 돌아가 다시 `source ./emsdk_env.sh`를 실행해야 한다.
> 이러한 이유는, 터미널에서 실행한 사용자만 접근 가능하기 때문으로, profile 파일을 직접 수정하여 자동으로 처리되도록 변경할 수도 있다.

- 이제 Emscripten SDK를 사용할 준비가 되었다.



<hr>
## ref.
- https://emscripten.org/docs/getting_started/downloads.html