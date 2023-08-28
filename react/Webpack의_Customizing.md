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
  // 원하는 설정 변경을 여기에 작성

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

## 3. CRACO
- CRACO(Create-React-App Configuration Override) 역시 위의 react-app-rewired와 마찬가지로 React 프로젝트를 eject 하지 않고 customizing을 가능하게 해주는 라이브러리 중 하나이다.

```bash
npm install @craco/craco craco-alias craco-less --save-dev
```
1. `@craco/craco`: Create React App의 내부 설정을 수정하지 않고 프로젝트를 확장할 수 있도록 도와주는 도구로 `craco` 패키지를 사용하면 "eject"하지 않고도 CRA의 설정을 커스터마이즈할 수 있다.
2. `craco-alias`: 프로젝트 내에서 경로 별칭(alias)을 설정하고 관리하기 위한 도구로 경로 별칭은 긴 상대 경로를 짧은 이름으로 대체하여 코드 작성을 간결하게 만들어 준다.
3. `craco-less`: Less 스타일을 사용할 때 CRA 프로젝트 내에서 Less 설정을 커스터마이즈하기 위한 도구로 Less는 CSS 전처리기로, 변수, 믹스인, 중첩 등의 기능을 제공하여 스타일 시트 작성을 편리하게 만들어준다.

- 프로젝트 루트 디렉토리에 `craco.config.js` 파일을 생성하여 추가할 Webpack config들을 정의하고 확장시킬 수 있다.

```javascript
const CracoAlias = require('craco-alias');
const CracoLessPlugin = require('craco-less');

module.exports = {
  plugins: [
    {
      plugin: CracoAlias,
      options: {
        source: 'tsconfig',
        // 별칭 설정을 여기에 추가
        aliases: {
          '@components': './src/components',
          '@utils': './src/utils',
        },
      },
    },
    {
      plugin: CracoLessPlugin,
      options: {
        lessLoaderOptions: {
          // Less 옵션을 설정
        },
      },
    },
  ],
};

```

- package.json의 스크립트 실행 명령어를 변경한다.

```json
// package.json

// ...
"scripts": {
  "start": "craco start",
  "build": "craco build",
  "test": "craco test",
  "eject": "craco eject"
  // ...
}
// ...

```


## 4. SMP(Speed Measure Plugin)

- Webpack에 적용되어 있는 많은 기능 중 어떤 것이 가장 많은 시간을 잡아먹는지, 각 기능이 이루어지는데 시간이 얼마나 걸리는지 측정해야 할 경우가 있다.
- Webpack에 설정된 plugin들과 loader들의 속도를 측정해주는 기능이 있다.
- `speed-measure-webpack-plugin`은 Webpack 빌드 프로세스에서 각 단계의 실행 시간을 측정하여 빌드 성능을 분석하고 최적화하는 데 사용되는 도구이다.
- `craco-speed-measure`는 위의 플러그인을 craco와 통합하여 Create React App 프로젝트의 Webpack 빌드를 분석할 수 있도록 도와주는 플러그인이다.
- `craco-speed-measure`를 사용하면 Webpack 빌드 프로세스의 각 부분에서 발생하는 시간 지연을 식별하고 성능 향상을 위한 개선점을 찾을 수 있다.

```bach
npm install craco-speed-measure speed-measure-webpack-plugin --save-dev
```

- 위의 패키지를 개발 의존성으로 설치한다. 개발 의존성은 주로 개발 중에 필요한 도구나 라이브러리를 의미한다.

1. react-app-rewired에 SMP 적용
```javascript
// config-overrides.js
const rewireReactHotLoader = require('react-app-rewire-hot-loader');
const SpeedMeasurePlugin = require('speed-measure-webpack-plugin');

const smp = new SpeedMeasurePlugin();

module.exports = function (config, env) {
	config = smp.wrap(rewireReactHotLoader(config, env), { ...config });
	return config;
};
```

2. craco에 SMP 적용
```javascript
// craco.config.js

const SpeedMeasurePlugin = require('speed-measure-webpack-plugin');
const smp = new SpeedMeasurePlugin();

module.exports = {
	webpack: {
		configure: (webpackConfig) => {
			return smp.wrap(webpackConfig);
		},
	},
};
```

