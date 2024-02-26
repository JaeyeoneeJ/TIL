- 해당 테스트는 [React App에서 WebAssembly 사용하기](./React_App에서_WebAssembly_사용하기.md) 및 [브라우저에서_일반_자바스크립트와_웹어셈블리_속도_비교](./브라우저에서_일반_자바스크립트와_웹어셈블리_속도_비교.md) 테스트의 고도화로 인앱 브라우저에서 일반 자바스크립트 코드와 C를 WebAssembly로 컴파일한 코드의 런타임 속도 차이를 확인하기 위함을 목적으로 합니다.
- 기존 글에서는 C를 WebAssembly로 변환하며 mjs(모듈 자바스크립트)로 변환하였지만, 이번에는 wasm 파일로 변환하여 실제 두 변환 방법의 차이를 확인하는 것을 목적으로 합니다.
- 만일 WebAssembly를 React에서 적용하는 방법에 대해 궁금하다면 위 링크를 참조해 주세요.

## 1. 테스트 목표
- 앞선 테스트에서 우리는 React 앱에서 C로 작성한 코드를 WebAssembly 모듈을 로드하기 위해 mjs 형태로 변환하여 적용할 수 있다는 것을 확인했다. 따라서 이러한 궁금증이 생기게 되었다.
- 하지만 실제 로드 내용은 mjs인 것이므로 이미 C의 변환 로드는 mjs로 변환된 시점에 완료되었다는 것을 알게 되었다.
- 즉, mjs로 변환된 파일은 불필요하게 복잡한 코드 레벨을 압축하여 낮춰준 것일 뿐, 바이너리 타입의 어셈블리 언어는 아니라는 결론이 나온다.
- 그렇다면, "실제 wasm 파일을 React에 직접 로드하려면 어떻게 해야하는가?"

- 이번 테스트에서 궁극적으로 목표하는 바는 다음과 같다.

1. WebAssembly로 변환할 수 있는 저수준 언어(C/C++, Rust 등)를 작성한다.
2. 저수준 언어로 작성한 파일을 WebAssembly 코드로 컴파일 한다.(mjs, wasm)
3. React App에서 JavaScript와 C를 WebAssembly로 컴파일한 모듈(mjs, wasm)의 실행 
4. WebAssembly 파일을 가져와 사용한다.
5. Web App에서 일반 JavaScript와 mjs, wasm으로 변환한 모듈의 속도 차이를 확인한다.

- Emscripten SDK를 활용하여 컴파일을 진행하겠다.

## 2. C 작성
- C 로 간단한 배열을 만들어 사용해보자.
- 기존에 만들었던 `src/add.c` 와 `src/array.c`를 그대로 사용하고자 한다.
- 현재까지 만든 C 를 명시적으로 관리하기 위해 기존 `src` 폴더를 정리한다.
```
├── App.js
├── index.js
└── wasm
    ├── add.c
    └── array.c
```

- 위처럼 `wasm` 폴더에 파일을 넣었다면 아래와 같은 코드가 있을 것이다.
```c
// src/wasm/add.c
#include <emscripten/emscripten.h>
#include <stdlib.h>

EMSCRIPTEN_KEEPALIVE int add(int a, int b)
{
	return a + b;
}
```

```c
// src/wasm/array.c
#include <emscripten/emscripten.h>
#include <stdio.h>
#include <stdlib.h>

typedef struct {
	int* data;
	int size;
	int a;
	int b;
} IntArray;

EMSCRIPTEN_KEEPALIVE int* array(int size, int a, int b) {
	// 메모리 할당
	int* result = (int*)malloc(size * sizeof(int)); // 배열 크기 정보를 위한 추가 공간 확보
	
	if(result == NULL) {
		fprintf(stderr, "메모리 할당에 실패했습니다.\n");
		exit(1);
	}
	
	for (int i = 0; i < size; i++) {
		result[i] = a * b * i;
	}
	
	return result;
}
```

