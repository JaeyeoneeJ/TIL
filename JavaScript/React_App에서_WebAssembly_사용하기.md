- 해당 테스트는 [WebAssembly란](./WebAssembly란.md) 문서를 실제로 인앱에서 구현해보고 테스트해보기 위한 자료입니다.
## 1. 테스트 목표
- 앞선 지식을 토대로 실제로 React 앱에서 WebAssembly를 적용할 수 있는지 테스트 해보자.
- 이번 테스트에서 궁극적으로 목표하는 바는 다음과 같다.

1. WebAssembly로 변환할 수 있는 저수준 언어(C/C++, Rust 등)를 작성한다.
2. 저수준 언어로 작성한 파일을 WebAssembly 코드로 컴파일 한다.
3. React App에서 WebAssembly 파일을 가져와 사용한다.
4. Web App에서 정상적으로 출력되는지 확인한다.

- 테스트를 하기에 앞서 우선 간단한 React App과 WebAssembly로 컴파일할 수 있는 도구가 필요하다.
- 이번 테스트에서는 Emscripten SDK를 활용하고자 한다.

> [Emscripten SDK 설치 방법](./Emscripten_설치.md)

## 2. React App 설치 및 빌드
- 일반적으로 CRA(Create-React-App)를 사용하여 설치하고 있고, 최신 버전의 CRA는 wasm 형식의 파일을 지원한다.
- 하지만, 더욱 심도 깊게 설정을 변경하기 위해 eject 등으로 웹팩 설정을 직접 꺼내어 사용하는 케이스도 종종 발생한다.
- 이번 테스트에서는 그 어떠한 경우에도 CRA 상태를 유지하여 웹팩을 꺼내지 않고 사용하여 WebAssembly를 가져오는 방법을 알아보겠다.

### CRA 생성
- 단순히 테스트를 하기 위함이므로 간단하게 CRA로 리액트 앱을 생성한다.
```bash
npx create-react-app wasm-test
```

### C 작성
- WebAssembly를 작성하기에 앞서 여러 옵션 중 한 가지를 결정해야 한다.
- 일회성 wasm 모듈을 작성하는 가장 인기 있는 옵션은, `C, Rust, AssemblyScript`이며 각각의 옵션에는 장단점이 있다.

1. C: 오랫동안 사용되어 온 언어로, 탁월한 성능과 거대한 커뮤니티, 훌륭한 도구를 갖추고 있지만 JS/TS에서 전환하는데 러닝 커브가 높음
2. Rust: 훌륭한 컴파일러와 메모리 안정성, wasm을 앱에 통합하기 쉽게 만드는 도구 체인을 갖추고 있으나 C와 마찬가지로 러닝 커브가 높음
3. AssemblyScript: TypeScript의 하위 집합으로, JavaScript를 개발해 본 사람으로서는 어느정도 익숙하고 경험치를 가지고 있음. 또한 wasm으로 컴파일하기 위한 목적으로 만들어진 언어임

- 이번 테스트의 궁극적인 목적은 브라우저 엔진에서 C를 컴파일하여 실행할 때와 일반 JS를 실행할 때의 차이가 얼마나 발생하는지 이므로 이에 가장 효과적인 C로 결정하겠다.

- C 로 간단한 덧셈 기능을 만들어보자.
- 프로젝트 root에서 src/add.c를 만들고 아래 내용을 작성한다.
```c
#include <emscripten/emscripten.h>
#include <stdlib.h>

EMSCRIPTEN_KEEPALIVE int add(int a, int b)
{
return a + b;
}
```
- 위 코드에서 특히 함수 시작부분에 있는 `EMSCRIPTEN_KEEPALIVE` 텍스트를 주목해보자.
- 해당 텍스트가 없으면 Emscripten 컴파일러는 이를 데드 코드로 간주하여 제거한다.

### Makefile 생성 및 빌드
1. Not npm
- CRA로 앱을 만들었다면 당연히 npm으로 앱을 빌드하겠지만, 일반 스크립트의 옵션이 어떻게 짜여지는지를 확인하기 위해 Makefile을 만들어 확인해보자.
- 프로젝트 root에 “Makefile”이라는 파일을 만들고 아래 내용을 복사하여 붙여넣는다.
```bash
src/add.mjs: src/add.c
emcc --no-entry src/add.c -o src/add.mjs \
-s ENVIRONMENT='web' \
-s SINGLE_FILE=1 \
-s EXPORT_NAME='createModule' \
-s USE_ES6_IMPORT_META=0 \
-s EXPORTED_FUNCTIONS='["_add", "_malloc", "_free"]' \
-s EXPORTED_RUNTIME_METHODS='["ccall", "cwrap"]' \
-O3
```

