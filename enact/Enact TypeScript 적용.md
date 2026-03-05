## 1) 목적
- enact 4.1.8에 typescript 적용

## 2) 문제 원인
- enact 4.x의 기본 ESLint 설정(`@enact/eslint-config`)은 내부적으로 파서를 지정하나, TypeScript 파일을 파싱하려면 `@typescript-eslint/parser`가 필요함.
- 하지만 Enact 기본 ESLint 설정이 이것을 포함하지 않거나, 포함하더라도 버전 충돌이 나는 경우가 존재함.

### 2-1) `@babel/eslint-parser` **vs** `@typescript-eslint/parser` **충돌**
- Enact ESLint는 기본적으로 Babel 파서를 씀. Babel 파서는 `.ts`, `.tsx` 파일을 제대로 파싱 못하거나, 타입 문법에서 터짐.

### 2-2) **Enact 4.x의 ESLint 설정이 TS 룰셋을 누락**
- `@enact/eslint-config` 4.x가 `@typescript-eslint/eslint-plugin`의 룰을 포함하지 않아서, TS 전용 문법(타입 단언, 제네릭 등)을 만나면 파싱 에러.

### 2-3) `parserOptions.project` **미설정**
- 타입 기반 린트 룰(`@typescript-eslint/recommended`)은 `tsconfig.json` 경로를 알아야 동작하는데, Enact ESLint 설정에서 이걸 자동으로 잡아주지 않음.

## 3) 해결방법

### 3-1) 추천 TypeScript 버전
- enact 4.1.8의 공식 적용 typescript의 범주는 4.3.x 이나 해당 버전이 너무 낮은 관계로, react 17 대응을 위해 4.9.5로 사용. 만일 5.x로 넘어갈 경우, 컴파일러단에서 문제가 발생하므로 enact 4.1.8 기준 typescript는 최대 4.9.5를 추천.
- 따라서 기본 enact eslint 의존도를 무시하고 babel(react 17과 대응 가능한 webpack 5버전)의 typescript 적용을 위해 아래 사항을 적용
    ```json
    // package.json
    "devDependencies": {
    	"@babel/eslint-parser": "^7.28.6",
    	"@babel/preset-react": "^7.28.5",
    	"@enact/cli": "^4.1.8",
    	"eslint-plugin-react": "^7.37.5",
    	"typescript": "^4.9.5"
    }
    ```

### 3-2) local enact cli 사용
- Enact 공식 문서에서는 글로벌 설치를 기본으로 안내하고 있음.
- 이에 따라 global enact/cli를 사용 시 로컬 패스의 typescript를 사용하게 되어 버전 불일치로 문제가 발생.
    - 글로벌 설치 경로(예시): ~/.nvm/versions/node/v16.x/lib/node_modules/@enact/cli
    - 글로벌 CLI는 실행 시 자신이 설치된 경로 기준으로 의존성을 탐색함
    - 따라서 프로젝트의 node_modules 내 typescript를 찾지 못하거나, 버전이 달라 TS 처리가 정상적으로 동작하지 않음
- TypeScript 적용을 위해 프로젝트에 enact/cli를 설치하여 local 방식 사용 권장
    - 로컬 설치 시 enact serve 실행 시점에 프로젝트의 node_modules를 우선 탐색하므로, 같은 node_modules 안의 TypeScript를 정확히 찾아 TS가 정상 동작함.