## 3. build script 작성
- 프로젝트 root의 package.json 파일에서 기존에 작성했던 빌드 스크립트를 아래와 같이 변경해준다.
```json
// package.json
  "scripts": {
    ...
    "build:wasm-mjs": "emcc --no-entry src/wasm/*.c -o src/wasm/wasmModule.mjs -s ENVIRONMENT='web' -s SINGLE_FILE=1 -s EXPORT_NAME='createModule' -s USE_ES6_IMPORT_META=0 -s EXPORTED_FUNCTIONS='[_add, _array, _malloc, _free]' -s EXPORTED_RUNTIME_METHODS='[ccall, cwrap]' -O3 -s MODULARIZE=1",
    "build:wasm": "emcc --no-entry src/wasm/*.c -o src/wasm/wasmModule.wasm -s ENVIRONMENT='web' -s SINGLE_FILE=1 -s EXPORT_NAME='createModule' -s USE_ES6_IMPORT_META=0 -s EXPORTED_FUNCTIONS='[_add, _array, _malloc, _free]' -s EXPORTED_RUNTIME_METHODS='[ccall, cwrap]' -O3 -s MODULARIZE=1",
    "build": "npm run build:wasm && build:wasm-mjs && npm run build:react",
    ...
  },
```
- 상기 스크립트를 보면, 기존의 빌드 스크립트가 `build:wasm-mjs`이고, wasm 파일 형태로 변환하는 것이 `build:wasm`인 것을 확인할 수 있다.
- 한 스크립트에 변환 모듈을 같이 작성해도 되지만, `-o` 를 한 스크립트에서 2번 사용하는 것은 권장되지 않는 방법이므로 각 따로 빌드하여 `build` 스크립트에서 묶었다.

```json
// package.json
  "eslintConfig": {
    ...
    "ignorePatterns": [
      "src/wasm/*"
    ]
  },
```
- 또한 폴더 경로를 `src/wasm` 으로 변경하면서 해당 폴더 안의 모든 파일의 eslint 오류를 무시하도록 설정했다.

## 4. App.js 수정
- 기존에 작성했던 App.js를 수정했다.
```jsx
// src/app.js
import React from "react";
import WasmModule from "./components/WasmModule";

const data = { size: 100000, timeset: 10, a: 2, b: 3 };

function App() {
  return (
    <div className="App">
      <h2>Wasm Module Load Test</h2>
      {Object.keys(data).map((key, index) => (
        <div key={index}>
          <span>{key}: </span>
          <span>{data[key]}</span>
        </div>
      ))}
      <WasmModule data={data} />
    </div>
  );
}

export default App;
```
- 기존에 `App.js`에 지저분하게 있던 코드를 `WasmModule`이라는 컴포넌트에 옮겨주고, 해당 부분을 수정할 것이다.
- 또한, JavaScript, mjs, wasm에 같은 인자가 들어갈 수 있도록 외부에서 `data`라는 prop을 생성해서 보내주기로 했다.

