## 1. styled-components란
- styled-components는 React 애플리케이션에서 CSS 스타일링을 위한 자바스크립트 기반 라이브러리임. 이 라이브러리는 컴포넌트 단위로 스타일을 정의하고 적용할 수 있으며, CSS와 JavaScript를 결합하여 동적인 스타일링을 가능하게 함
- styled-components를 사용하면 JavaScript 파일 안에서 CSS 코드를 작성할 수 있음. 이렇게 스타일을 작성하면 해당 컴포넌트에만 적용되는 유니크한 클래스명이 생성되어 스타일이 적용됨. 이러한 접근 방식은 컴포넌트 기반 개발에 매우 적합하며, 스타일과 컴포넌트가 밀접하게 결합되어 코드의 가독성과 유지보수성을 향상시킴
- styled-components는 CSS-in-JS 라이브러리 중 하나로, CSS를 작성하는 데 필요한 전처리기나 빌드 설정 없이도 사용할 수 있음. 또한, props를 활용하여 동적인 스타일링을 지원하며, 다른 컴포넌트의 스타일을 상속하거나 조합하여 재사용성을 높일 수도 있음
- styled-components는 React뿐만 아니라 React Native 및 Vue.js, Next.js와 같은 다른 프레임워크와도 함께 사용할 수 있음. 컴포넌트 기반의 스타일링을 사용하고 싶을 때 styled-components는 강력한 선택지 중 하나임

## 2. CSS 전처리기 대신 styled-components
- styled-components를 사용하면 CSS 전처리기를 사용하는 것 보다 다음과 같은 몇 가지 강점이 있음

1. 컴포넌트 기반 접근: 컴포넌트 단위로 스타일을 작성 및 적용하므로 코드의 재사용성과 유지보수성을 높임
2. CSS-in-JS: JavaScript 코드 내에서 스타일을 정의하고 적용 가능함. 스타일 로직을 한 곳에 모아 관리할 수 있으며, 스타일의 일관성을 유지하고 스타일 충돌을 방지할 수 있음
3. 동적 스타일링: 프로퍼티를 props로 전달받아 컴포넌트의 스타일을 변경하거나 조건부 스타일링을 수행할 수 있음
4. 스타일 범위 제한: 각 컴포넌트 내에서 스타일이 캡슐화되므로 이 스타일을 해당 컴포넌트에만 적용되고 다른 컴포넌트에 영향을 주지 않음
5. 간편한 스타일 작성: 템플릿 리터럴 문법을 사용하여 스타일을 작성하므로 CSS의 일반적인 문법과 유사하게 작성 가능
6. 테마 지원: 테마(theme) 지원을 통해 앱 전반의 스타일을 일관되게 유지할 수 있음. 테마는 컴포넌트에서 사용되는 스타일 변수들을 중앙에서 관리하는 방식으로, 전체 앱의 스타일을 일관성있게 변경하거나 다른 테마를 적용하는 것이 용이함
7. 스타일 간 의존성 해결: CSS 전처리기에서는 클래스 이름을 통해 스타일을 선택하고 적용하는 반면, 스타일 컴포넌트는 컴포넌트 단위로 스타일을 작성하기 때문에 스타일 간 의존성 충돌이나 우선순위 문제를 해결할 수 있음
8. 자동 벤더 프리픽싱: 자동으로 벤더 프리픽스(vendor prefix)를 적용하여 크로스 브라우저 호환성을 지원하므로 스타일 작성 시, 일일히 벤더 프리픽스를 추가할 필요가 없음
9. 런타임 성능 최적화: 컴포넌트의 스타일은 한 번 해석되고 적용된 이후에는 캐싱되어 재사용되므로 성능 향상과 빠른 렌더링 속도를 기대할 수 있음

## 3. styled-components 설치 및 기본 구조 셋팅
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


## 4. TypeScript 환경에서 styled-components 사용하기
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


## styled-components에서의 props
### 1) styled-components에서의 props란
- styled-components에서의 `props`는 컴포넌트에 전달되는 속성(값)을 의미함. 이 `props`를 활용하여 스타일드 컴포넌트의 동적인 스타일링과 조건부 스타일링을 구현할 수 있음. `props`는 컴포넌트의 속성을 동적으로 변경하거나, 조건에 따라 다른 스타일을 적용하는 데 사용됨
- styled-components에서 `props`의 핵심적인 역할은 다음과 같음

