> 아래 테스트 내용은 저의 github에 수록되어 있습니다. 테스트 앱의 확인이 필요한 경우, [lazy-suspense](https://github.com/JaeyeoneeJ/lazy-suspense)를 통해 프로젝트를 확인할 수 있습니다.

## Code Splitting
- 앱이 커지면 번들 사이즈도 커진다. 특히 큰 규모의 서드 파티 라이브러리를 추가할 때, 실수로 앱이 커져서 로드 시간이 길어진다.
- 대부분의 경우, 사용자는 적어도 처음에는 전체 웹 페이지를 보지 못한다.
- 이러한 경우, 이러한 구성 요소를 렌더링하면 응용 프로그램의 성능이 저하될 뿐만 아니라 많은 리소스가 낭비된다.(특히 이미지나 유사한 데이터를 많이 사용하는 컨텐츠가 있는 경우)
- 번들이 거대해지는 것을 방지하기 위한 좋은 해결방법은 번들을 나누는 것이다. Code Splitting은 런타임에 여러 번들을 동적으로 만들고 불러오는 것으로 Webpack, Rollup과 Browserify(factor-bundle) 같은 번들러가 지원하는 기능이다.
- 코드 분할은 레이지 로딩을 통해 앱 사용자에게 획기적인 성능 향상을 할 수 있다. 앱의 코드 양을 줄이지 않고도 사용자가 필요하지 않은 코드를 불러오지 않게 하며, 앱의 초기화 로딩에 필요한 비용을 줄일 수 있다.

## Lazy Loading
- 레이지 로딩은 필요 시점까지 객체의 초기화를 연기시키기 위해 컴퓨터 프로그래밍에 흔히 사용되는 디자인 패턴의 하나로 적절히 사용될 경우 프로그램 운영 차원에서 효율적이다.
```jsx
// before
import OtherComponent from "./OtherComponent"

// after
const OtherComponent = React.lazy(() => import("./OtherComponent"))
```
- React.lazy의 방식으로 import를 진행하게 된다면 MyComponent가 처음으로 렌더링이 되었을 때 OtherComponent를 포함한 번들을 자동으로 불러온다. 그리고 이 함수는 React 컴포넌트를 default export로 가진 모듈 객체가 이행되는 Promise를 반환해야 한다.

```jsx
import React, { Suspense } from 'react';

const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
```
- React.lazy에 있는 lazy 컴포넌트는 Suspense 컴포넌트의 하위에 렌더링되어야 한다. 그리고 Suspense는 lazy 컴포넌트가 로드되길 기다리는 동안 로딩 화면과 같은 예비 컨텐츠를 보여줄 수 있게 해준다.
- Suspense 컴포넌트에서 fallback이라고 하는 prop 내부에 또 다른 컴포넌트가 들어있는 모습을 볼 수 있다. 이 컴포넌트는 해당 컴포넌트가 로드될 때까지 기다리는 동안 렌더링하려는 React 엘리먼트를 받아들일 수 있다.

- 애플리케이션에 코드 분할을 적용하기 가장 좋은 곳은 바로 ‘라우트’를 진행하는 위치에 넣는 것이다.
```jsx
import React, { Suspense, lazy } from 'react';
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';

const Home = lazy(() => import('./routes/Home'));
const About = lazy(() => import('./routes/About'));

const App = () => (
  <Router>
    <Suspense fallback={<div>Loading...</div>}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </Suspense>
  </Router>
);
```

### 1) lazy 적용 이전
- route 단위 lazy를 적용하기 전에 `app.js`이다.
```jsx
// App.js
import { BrowserRouter as Router, Routes, Route } from "react-router-dom";
import Home from "./Routes/Home";
import Test1 from "./Routes/Test1";
import Test2 from "./Routes/Test2";
import Test3 from "./Routes/Test3";

function App() {
  return (
    <Router>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/test1" element={<Test1 />} />
        <Route path="/test2" element={<Test2 />} />
        <Route path="/test3" element={<Test3 />} />
      </Routes>
    </Router>
  );
}

export default App;

```

- `npm run build`를 했을 때의 폴더 구조는 아래와 같다.
```
# build size
File sizes after gzip:

  52.13 kB (-1.08 kB)  build/static/js/main.29132bd6.js
```

```
./build
├── asset-manifest.json
├── favicon.ico
├── index.html
├── logo192.png
├── logo512.png
├── manifest.json
├── robots.txt
└── static
    └── js
        ├── main.29132bd6.js
        ├── main.29132bd6.js.LICENSE.txt
        └── main.29132bd6.js.map
```

- 빌드된 파일을 실행해보기 위해 `npx http-server ./build`를 실행해보았다.
<img src="https://github.com/JaeyeoneeJ/TIL/assets/77138259/b4800389-50e1-49f5-a342-8752bdb843e0" alt="lazy 이전" />
- main.js가 1개 있고, 다른 라우터로 이동하더라도 처음에 불러온 main.js만을 사용하므로 추가 작업이 없다.

### 2) lazy 적용 이후
- route 단위 lazy를 적용한 후의 `app.js`이다.
```jsx
// App.js
import { Suspense, lazy } from "react";
import { BrowserRouter as Router, Routes, Route } from "react-router-dom";

const Home = lazy(() => import("./Routes/Home"));
const Test1 = lazy(() => import("./Routes/Test1"));
const Test2 = lazy(() => import("./Routes/Test2"));
const Test3 = lazy(() => import("./Routes/Test3"));

function App() {
  return (
    <Router>
      <Suspense fallback={<div>loading...</div>}>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/test1" element={<Test1 />} />
          <Route path="/test2" element={<Test2 />} />
          <Route path="/test3" element={<Test3 />} />
        </Routes>
      </Suspense>
    </Router>
  );
}

export default App;

```

- `npm run build`를 했을 때의 폴더 구조는 아래와 같다.
```
# build size
File sizes after gzip:

  53.21 kB  build/static/js/main.2106b269.js
  306 B     build/static/js/960.8a213e3e.chunk.js
  303 B     build/static/js/318.6a99f56c.chunk.js
  303 B     build/static/js/456.287a21c5.chunk.js
  302 B     build/static/js/923.b7014cd4.chunk.js
```

```
./build
├── asset-manifest.json
├── favicon.ico
├── index.html
├── logo192.png
├── logo512.png
├── manifest.json
├── robots.txt
└── static
    └── js
        ├── 318.6a99f56c.chunk.js
        ├── 318.6a99f56c.chunk.js.map
        ├── 456.287a21c5.chunk.js
        ├── 456.287a21c5.chunk.js.map
        ├── 923.b7014cd4.chunk.js
        ├── 923.b7014cd4.chunk.js.map
        ├── 960.8a213e3e.chunk.js
        ├── 960.8a213e3e.chunk.js.map
        ├── main.2106b269.js
        ├── main.2106b269.js.LICENSE.txt
        └── main.2106b269.js.map
```
- lazy 전에 main.js만 있던 것에 비해 `chunk.js`가 총 4개 추가 생성된 것을 알 수 있다.
- 실제 용량은 오히려 증가되었는데, 이는 `import { Suspense, lazy } from "react";`의 **import-cost**가 증가되었기 때문이다.
- 만일, 우리의 컴포넌트 크기가 각각 큰 용량을 차지하게 된다면 총 **import-cost**는 현 상태 그대로, `chunk.js`의 크기가 증가하게 될 것이다.

- 빌드된 파일을 실행해보기 위해 `npx http-server ./build`를 실행해보았다.
<img src="https://github.com/JaeyeoneeJ/TIL/assets/77138259/361feef1-1fe4-449d-80ff-6b4df68415bd" alt="lazy 이후" />
- main.js와 chunk.js를 불러온 것을 확인할 수 있다.
- 이는, 앱 공통 영역에서 사용하는 main.js와 Home 컴포넌트에 해당하는 chunk.js를 모두 불러왔기 때문이다.
<img src="https://github.com/JaeyeoneeJ/TIL/assets/77138259/d6cac4db-5705-46a5-a5e4-2445a63d953d" alt="lazy 이후 라우터 이동" />
- 다른 라우터로 이동 시, 추가 chunk.js를 불러오며, 이는 Test1 컴포넌트에 해당한다.
- 이로서 bundle이 정상적으로 분리된 것을 확인할 수 있다.

### 3) 분리된 chunk.js에 이름 붙이기
- 기본 설정을 통해 번들을 분리하면 자동으로 생성된 id를 이름으로 하는 번들이 생성된다.
- 만일, 우리가 분리한 bundle에 유의미한 이름을 부여하고자 한다면 **dynamic import** 과정에 특별한 주석을 삽입해야 한다.

```jsx
// App.js
const Home = lazy(() => import(/* webpackChunkName: "home" */ "./Routes/Home"));
const Test1 = lazy(() => import(/* webpackChunkName: "test1" */ "./Routes/Test1"));
const Test2 = lazy(() => import(/* webpackChunkName: "test2" */ "./Routes/Test2"));
const Test3 = lazy(() => import(/* webpackChunkName: "test3" */ "./Routes/Test3"));
```
- 위와 같이 import 하는 과정에 주석을 삽입하면 번들링 시점에 이 모듈의 이름이 "home"이라는 것을 인식하고 home.chunk.js와 같이 명칭을 붙여 파일을 생성한다.
```
File sizes after gzip:

  53.24 kB  build/static/js/main.f34bbc21.js
  307 B     build/static/js/home.076c7eb7.chunk.js
  304 B     build/static/js/test1.78fa2d37.chunk.js
  304 B     build/static/js/test2.7b05ba40.chunk.js
  303 B     build/static/js/test3.67f9e9f4.chunk.js
```
- 실제로 빌드해보면 위와 같이 파일 명칭이 변경된 것을 확인할 수 있다.

<img src="https://github.com/JaeyeoneeJ/TIL/assets/77138259/e2c61a7e-7490-4c74-97f0-54d31c075c6e" alt="번들 이름 부여" />
- 이를 브라우저에서 확인하면, home, test1 등 번들 이름이 정상적으로 생성된 것을 확인 가능하다.


<hr>
## ref.
- https://reactjs.org/docs/code-splitting.html
- https://helloinyong.tistory.com/297
- https://web.dev/i18n/ko/browser-level-image-lazy-loading/
- https://web.dev/i18n/ko/code-splitting-suspense/
- https://medium.com/@uk960214/%EC%84%B1%EB%8A%A5-%EC%B5%9C%EC%A0%81%ED%99%94-1-%EB%B2%88%EB%93%A4-%ED%81%AC%EA%B8%B0-%EC%A4%84%EC%9D%B4%EA%B8%B0-react-webpack-minify-code-splitting-e2391e7e5f1b