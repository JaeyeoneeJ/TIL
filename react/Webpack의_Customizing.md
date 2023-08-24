- 일반적으로 CRA(Create React App)을 사용하여 React Project를 구성하면 기본적인 Webpack 설정 파일들이 숨겨져 구성되어 있다.
- 개발을 하다보면 종종 Webpack 설정을 변경하여 적용해야 하는 경우가 생긴다. 이 경우, 어떻게 Webpack 설정을 변경할 수 있을까?

## 1. Eject
```bash
npm eject
# or
yarn eject
```

- eject는 CRA로 생성된 프로젝트의 설정을 custom 하기 위한 방법 중 하나로, CRA를 통해 제공된 숨겨져 있는 모든 설정들과 패키지들의 의존성을 살펴볼 수 있다

> Eject는 신중해야 한다
> 
> 한번 eject를 실행하게 되면 이전 상태로 돌아갈 수 없다.
> ...


## 2. react-app-rewired

- 다행히도 eject를 하지 않고 라이브러리의 도움을 통해 Webpack을 customizing 할 수 있다.
- react-scripts를 대체하는 react-app-rewired를 이용하여 Webpack 설정에 새로운 설정을 주입시켜 custom 해보자.

```bash
npm install react-app-rewired
```

- 프로젝트 루트 디렉토리에 config-overrides.js 파일을 생성하여 추가할 Webpack config들을 정의하고 확장할 수 있다.

```javascript
// config-overrides.js

const path = require('path');

module.exports = function override(config, env) {
  // 원하는 설정 변경을 여기에 작성합니다.

  // 예시: 절대 경로 설정을 위해 경로 별칭 추가
  config.resolve.alias = {
    ...config.resolve.alias,
    '@components': path.resolve(__dirname, 'src/components'),
    '@utils': path.resolve(__dirname, 'src/utils'),
  };

  return config;
};

```

- 위 예시에서는 절대 경로를 설정하기 위해 경로 별칭을 추가하는 방법을 보여준다.

```json
// package.json

// ...
"scripts": {
  "start": "react-app-rewired start",
  "build": "react-app-rewired build",
  "test": "react-app-rewired test",
  // ...
}
// ...

```

- package.json 파일에서 스크립트를 수정하여 `react-scripts` 대신 `react-app-rewired`를 사용하도록 변경한다.

- 이를 통해 Creact React App의 기본 설정을 변경할 수 있으며, 해당 설정이 적용된 Webpack 빌드가 실행될 것이다.