1. 동적 스타일링(Dynamic Styling): `props`를 활용하여 컴포넌트의 스타일을 동적으로 변경할 수 있음. 예를 들어, `props`의 값을 통해 배경색이나 글자색을 조건에 따라 변경할 수 있음. 이는 컴포넌트가 다양한 상황에서 유연하게 스타일을 적용할 수 있도록 해줌

2. 조건부 스타일링(Conditional Styling): `props`를 활용하여 조건에 따라 다른 스타일을 적용할 수 있음. 예를 들어, `props`의 값에 따라 버튼의 크기나 모양을 조정하거나, 특정 상태일 때에만 특정 스타일을 적용할 수 있음. 이를 통해 컴포넌트의 상태나 속성에 따라 다양한 스타일 변화를 구현할 수 있음

3. 테마(Theme) 지원: styled-components는 ThemeProvider를 통해 테마를 제공함. `props`를 활용하여 테마의 속성을 전달받고, 해당 속성을 기반으로 컴포넌트의 스타일을 설정할 수 있음. 이를 통해 애플리케이션의 전체적인 디자인 시스템을 관리하고 일관된 스타일을 유지할 수 있음

4. 컴포넌트 간 데이터 전달: `props`를 활용하여 컴포넌트 간에 데이터를 전달할 수 있음. 부모 컴포넌트에서 자식 컴포넌트로 `props`를 통해 데이터를 전달하면, 자식 컴포넌트는 해당 데이터를 활용하여 동적으로 스타일을 결정하거나 내용을 렌더링할 수 있음

### 2) props로 동적 스타일 설정
- 일반적으로 styled-components에서 컴포넌트를 정의할 때, 컴포넌트에 전달되는 props 값을 활용하여 스타일을 동적으로 설정할 수 있음
- 이를 통해 컴포넌트 외부에서 전달되는 데이터나 상태에 따라 스타일을 조작할 수 있음
- 예를 들어, 다음은 `Button` 컴포넌트를 정의하고 `primary` prop을 기반으로 다른 스타일을 적용하는 예시임

```jsx
import styled from 'styled-components';

const Button = styled.button`
  padding: 10px 20px;
  font-size: 16px;
  background-color: ${props => props.primary ? 'blue' : 'gray'};
  color: white;
`;

// 사용 예시
<Button primary>Primary Button</Button> // primary prop이 true인 경우 파란색 배경 버튼 
<Button>Default Button</Button> // primary prop이 주어지지 않은 경우 회색 배경 버튼

```

- 위의 예시에서 `Button` 컴포넌트는 `primary` prop을 받아 해당 prop 값에 따라 배경색을 동적으로 설정하고 있음. `primary` prop이 `true`인 경우 파란색 배경 버튼을, `false`인 경우 회색 배경 버튼을 생성함
- 이처럼 styled-components에서 props를 활용하면 컴포넌트의 스타일을 동적으로 제어하고 상황에 맞게 스타일을 변경할 수 있음

### 3) props의 인스턴스는 독립적으로 생성이 될까?
- 만약에 `Button1`과 `Button2`가 스타일 컴포넌트로 생성되었고, App.js에서 ThemeProvider로 theme를 주입받았으며, `theme`의 color는 'black'이라고 가정해보자(props.theme.color === 'black')
- styled-components에서 각각의 컴포넌트 인스턴스는 독립적으로 생성되기 때문에, `Button1`과 `Button2`는 서로 다른 인스턴스로 간주됨
- 따라서 `Button1`과 `Button2`는 동일한 컴포넌트 정의를 공유하지만, 각각의 인스턴스는 별도의 props를 가질 수 있음. props는 컴포넌트 인스턴스에 대한 정보를 저장하기 때문임
- 또한, ThemeProvider를 사용하여 Theme을 전달하더라도, 각각의 `Button1`과 `Button2` 인스턴스는 독립적으로 Theme을 참조함

> props.theme.color가 'black'인 경우, `Button1`과 `Button2`는 각각의 인스턴스에서 동일한 테마 속성을 사용할 수 있지만, 이는 서로 독립된 값으로, `Button1`의 props.theme과 `Button2`의 props.theme은 서로 다른 객체로 존재하게 됨

