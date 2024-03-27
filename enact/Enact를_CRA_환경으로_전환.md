### 1) Enact template 설치
```bash
enact template install @enact/template-webostv
enact create -t webostv [<directory>]
```

### 2) 프로젝트 루트에 npm library 설치
```bash
# webpack override
npm install customize-cra react-app-rewired --save-dev

# less 설치
npm install less --save-dev

# less 확장자 로드
npm install less-loader customize-cra-less-loader --save-dev

# less 내 px => rem 변환
npm install postcss-loader postcss-preset-env postcss-pxtorem --save-dev
```

### 3) 프로젝트 루트에 public 폴더 생성
- react-scripts 사용을 위함
  (public 폴더는 기존 CRA 프로젝트의 아무 public 폴더를 복사하여 붙여넣기)

### 4) package.json 수정
```json
{
  "scripts": {
    ...
    "start": "react-app-rewired start",
    "build": "react-app-rewired build"
  },
  "enact": {
    ...
    "ri": {
      "baseSize": 24
    }
  },
  "eslintConfig": {
	  // "extends" 내용 삭제(빈 객체로 두기)
  },
}
```
- 기존 `enact` CLI를 대체하여 `react-scripts`를 사용하나,  webpack을 override하여 사용할 것이므로 `react-app-rewired`로 빌드 스크립트를 교체함
- enact의 `ri`에 baseSize를 24로 적용
- "eslintConfig"는 `eslint-config-enact-proxy` 와 react-scripts 간 충돌 문제로 삭제함

### 5) config-overrides.js 생성
```js
const { override, adjustStyleLoaders } = require("customize-cra");
const addLessLoader = require("customize-cra-less-loader");
const postcssPresetEnv = require("postcss-preset-env");

const themeConfig = require("./package.json").enact; // package.json에서 설정한 값을 가져옴
const baseSize = themeConfig?.ri?.baseSize || 24;

module.exports = override(
  addLessLoader({
    lessLoaderOptions: {
      lessOptions: {
        javascriptEnabled: true,
      },
    },
  }),
  adjustStyleLoaders((rule) => {
    if (rule.test.toString().includes("less")) {
      const loaders = rule.use;
      loaders.splice(loaders.length - 1, 0, {
        loader: "postcss-loader",
        options: {
          postcssOptions: {
            plugins: [
              postcssPresetEnv({
                stage: 3, // 4단계 중 3단계 사용. 최신 문법 지원 중 3단계가 안정적
              }),
              require("postcss-pxtorem")({
                rootValue: baseSize,
                propList: ["*"],
              }),
            ],
          },
        },
      });
    }
  })
);

```
- package.json에서 `baseSize: 24`로 설정하였음
- `postcss-pxtorem` 플러그 인은 특정 CSS 속성 값(px)을 rem 단위로 변환해주는 플러그인으로, `rootValue`는 rem 단위를 계산하는 기준이 되는 값임
- `rootValue: 24`로 설정시 이 플러그인은 모든 px 값을 24의 배수로 변환함
  (width: 48px => width: 2rem)