- 이 Makefile은 C 코드를 WebAssembly로 컴파일 한다. 여기에서는 `src/add.c`를 `src/add.mjs`로 변환한다.
- 여기서 `emcc`는 emsdk의 도구이다. 빠른 상태 점검을 수행하고 실행할 때 확인한다.
- `--no-entry`: 진입점(즉, main())이 없다는 의미
- `ENVIRONMENT='web'`: 컴파일된 WebAssembly를 웹 작업자, Node.js, webview와 같은 환경에서 실행하는 것이 가능하지만 여기서는 web만 사용
- `SINGLE_FILE=1`: 이렇게 하면 불필요한 `add.wasm` 파일이 아닌 `add.mjs` 파일만 생성됨. `.mjs` 파일 대신 `.wasm` 바이너리를 사용할 수 있으며, 바이너리 버전을 사용하면 메모리 사용량 이점이 있으나, 이 테스트에서는 CRA 꺼내기(`eject`)를 방지하므로 `.mjs`로 진행하겠음

> `.mjs` 확장자는 JavaScript 모듈을 나타내며, 이는 단일 파일에 웹어셈블리 코드와 관련된 JavaScript 코드가 함께 포함되어 있음을 의미한다.
> 
> 즉,  `.mjs` 파일은 단일 파일에 여러 유형의 코드를 포함할 수 있는데, 여기에는 JavaScript 및 웹어셈블리 코드가 모두 포함된다.
> 
> 여기에서는 SINGLE_FILE 옵션을 사용하여 이렇게 하나의 파일에 모든 것을 포함하면서 웹 어플리케이션을 보다 쉽게 배포할 수 있고, 다양한 환경에서 실행할 때, 추가적인 파일 관리를 최소화할 수 있다.

- `EXPORT_NAME='createModule'`: 어떤 이유로든 Emscripten은 기본적으로 Module 인스턴스를 얻기 위해 호출하는 Module이라는 함수를 생성함. 혼란스럽기 때문에 "createModule"이라고 부르면 모듈 인스턴스가 아니라 호출 가능한 함수라는 것이 더 명확해지므로 `createModule`이라고 명명하였음
- `USE_ES6_IMPORT_META=0`: 잘 짜여진 앱에서는 이 값을 1로 둘 수 있으며, 이 경우, wasm 모듈 경로를 자동으로 감지할 수 있음. 하지만 이번 리액트 앱은 `import.meta.url`을 찾을 수 없으므로 `0`으로 두겠음
- `EXPORTED_FUNCTIONS='["_add", "_malloc", "_free"]`: 내보낼 함수를 선언하는 파트이며, `_add`는 우리가 만든 함수임. `_malloc`과 `_free`는 add.mjs 코드에 필요한 내부함수임

> 일반적으로 `_(언더스코어)`는 C/C++의 네이밍 컨벤션 중 하나로, 외부에서 사용되는 함수와 내부에서 사용되는 함수를 구분하기 위해 사용되는 편이다.
> 
> Emscripten에서는 이러한 컨벤션을 따르며, 이를 사용하는 것이 규칙이다. 이것은 코드의 일관성을 유지하고 웹어셈블리 모듈을 올바르게 사용하기 위해 권장되는 방법이다.
> 
> malloc: 메모리 할당 함수로, 특정 크기의 메모리 블록을 동적으로 할당한다. 할당된 메모리는 초기화되지 않으며, 할당된 메모리 블록의 포인터가 반환된다.
> `void* malloc(size_t size)`
> 
> free: 메모리 해제 함수로, `malloc`이나 유사한 함수로 할당된 메모리를 해제한다. 이 함수는 더 이상 필요하지 않은 메모리를 운영체제에 반환한다.
> `void free(void* ptr)`
> 
> 이러한 함수들은 메모리를 효율적으로 사용하고 해제함으로써 메모리 누수를 방지하고 프로그램 성능을 향상시키는데 사용된다.

- `EXPORTED_RUNTIME_METHODS='["ccall", "cwrap"]'`: 이름에서 알 수 있듯이 런타임에 실행할 수 있도록 내보내려는 함수. `ccall`을 사용하면 WebAssembly 함수를 즉시 호출할 수 있는 반면, `cwrap`을 사용하면 먼저 함수를 메모리에 저장한 후 원하는 만큼 여러번 호출할 수 있음
- `-03`: 이 플래그는 WebAssembly 코드를 최적화하는 방법을 선언한다. 만일 컴파일 시간이 길어지고 코드 크기가 커질 수 있다는 점을 감안한다면 가장 최적화된 옵션임

```bash
make
```
- 위 명령어를 실행했을 때 `src/add.mjs`가 보인다면 성공한 것이다.

