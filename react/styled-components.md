## styled-components란
- styled-components는 React 애플리케이션에서 CSS 스타일링을 위한 자바스크립트 기반 라이브러리임. 이 라이브러리는 컴포넌트 단위로 스타일을 정의하고 적용할 수 있으며, CSS와 JavaScript를 결합하여 동적인 스타일링을 가능하게 함
- styled-components를 사용하면 JavaScript 파일 안에서 CSS 코드를 작성할 수 있음. 이렇게 스타일을 작성하면 해당 컴포넌트에만 적용되는 유니크한 클래스명이 생성되어 스타일이 적용됨. 이러한 접근 방식은 컴포넌트 기반 개발에 매우 적합하며, 스타일과 컴포넌트가 밀접하게 결합되어 코드의 가독성과 유지보수성을 향상시킴
- styled-components는 CSS-in-JS 라이브러리 중 하나로, CSS를 작성하는 데 필요한 전처리기나 빌드 설정 없이도 사용할 수 있음. 또한, props를 활용하여 동적인 스타일링을 지원하며, 다른 컴포넌트의 스타일을 상속하거나 조합하여 재사용성을 높일 수도 있음
- styled-components는 React뿐만 아니라 React Native 및 Vue.js, Next.js와 같은 다른 프레임워크와도 함께 사용할 수 있음. 컴포넌트 기반의 스타일링을 사용하고 싶을 때 styled-components는 강력한 선택지 중 하나임

## styled-components 설치 및 기본 구조 셋팅
### 1) 설치
- 먼저 생성한 리액트 프로젝트 등에 styled-components 라이브러리를 먼저 설치해야 함

- npm을 사용하는 경우, 다음 명령어를 실행함
```bash
npm install styled-components
```

- yarn 사용하는 경우, 다음 명령어를 실행함
```bash
yarn add styled-components
```

### 2) 적용
- 스타일을 적용할 컴포넌트 파일에서 styled-components를 import 함
```javascript
import styled from 'styled-components'
```

- `styled` 함수를 사용하여 스타일을 정의함. 이 함수는 템플릿 리터럴 문법을 사용하여 CSS 코드를 작성함. 정의된 스타일은 재사용 가능한 컴포넌트로 사용할 수 있음
```js
const StyledButton = styled.button`
	background-color: white;
	coloe: black;
	font-size: 14px;
	padding: 8px 16px;
`;
```

- 스타일을 적용하려는 컴포넌트에서 정의된 스타일 컴포넌트를 사용함. 이렇게 하면 해당 컴포넌트에 스타일이 적용됨
```jsx
function MyComponent() {
	return <StyledButton>Click me</StyledButton>;
}
```

- 위에서 `StyledButton`은 스타일이 적용된 버튼 컴포넌트임. 이렇게 정의된 스타일 컴포넌트를 사용하면 해당 스타일이 적용된 버튼이 렌더링 됨
- styled-components를 사용하면 컴포넌트마다 독립적인 스타일이 적용되며, CSS 클래스명을 걱정할 필요가 없음
- 또한, props를 활용하여 동적인 스타일링을 할 수 있음. 이를 통해 컴포넌트를 더 유연하게 스타일링할 수 있음


## TypeScript 환경에서 styled-components 사용하기
- styled-components를 TypeScript 환경에서 사용하기 위해서는 몇 가지 추가적인 작업이 필요함

### 1) TypeScript 및 선언 파일 설치
- 프로젝트에 TypeScript와 styled-components의 TypeScript 선언 파일을 설치해야 함
```bash
npm install typescript @types/react @types/styled-components
```

- yarn을 사용하는 경우, 아래 명령어를 입력함
```bash
yarn add typescript @types/react @types/styled-components
```

- `tsconfig.json` 파일을 열어 compilerOptions 값을 수정해 줌
- 만약, `tsconfig.json` 파일이 존재하지 않는 경우, 프로젝트 루트 디렉토리에 새로운 파일을 생성하면 됨
```json
{ "compilerOptions":
  { 
    "jsx": "react-jsx", // 또는 "react-jsxdev"를 사용할 수도 있음
    "target": "es6",
    "module": "esnext",
    "esModuleInterop": true,
    "strict": true,
    "noImplicitAny": true,
    "moduleResolution": "node"
  }
}
```
- 위 옵션 중 `strict: true`는 엄격한 타입 체크를 활성화하느나 옵션임
- `noImplicitAny: true`는 암시적으로 any 타입을 허용하지 않도록 설정함
- 위 설정은 일반적인 TypeScript 프로젝트에 적용되는 설정이며, 필요에 따라 추가적인 설정을 할 수 있음
- 설정 파일이 작성되면 TypeScript 컴파일러는 해당 파일을 읽고 프로젝트를 설정에 맞게 컴파일함

> 설정에 대한 자세한 내용은 [TypeScript 문서의 컴파일러 옵션 섹션](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)을 참조

- 이제 TypeScript와 styled-components를 함께 사용할 준비가 되었음
- TypeScript는 정적 타입 검사를 수행하므로 코드에서 타입 관련 오류를 사전에 확인할 수 있게 됨


<hr>
## ref.
- https://styled-components.com/docs
- https://www.typescriptlang.org/docs/
- https://www.typescriptlang.org/docs/handbook/tsconfig-json.html