## 5. WasmModule.jsx 작성
- 아래는 원본 코드이다.
```jsx
import React, { useEffect, useMemo, useState } from "react";
import wasmModuleFile from "../wasm/wasmModule.wasm";
import createModule from "../wasm/wasmModule.mjs";

const loadWasmModule = async () => {
  try {
    const importObject = {
      wasi_snapshot_preview1: {
        proc_exit: () => {},
        fd_close: () => {},
        fd_write: () => {},
        fd_seek: () => {},
      },
    };

    const response = await fetch(wasmModuleFile);
    const buffer = await response.arrayBuffer();
    const result = await WebAssembly.instantiate(buffer, importObject);

    return result.instance.exports;
  } catch (e) {
    console.warn("loadWasmModule error", e);
  }
};

const WasmModule = ({
  debug = false,
  data = { size: 100000, timeset: 20, a: 20, b: 30 },
}) => {
  const [wasmModule, setWasmModule] = useState();
  const [module, setModule] = useState();
  const [arrayFunc, setArrayFunc] = useState();

  const cToJsTime = useMemo(() => {
    if (arrayFunc) {
      const time = [];
      for (let i = 0; i < data.timeset; i++) {
        const startTime = performance.now();

        const resultPtr = arrayFunc(data.size, data.a, data.b);
        // console.log("jjy resultPtr c", resultPtr);
        const dataArray = new Int32Array(
          module?.HEAP32.buffer,
          resultPtr,
          data.size
        );
        debug && console.log("jjy cToJsTime dataArray", dataArray);

        const endTime = performance.now();
        const duringTime = endTime - startTime;

        time.push(duringTime);
      }

      if (time.length > 0) {
        const sum = time.reduce((acc, value) => acc + value, 0);
        const average = sum / time.length;
        return average;
      }
    }
  }, [arrayFunc, module, data, debug]);

  const cToWasmTime = useMemo(() => {
    if (wasmModule) {
      const time = [];
      const arrayFunc = wasmModule.array;

      for (let i = 0; i < data.timeset; i++) {
        const startTime = performance.now();

        const resultPtr = arrayFunc(data.size, data.a, data.b);
        // console.log("jjy resultPtr c", resultPtr);
        const dataArray = new Int32Array(
          wasmModule.memory.buffer,
          resultPtr,
          data.size
        );
        debug && console.log("jjy cToWasmTime dataArray", dataArray);
        wasmModule.free(resultPtr);

        const endTime = performance.now();
        const duringTime = endTime - startTime;

        time.push(duringTime);
      }

      if (time.length > 0) {
        const sum = time.reduce((acc, value) => acc + value, 0);
        const average = sum / time.length;
        return average;
      }
    }
  }, [wasmModule, data, debug]);

  const jsTime = useMemo(() => {
    const time = [];

    for (let i = 0; i < data.timeset; i++) {
      const startTime = performance.now();
      const obj = [];
      for (let i = 0; i < data.size; i++) {
        obj.push(2 * 3 * i);
      }
      debug && console.log("jjy js dataArray", obj);
      const endTime = performance.now();
      const duringTime = endTime - startTime;
      time.push(duringTime);
    }
    if (time.length > 0) {
      const sum = time.reduce((acc, value) => acc + value, 0);
      const average = sum / time.length;
      return average;
    }
  }, [data, debug]);

  useEffect(() => {
    const initializeWasm = async () => {
      const wasmModule = await loadWasmModule();
      setWasmModule(wasmModule);
    };
    initializeWasm();

    const initializeMjs = async () => {
      const mjsModule = await createModule();
      setModule(mjsModule);
      setArrayFunc(() =>
        mjsModule.cwrap("array", "number", ["number", "number", "number"])
      );
    };
    initializeMjs();
  }, []);

  return (
    <div>
      <h3>JS / WASM Duration time</h3>
      <table className={"table"}>
        <thead>
          <tr>
            <th>TYPE</th>
            <th>VALUE</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>C to JS</td>
            <td>{cToJsTime}</td>
          </tr>
          <tr>
            <td>C to WASM</td>
            <td>{cToWasmTime}</td>
          </tr>
          <tr>
            <td>JS</td>
            <td>{jsTime}</td>
          </tr>
          <tr>
            <td>JS / C to JS</td>
            <td>{jsTime / cToJsTime}</td>
          </tr>
          <tr>
            <td>JS / C to WASM</td>
            <td>{jsTime / cToWasmTime}</td>
          </tr>
        </tbody>
      </table>
    </div>
  );
};

export default WasmModule;
```
- 하나씩 뜯어보자.

### 1) wasm 확장자 모듈 로드
- 사실 상 이번 테스트의 핵심이라고 할 수 있는 부분이다.
```js
import wasmModuleFile from "../wasm/wasmModule.wasm";

const loadWasmModule = async () => {
  try {
    const importObject = {
      wasi_snapshot_preview1: {
        proc_exit: () => {},
        fd_close: () => {},
        fd_write: () => {},
        fd_seek: () => {},
      },
    };

    const response = await fetch(wasmModuleFile);
    const buffer = await response.arrayBuffer();
    const result = await WebAssembly.instantiate(buffer, importObject);

    return result.instance.exports;
  } catch (e) {
    console.warn("loadWasmModule error", e);
  }
};
```
- 우선 `loadWasmModule` 함수의 역할은 웹어셈블리 모듈을 로드하는 작업을 비동기로 수행한다.
- `response`: 외부에서 wasm 파일을 가져와 `fetch` 함수를 사용해 웹어셈블리 파일을 네트워크에서 가져온다.
- `buffer`: 바이너리 데이터를 로드하기 위해 메모리 상에 할당된 `arrayBuffer`가 필요하기 때문에 `response`에서 받아온 데이터를 `arrayBuffer`로 변환한다.(웹어셈블리는 바이너리 형식의 데이터를 다루기 때문)
- `result`: 브라우저 내장 api인 `WebAssembly.instantiate`함수를 사용하여 웹어셈블리 모듈을 메모리에 로드하고 인스턴스화한다. 이 때, 위에서 정의한 `importObject`가 함께 전달되어 모듈이 사용할 외부 기능을 제공한다.
- `return result.instance.exports`: 로드된 웹어셈블리 모듈의 `exports` 객체를 반환한다. 이 객체에는 웹어셈블리 모듈에서 내보낸 함수나 변수 등이 포함되어 있다.

- `importObject`: 웹어셈블리 모듈에서 사용할 `importObject`를 정의한다. 여기에서는 `wasi_snapshot_preview1` 인터페이스에 해당하는 몇 가지 함수를 dummy 함수로 초기화했다. 따라서 웹어셈블리 모듈이 이 import 함수를 사용하게 될 때, 강제로 사용하지 못하게 한 것이다.