2. Package.json에서 스크립트 파일 수정하기(npm CRA 기준)
- 앞에서 makefile을 통해 정리하였으므로, 빌드 스크립트에서 실행하는 방법을 설명한다.
- 우선 root 폴더의 package.json 파일의 빌드 스크립트 파일을 아래 내용으로 변경한다.
```bash
"scripts": {
    "start": "react-scripts start",
    "build:react": "react-scripts build",
    "build:wasm": "emcc --no-entry src/add.c -o src/add.mjs -s ENVIRONMENT='web' -s SINGLE_FILE=1 -s EXPORT_NAME='createModule' -s USE_ES6_IMPORT_META=0 -s EXPORTED_FUNCTIONS='[_add, _malloc, _free]' -s EXPORTED_RUNTIME_METHODS='[ccall, cwrap]' -O3",
    "build": "npm run build:wasm && npm run build:react"
  },
```
- 여기서 주의 깊게 볼 내용은 `build:wasm` 이다.
- npm으로 install한 React 프로젝트의 경우, make 메서드가 동작하지 않을 수 있으므로, npm에서 실행할 수 있도록 스크립트를 작성하여 넣었다.
```bash
npm run build
```
- 명령어를 입력 후, 정상적으로 실행되었다면 `src/add.mjs`가 보일 것이다.


## 3. React 코드 작성
- App.js 컴포넌트를 아래와 같이 작성한다.
```jsx
import { useEffect, useRef, useState } from "react";
import createModule from "./add.mjs";

function App() {
  // 상태 및 리프 변수 정의
  const [add, setAdd] = useState();
  // useEffect를 사용하여 웹어셈블리 모듈 비동기적으로 불러오기
  useEffect(() => {
    createModule().then((Module) => {
      // 웹어셈블리 모듈에서 'add'함수를 가져와서 상태 업데이트
      setAdd(() => Module.cwrap("add", "number", ["number", "number"]));
    });
  }, []);

// 웹어셈블리 모듈이 로드되지 않았을 때 로딩 메세지 표시
  if (!add) {
    return "Loading webassembly...";
  }

// 결과 표시
  return (
    <div className="App">
      <p>Let's do some basic addition:</p>
      <div>123 + 234 = {add(123, 234)}</div>
    </div>
  );
}

export default App;
```

- useEffect 쪽 함수를 살펴보면 createModule 함수를 호출(makefile 또는 빌드 스크립트에서 EXPORT_NAME으로 선언한 함수)하고 promise로 비동기 처리한다.
- 비동기로 데이터를 정상적으로 가져오면 runtime 기능을 갖춘 모듈이 된다.
- 이 코드에서는 useState 훅을 사용하여 state를 관리하고, state를 업데이트하는 함수를 설정했다.
- `Module.cwrap("add", "number", ["number", "number"])`: 이 부분은 Emscripten에서 생성된 웹어셈블리 모듈인 `Module`에서 `add`라는 함수를 가져오는 역할을 한다. `cwrap`은 C 함수를 JavaScript로 래핑하는 역할을 한다. `cwrap` 함수는 첫 번째 매개변수로 함수 이름을 받고, 두 번째 매개변수로 함수의 반환 타입을, 세 번째 매개변수로 함수의 매개변수 타입들을 배열로 받는다.
- `() => Module.cwrap("add", "number", ["number", "number"])`: 이 부분은 화살표 함수를 사용하여 위에서 정의한 `cwrap` 함수를 반환하는 함수로 이 함수는 `setAdd`에 전달된다.

> `setAdd(() => Module.cwrap("add", "number", ["number", "number"]))`: `setAdd`는 React의 `useState` 훅에서 반환한 state를 업데이트하는 함수로 여기에서는 해당 state를 함수로 업데이트하고 있다. 이때 전달된 함수는 이전 state 값을 받아서 새로운 state 값을 반환하는 역할을 한다.
> 
> 따라서 위 코드는 `add`라는 state 변수를 설정하는데, 이 state 변수는 emscripten 모듈의 “add”(makefile 또는 빌드 스크립트에서 EXPORTED_FUNTIONS로 설정한 함수)를 래핑한 함수가 된다. 이후에 `add`를 호출하면 emscripten 모듈의 `add` 함수가 실행되는 것이기 때문에 React 앱에서 웹어셈블리 기능을 사용할 수 있게 된다.

  - 따라서 `<div>123 + 234 = {add(123, 234)}</div>` 와 같이 state 변수가 함수처럼 동작할 수 있게 된다.


## 4. Web App 출력
- 아래 그림과 같이 자바스크립트가 아닌 C 로 작성한 코드가 WebAssembly로 컴파일되어 앱에서 정상적으로 표시되는 것을 확인할 수 있다.
<img src="https://github.com/JaeyeoneeJ/TIL/assets/77138259/222e1a2f-33e1-406c-bc1a-ad3d47e69b36" alt="WebApp 결과" />
