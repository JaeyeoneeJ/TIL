## 1. sourcemap이란
- CRA로 생성한 React 프로젝트를 빌드하면 기본적으로 production build가 된다.
- 기본적으로 webpack에 의해 번들링 된 파일과 해당 파일에 대응되는 sourcemap이 생성된다.
```
./build
├── asset-manifest.json
├── favicon.ico
├── index.html
├── logo192.png
├── manifest.json
├── robots.txt
└── static
    ├── css
    │   ├── main.46b46fb8.css
    │   └── main.46b46fb8.css.map
    ├── js
    │   ├── main.b8a6ce22.js
    │   ├── main.b8a6ce22.js.LICENSE.txt
    │   └── main.b8a6ce22.js.map
    └── media
        └── ic_search.3a608aca6f4d314714c4.png
```
- 위를 보면 `*.css.map`과 `*.js.map` 파일이 sourcemap 이다.

### 1) Debuging을 위해
- 이 파일이 있으면 압축되고 난독화된 파일을 브라우저 내에서 다시 원본 소스처럼 확인할 수 있다.(브라우저 development tools의 sources 탭 등)

### 2) sourcemap의 위험
- 실제 배포 시 이 sourcemap은 제거되는 편이 좋다.

- 내부 코드가 그대로 노출되므로 난독화의 이점을 상실한다.
- 빌드 시 메모리 부족(OOM) 이슈가 발생할 수 있다.

- 하지만 sourcemap은 디버깅에 매우 유용하기 때문에, webpack 설정 등으로 문제를 해결할 수도 있다.


## 2. sourcemap 제거
 - CRA로 생성한 React 프로젝트인 경우, sourcemap을 제거하는 방법은 간단하다.

- 환경변수 `GENERATE_SOURCEMAP`의 값을 `false`로 설정할 수도 있다. 설정하는 방법은 `.env` 파일에 `GENERATE_SOURCEMAP=false`를 추가해서 설정할 수 있다.

- 하지만 `package.json`을 다음과 같이 수정하여도 된다.
```json
//package.json
"scripts": {
  "build": "GENERATE_SOURCEMAP=false react-scripts build",
  ...
}
```
- 그렇다면 아래와 같이 sourcemap이 제거된 것을 확인할 수 있다.

```
./build
├── asset-manifest.json
├── favicon.ico
├── index.html
├── logo192.png
├── manifest.json
├── robots.txt
└── static
    ├── css
    │   └── main.aaf0ba32.css
    ├── js
    │   ├── main.42a487ed.js
    │   └── main.42a487ed.js.LICENSE.txt
    └── media
        └── ic_search.3a608aca6f4d314714c4.png
```

### 또는
- 빌드 후 생성된 sourcemap을 제거할 수도 있다.
```json
//package.json
"scripts": {
  "build": "react-scripts build && rm build/static/**/*.map",
  ...
}
```



<hr>
## ref.
- https://velog.io/@racoon/React-build-%EC%8B%9C-sourcemap-%EC%A0%9C%EA%B1%B0%ED%95%98%EA%B8%B0