- 이와 같은 독립성은 컴포넌트 간의 분리된 스타일 및 속성 관리를 용이하게 해주며, 애플리케이션에서의 컴포넌트 재사용성과 유지보수성을 높일 수 있음

### 4) props의 인스턴스는 독립적으로 생성되는 것이 중요한 이유
- 각각의 컴포넌트 인스턴스가 독립적으로 생성되는 것은 styled-components에서 중요한 이유가 있음

1. 스타일 격리(Isolation): 각 컴포넌트 인스턴스는 자체적으로 스타일을 정의하고 관리함. 이는 스타일이 다른 컴포넌트에 미치는 영향을 방지하고 스타일 격리를 가능하게 함. 따라서 한 컴포넌트의 스타일 변경이 다른 컴포넌트에 영향을 주지 않으며, 스타일 충돌을 방지할 수 있음

2. 코드 재사용과 모듈화: 독립적인 컴포넌트 인스턴스는 코드의 재사용성과 모듈화를 높여줌. 각 컴포넌트는 자체적인 스타일과 동작을 가지며, 필요한 경우 재사용될 수 있음. 이는 코드의 구조를 단순화하고 유지보수를 용이하게 만듬

3. 컴포넌트 단위 테스팅: 독립적인 컴포넌트 인스턴스는 컴포넌트 단위 테스팅을 용이하게 만듬. 각 컴포넌트는 독립적으로 동작하고, 특정 상황에서 예상대로 작동하는지 쉽게 확인할 수 있음. 이는 테스트 코드 작성과 디버깅을 편리하게 만들어 줌
 
4. 성능 최적화: 각각의 컴포넌트 인스턴스는 필요한 경우에만 업데이트되고 재렌더링 됨. 이는 React의 가상 DOM(Virtual DOM) 및 조화(Reconciliation) 과정을 최적화하여 성능을 향상시킴. 독립적인 컴포넌트 인스턴스를 사용하면 불필요한 재렌더링을 방지하고, 전체 애플리케이션의 성능을 향상시킬 수 있음

- 따라서 styled-components에서 각각의 컴포넌트 인스턴스가 독립적으로 생성되는 것은 스타일 격리, 코드 재사용과 모듈화, 테스팅 편의성, 성능 최적화 등의 이점을 제공하며, 유지보수성과 확장성을 향상시켜 줌

### 5) props로 참조하는 값의 수정
- styled-components에서 props는 컴포넌트에 전달되는 값으로 일반적으로 읽기 전용임. 컴포넌트가 props를 받으면 그 값을 사용하여 스타일을 조정하거나 렌더링을 수행할 수 있음. 그러나 일반적으로 props 값을 직접 수정하는 것은 권장되지 않음
- props는 부모 컴포넌트에서 자식 컴포넌트로 전달되는 값으로, 일반적으로 컴포넌트의 상태(state)를 변경하는 용도로 사용됨. 상태(state)는 컴포넌트 내에서 변경 가능한 값으로 관리되며, 컴포넌트 내에서 직접 수정할 수 있음
- styled-components에서는 컴포넌트가 props를 받아 스타일을 조정하거나 렌더링하는 것이 일반적인 용도임. 만약 스타일을 동적으로 변경하려면 상태(state)를 활용하여 컴포넌트의 상태를 변경하고, 그에 따라 스타일을 업데이트하는 방식을 사용하는 것이 보다 적절함
- 예를 들어, 컴포넌트가 특정 상황에 따라 스타일을 변경해야 한다면, 해당 상황에 대한 상태를 관리하고 상태가 변경될 때 스타일을 조정하는 방식을 사용할 수 있음. 이를 위해 컴포넌트의 상태를 변경하는 함수를 정의하고, 이벤트나 조건에 따라 해당 함수를 호출하여 상태를 업데이트하고 스타일을 조정할 수 있음
- props 값을 직접 수정하는 것은 컴포넌트의 일관성과 예측 가능성을 저해할 수 있으므로, 일반적으로 권장되지 않음. 대신 상태(state)를 활용하여 컴포넌트를 관리하고, 상태에 따라 동적으로 스타일을 조정하는 것이 좋음

<hr>
## ref.
- https://styled-components.com/docs
- https://www.typescriptlang.org/docs/
- https://www.typescriptlang.org/docs/handbook/tsconfig-json.html