> 왜 `importObject`에 dummy 함수를 넣었는가?
- 이번 테스트에서 가장 애를 먹은 부분이기도 하다.
- 웹어셈블리 모듈을 사용하는 환경은 일반적으로 서버 환경이 많기 때문에 클라이언트 환경인 브라우저에서는 케이스를 찾기가 매우 어려웠다.

- 초기 importObejct를 빈 객체(`{}`)를 넣었으나 아래와 같은 에러가 발생했다.
```console
loadWasmModule error TypeError: WebAssembly.instantiate(): Import #0 module="wasi_snapshot_preview1": module is not an object or function
```
- 위 오류는 `WebAssembly.instantiate()` 함수에서 발생한 것으로, 웹어셈블리 모듈을 로드하고 인스턴스화 하는 과정에서 문제가 발생했음을 나타낸다. 
- 에러 메세지에서 `Import #0 module="wasi_snapshot_preview1"`은 `wasi_snapshot_preview1` 모듈을 로드하는데 문제가 발생했음을 나타낸다.
- 즉, WASI 관련 설정이나 모듈이 문제를 일으키는 것으로, WASI 설정을 제공해야 한다.

- WASI (WebAssembly System Interface)는 웹어셈블리 모듈이 외부 환경과 상호 작용할 수 있게 하는 인터페이스를 제공하는 표준이며, 주로 시스템 레벨의 작업을 수행하거나 입출력 작업을 처리하는 데 사용된다.
- WASI는 주로 웹 어셈블리 모듈이 운영체제 수준의 시스템 호출 및 기능을 수행하는데 사용되며, 웹 환경에서는 이러한 기능이 브라우저 API 및 웹 플랫폼 기능을 통해 노출되기 때문에 WASI의 사용이 필요하지 않다. WASI의 기능을 사용하기 위해서는 별도의 WASI SDK를 로드하여 연결해주는 역할이 필요하기 때문이다.
- 우리의 `emcc` 스크립트에는 `--environment='web'` 옵션을 사용하였고, 이는 웹어셈블리 코드가 브라우저 환경에서 실행될 것이라고 선언하였다. 이는 브라우저에서 제공하는 JavaScript API 및 웹 플랫폼의 기능을 사용할 것을 의미한다. 따라서 이 설정을 하면 WASI를 사용하지 않고, 대신 브라우저 환경에서 제공되는 기능을 활용할 수 있게 된다.

- 안타깝게도 `emsdk(emcc)`는 WASI 설정 기능을 완벽히 지원하지 않는다. 따라서 wasm 파일을 로드할 때, WASI 기능을 사용하지 않도록 에러가 발생하는 파트의 사용을 강제적으로 없애는 방법이 필요하다.
- 따라서 우리는 `importObject`에 `wasi_snapshot_preview1`를 더미 함수로 정의함으로써 웹어셈블리 모듈에서 실제 WASI 함수들을 대체하게 하였다.

### 2) wasm 모듈 초기화
```jsx
import createModule from "../wasm/wasmModule.mjs";

const loadWasmModule = async () => {
  ...
}

const WasmModule = ({ ... }) => {
  const [wasmModule, setWasmModule] = useState();
  const [module, setModule] = useState();
  const [arrayFunc, setArrayFunc] = useState();
  
  ...
  
  useEffect(() => {
    const initializeWasm = async () => {
      const wasmModule = await loadWasmModule();
      setWasmModule(wasmModule);
    };
    initializeWasm();

    const initializeMjs = async () => {
      const mjsModule = await createModule();
      setModule(mjsModule);
      setArrayFunc(() =>
        mjsModule.cwrap("array", "number", ["number", "number", "number"])
      );
    };
    initializeMjs();
  }, []);
  ...
}
```
- `useEffect`를 보면 wasm 모듈과 mjs 모듈을 초기화하는 함수를 가지고 있다.
- `initializeWasm`: 위에서 선언한 `loadWasmModule` 함수로 모듈을 가져와 `wasmModule`에 세팅한다.
- `initializeMjs`: 이미 모듈 자바스크립트인 `createModule()`을 비동기로 가져와 `module`에 세팅한다. `arrayFunc`에도 array 함수를 명시적으로 선언하여 넣어준다.
- 이렇게 하면 `WasmModule` 컴포넌트가 로드될 때, 초기 1번만 wasm 모듈을 초기화할 수 있다.




<hr>
## ref.
- https://developer.mozilla.org/en-US/docs/WebAssembly/JavaScript_interface/instantiate_static
- https://emscripten.org/docs/api_reference/emscripten.h.html?highlight=wasi#calling-javascript-from-c-c