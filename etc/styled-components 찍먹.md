<p align="right">23.06.01.  jaeyeonee</p>

# 1. styled-components란
- styled-components는 CSS-in-JS 라이브러리 중 하나로, CSS를 작성하는 데 필요한 전처리기나 빌드 설정 없이도 사용할 수 있음. 또한, props를 활용하여 동적인 스타일링을 지원하며, 다른 컴포넌트의 스타일을 상속하거나 조합하여 재사용성을 높일 수도 있음
- styled-components는 React뿐만 아니라 React Native 및 Vue.js, Next.js와 같은 다른 프레임워크와도 함께 사용할 수 있음. 컴포넌트 기반의 스타일링을 사용하고 싶을 때 styled-components는 강력한 선택지 중 하나임


# 2. styled-components의 핵심 기능
- props를 활용한 동적 스타일링: 프로퍼티를 props로 전달받아 컴포넌트의 스타일을 변경하거나 조건부 스타일링을 수행할 수 있음
- 테마 지원: 테마(theme) 지원을 통해 앱 전반의 스타일을 일관되게 유지할 수 있음. 테마는 컴포넌트에서 사용되는 스타일 변수들을 중앙에서 관리하는 방식으로, 전체 앱의 스타일을 일관성있게 변경하거나 다른 테마를 적용하는 것이 용이함
- 자동 벤더 프리픽싱: 자동으로 벤더 프리픽스(vendor prefix)를 적용하여 크로스 브라우저 호환성을 지원하므로 스타일 작성 시, 일일히 벤더 프리픽스를 추가할 필요가 없음(chrome: -webkit-, IE: -ms-, firefox: -moz-, ...)
- 런타임 성능 최적화: 컴포넌트의 스타일은 한 번 해석되고 적용된 이후에는 캐싱되어 재사용되므로 성능 향상과 빠른 렌더링 속도를 기대할 수 있음
- 컴포넌트 기반 접근: 컴포넌트 단위로 스타일을 작성 및 적용하므로 코드의 재사용성과 유지보수성을 높이고, 각 컴포넌트 내에서 스타일이 캡슐화되므로 이 스타일을 해당 컴포넌트에만 적용되고 다른 컴포넌트에 영향을 주지 않음

> CSS 전처리기와 styled-components의 특징은 아래 문서 참고
> [CSS 전처리기와 styled-components](../CSS/CSS_전처리기와_styled-components.md)


# 3. styled-components에서의 props
- styled-components에서의 `props`는 컴포넌트에 전달되는 속성(값)을 의미함. 이 `props`를 활용하여 스타일드 컴포넌트의 동적인 스타일링과 조건부 스타일링을 구현할 수 있음. `props`는 컴포넌트의 속성을 동적으로 변경하거나, 조건에 따라 다른 스타일을 적용하는 데 사용됨

## 1) props로 동적 스타일 설정
- 일반적으로 styled-components에서 컴포넌트를 정의할 때, 컴포넌트에 전달되는 props 값을 활용하여 스타일을 동적으로 설정할 수 있음. 이를 통해 컴포넌트 외부에서 전달되는 데이터나 상태에 따라 스타일을 조작할 수 있음
- 예를 들어, 다음은 `Button` 컴포넌트를 정의하고 `primary` prop을 기반으로 다른 스타일을 적용해보자

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

## 2) Theme 적용
- styled-components는 ThemeProvider를 통해 theme라는 전역 변수 객체를 받을 수 있음. theme 역시 동적으로 상태를 할당할 수 있으므로 이를 통해 Light/Dark Mode를 적용할 수도 있음
```jsx
import React, { useState } from 'react';
import { ThemeProvider, createGlobalStyle } from 'styled-components';

// Light 모드와 Dark 모드에 대한 테마 설정
const lightTheme = {
  background: '#ffffff',
  fontColor: '#000000',
};

const darkTheme = {
  background: '#000000',
  fontColor: '#ffffff',
};

// Global 스타일 설정
const GlobalStyle = createGlobalStyle`
  body {
    background: ${(props) => props.theme.background};
    color: ${(props) => props.theme.fontColor};
    /* 추가적인 스타일 설정 */
  }
`;

const App = () => {
  const [theme, setTheme] = useState(lightTheme);

  const toggleTheme = () => {
    setTheme(theme === lightTheme ? darkTheme : lightTheme);
  };

  return (
    <ThemeProvider theme={theme}>
      <div>
        <button onClick={toggleTheme}>Toggle Theme</button>
        <h1>Hello, World!</h1>
        <p>This is an example of Light and Dark mode.</p>
      </div>
      <GlobalStyle />
    </ThemeProvider>
  );
};

export default App;
```
- 위 코드에서는 Light 모드와 Dark 모드를 위한 `lightTheme`과 `darkTheme` 객체를 생성하였음
- `ThemeProvider`를 사용하여 `theme` prop에 상태값을 전달하고, `GlobalStyle` 컴포넌트 내에서 해당 테마의 배경색과 폰트색을 설정함
- `toggleTheme` 함수를 통해 테마를 전환할 수 있도록 구현하였으며, 버튼 클릭 시 `theme` 상태를 변경하여 Light 모드와 Dark 모드를 토글할 수 있음
- 이를 기반으로 테마에 따라 배경색과 폰트색을 구분하여 적용할 수 있음

## 3) GlobalStyle
- 스타일을 전역에 적용하여 기본 스타일을 초기화하고 통일된 스타일을 적용하고 싶은 경우(reset CSS 등) styled-components의 `createGlobalStyle`을 활용하여 전역적으로 적용되는 스타일을 정의할 수 있음
- 일반적으로 App.js 내에 적용하는 편이며, 적용 시기가 반드시 정해져있지는 않음
- `createGlobalStyle`은 javascript가 parsing 된 시점에 정의되어 있을 뿐, 렌더링이 된 것은 아니며, 해당 컴포넌트가 불러와지는 시점에서 렌더링 됨


# 4. 스타일 컴포넌트 인스턴의 독립성
## 1) props의 인스턴스는 독립적으로 생성이 될까?
- 만약에 `Button1`과 `Button2`가 스타일 컴포넌트로 생성되었고, App.js에서 ThemeProvider로 theme를 주입받았으며, `theme`의 color는 'black'이라고 가정해보자(props.theme.color === 'black')
- styled-components에서 각각의 컴포넌트 인스턴스는 독립적으로 생성되기 때문에, `Button1`과 `Button2`는 서로 다른 인스턴스로 간주됨
- 따라서 `Button1`과 `Button2`는 동일한 컴포넌트 정의를 공유하지만, 각각의 인스턴스는 별도의 props를 가질 수 있음. props는 컴포넌트 인스턴스에 대한 정보를 저장하기 때문임
- 또한, ThemeProvider를 사용하여 Theme을 전달하더라도, 각각의 `Button1`과 `Button2` 인스턴스는 독립적으로 Theme을 참조함

> props.theme.color가 'black'인 경우, `Button1`과 `Button2`는 각각의 인스턴스에서 동일한 테마 속성을 사용할 수 있지만, 이는 서로 독립된 값으로, `Button1`의 props.theme과 `Button2`의 props.theme은 서로 다른 객체로 존재하게 됨

- 이와 같은 독립성은 컴포넌트 간의 분리된 스타일 및 속성 관리를 용이하게 해주며, 애플리케이션에서의 컴포넌트 재사용성과 유지보수성을 높일 수 있음

## 2) props의 인스턴스는 독립적으로 생성되는 것이 중요한 이유
- 각각의 컴포넌트 인스턴스가 독립적으로 생성되는 것은 styled-components에서 중요한 이유가 있음

1. 스타일 격리(Isolation): 각 컴포넌트 인스턴스는 자체적으로 스타일을 정의하고 관리함. 이는 스타일이 다른 컴포넌트에 미치는 영향을 방지하고 스타일 격리를 가능하게 함. 따라서 한 컴포넌트의 스타일 변경이 다른 컴포넌트에 영향을 주지 않으며, 스타일 충돌을 방지할 수 있음

2. 코드 재사용과 모듈화: 독립적인 컴포넌트 인스턴스는 코드의 재사용성과 모듈화를 높여줌. 각 컴포넌트는 자체적인 스타일과 동작을 가지며, 필요한 경우 재사용될 수 있음. 이는 코드의 구조를 단순화하고 유지보수를 용이하게 만듬

3. 컴포넌트 단위 테스팅: 독립적인 컴포넌트 인스턴스는 컴포넌트 단위 테스팅을 용이하게 만듬. 각 컴포넌트는 독립적으로 동작하고, 특정 상황에서 예상대로 작동하는지 쉽게 확인할 수 있음. 이는 테스트 코드 작성과 디버깅을 편리하게 만들어 줌
 
4. 성능 최적화: 각각의 컴포넌트 인스턴스는 필요한 경우에만 업데이트되고 재렌더링 됨. 이는 React의 가상 DOM(Virtual DOM) 및 조화(Reconciliation) 과정을 최적화하여 성능을 향상시킴. 독립적인 컴포넌트 인스턴스를 사용하면 불필요한 재렌더링을 방지하고, 전체 애플리케이션의 성능을 향상시킬 수 있음

- 따라서 styled-components에서 각각의 컴포넌트 인스턴스가 독립적으로 생성되는 것은 스타일 격리, 코드 재사용과 모듈화, 테스팅 편의성, 성능 최적화 등의 이점을 제공하며, 유지보수성과 확장성을 향상시켜 줌

## 3) props로 참조하는 값의 수정
- styled-components에서 props는 컴포넌트에 전달되는 값으로 일반적으로 읽기 전용임. 컴포넌트가 props를 받으면 그 값을 사용하여 스타일을 조정하거나 렌더링을 수행할 수 있음. 그러나 일반적으로 props 값을 직접 수정하는 것은 권장되지 않음
- props는 부모 컴포넌트에서 자식 컴포넌트로 전달되는 값으로, 일반적으로 컴포넌트의 상태(state)를 변경하는 용도로 사용됨. 상태(state)는 컴포넌트 내에서 변경 가능한 값으로 관리되며, 컴포넌트 내에서 직접 수정할 수 있음
- styled-components에서는 컴포넌트가 props를 받아 스타일을 조정하거나 렌더링하는 것이 일반적인 용도임. 만약 스타일을 동적으로 변경하려면 상태(state)를 활용하여 컴포넌트의 상태를 변경하고, 그에 따라 스타일을 업데이트하는 방식을 사용하는 것이 보다 적절함
- 예를 들어, 컴포넌트가 특정 상황에 따라 스타일을 변경해야 한다면, 해당 상황에 대한 상태를 관리하고 상태가 변경될 때 스타일을 조정하는 방식을 사용할 수 있음. 이를 위해 컴포넌트의 상태를 변경하는 함수를 정의하고, 이벤트나 조건에 따라 해당 함수를 호출하여 상태를 업데이트하고 스타일을 조정할 수 있음
- props 값을 직접 수정하는 것은 컴포넌트의 일관성과 예측 가능성을 저해할 수 있으므로, 일반적으로 권장되지 않음. 대신 상태(state)를 활용하여 컴포넌트를 관리하고, 상태에 따라 동적으로 스타일을 조정하는 것이 좋음


# 5. styled-components는 디버깅이 어렵다는데
- styled-components는 스타일을 동적으로 생성하기 위해 임의의 클래스 이름을 생성함. 이로 인해 외부에서 코드를 읽거나 디버깅할 때 일부 어려움을 겼을 수 있음. 그러나 styled-components는 디버깅을 용이하게 하기 위해 몇 가지 도구와 기능을 제공함

### 1) 개발자 도구에서 component 탭 확인
- 크롬 개발자 도구를 열고 extension으로 react devtools를 설치하면 react 프로젝트일 경우 component 탭이 생성됨. 이 컴포넌트 탭에서 tree 구조로 해당 컴포넌트에 동적으로 생성된 클래스 이름을 확인하고 해당 스타일을 식별할 수 있음
- 만약 displayName 속성을 부여했다면 displayName에 부여한 값이 있을 것임. 이를 통해 개발자 도구에서 컴포넌트의 이름을 식별하기 쉽게 할 수 있음
```jsx
const StyledButton = styled.button`
  /* 스타일 정의 */
`

StyledButton.displayName = "스타일한 버튼"
```

### 2) CSS 클래스 추적
- styled-components에서 CSS 클래스 추적 기능을 활성화하려면 `babel-plugin-styled-components`와 함께 Babel 구성을 설정해야 함
```bash
npm install --save-dev babel-plugin-styled-components
```
- 설치가 완료된 후 Babel 구성 파일인 `.babelrc` 파일에 `styled-components` 플러그인을 추가함 (없으면 생성)
- 프로젝트를 다시 빌드하거나 개발 서버를 재시작 함
- 이제 개발자 도구에서 해당 컴포넌트 요소를 검사하면 클래스 이름 옆에 styled-components의 소스 코드 위치가 표시됨

> **CRA 환경에서 CSS 클래스 추적하기**
- CRA 환경에서는 babel 설정을 변경하기 위해 별도의 플러그인을 설치하여 조작하여야 함 
- 다음 패키지를 설치함
```bash
npm install react-app-rewired customize-cra
```
- `react-app-rewired`는 eject를 꺼내지 않고 babel 설정을 변경하기 위함
- `customize-cra`는 `config-overrides.js` 설정을 편하기 변경하기 위함

- `package.json`의 스크립트 중 start, test, build에 있는 react-script를 'react-app-rewired'로 변경해 줌

```json
"scripts": {
  "start": "react-app-rewired start",
  "build": "react-app-rewired build",
  "test": "react-app-rewired test",
  ...
},
```

- babelrc 파일을 사용하기 위해 `config-overrides.js`에 설정을 재정의 함

```javascript
const { override, useBabelRc } = require('customize-cra');

module.exports = override(useBabelRc());
```
- 프로젝트를 재시작하면 정상적으로 적용되는 것을 확인


# 6. 간단하게 알아보는 React 디자인 패턴

> 본문은 [NEXTREE에서 작성한 'React 디자인 패턴'의 기술 블로그](https://www.nextree.io/react-design-pattern/)를 인용하여 조금씩 수정하고 살을 붙여 작성한 자료입니다.

## > 개요
- 디자인 패턴이란 프로그램을 개발하는 과정에서 사용되는 설계 패턴들을 정의한 것임
- 과거의 웹 사이트와 비교했을 때, 현재는 화면 작업이 훨씬 복잡해졌고 React, Vue와 같은 개발 프레임워크를 활용하여 **컴포넌트 단위로 웹 페이지를 개발**하고 있음
- 더욱 효율적인 웹사이트 구축이나 유지 보수를 위해 **컴포넌트 단위의 활용**이 중요하게 되었는데, 이에 컴포넌트를 어떻게 구성할 것인가에 대한 고민이 더해지게 되었음
- 다양한 디자인 패턴이 있으며 어떤 디자인 패턴을 프로젝트에 적용할 것인지 각 프로젝트의 특성에 맞게 고려해야 함

## 1) Presentation & Container
- React 디자인 패턴 중 가장 기본적인 패턴으로, 데이터 로직을 수행하는 Container 컴포넌트와 데이터를 출력하는 Presentation 컴포넌트를 분리하여 구현하는 디자인 패턴임
- 역할별 컴포넌트 분리로 인해 각각의 명확한 기능과 책임을 갖게 됨. 만약 컴포넌트를 분리하지 않고 하나의 컴포넌트 안에 로직, View를 구현하는 코드가 모두 있게 되면 컴포넌트 재사용이 어렵고 컴포넌트 의존도가 높아지게 됨

### (1) Container 컴포넌트
- API 호출, State 관리, Event 처리 등의 작업을 수행하는 컴포넌트
- 변경된 상태 값을 props를 통해 Presentation 컴포넌트로 전달함

### (2) Presentation 컴포넌트
- UI를 표시하는 컴포넌트
- 직접 상태값을 관리하지 않고 Container 컴포넌트가 전달해준 props를 받아 출력함
<img src="https://github.com/JaeyeoneeJ/TIL/assets/77138259/11ba88d4-60e2-4353-aabb-9ce8e8d94dd7" alt="Presentation & Container Pattern" />
<p style="margin:0" align="right"><a href="https://www.nextree.io/content/images/size/w1000/2023/02/Untitled.png">출처: NEXTREE Blog</a></p>

- 코드를 분리한다는 것이 복잡하게 느껴질 수 있지만, 위 이미지처럼 하나의 컴포넌트를 간단히 하나의 컴포넌트가 Logic을 담당하는 컴포넌트와 UI를 담당하는 컴포넌트로 분리된다고 이해하면 쉬움
- 다음 코드를 보며 어떤 방식으로 코드를 분리하는지 확인해보자

### (3) 패턴 적용 예시
1. 적용 전 코드
```jsx
function SearchForm() {
  const [searchKey, setSearchKey] = useState();
  
  function onChange(event) {
    setSearchKey(event.target.value);
  }
  
  function onSubmit(event) {
    event.preventDefault();
    console.log(searchKey);
  }
  
  return (
    <form onSubmit={onSubmit}>
      <div>
        <label>제목</label>
        <input type="text" value={searchKey} onChange={onChange} name="searchKey" />
        <button type="submit">검색</button>
      </div>
    </form>
  )
}

export default SearchForm;
```
- 예시로 input 태그와 button 태그로 검색 기능을 수행하는 SearchForm 컴포넌트를 만들었음
- 검색어를 입력하고 검색 버튼을 클릭하면 검색어가 콘솔에 출력되는 간단한 코드임
- 한 컴포넌트 내에 states, methods, 렌더링 될 UI 코드 등이 모두 구현되어 있음
- 이 컴포넌트에 Presentation & Container 패턴을 적용해보자

2. 적용 후 코드
```jsx
// Presentation Component
function SearchFormView({searchKey, onChange, onSubmit}) {
  return (
    <form onSubmit={onSubmit}>
      <div>
        <label>제목</label>
        <input type="text" value={searchKey} onChange={onChange} name="searchKey" />
        <button type="submit">검색</button>
      </div>
    </form>
  )
}

export default SearchFormView;
```
- 우선 Presentation 컴포넌트를 만들기 위해 새로운 .js 파일을 만들고 SearchFormView라고 명명함
- 기존 컴포넌트에서 렌더링 되었을 JSX 코드(리턴 값)를 그대로 가져오고, 해당 컴포넌트에서 선언되지 않은 states, methods는 props로 받아오도록 처리함

```jsx
// Container Component
function SearchFormContainer() {
  const [searchKey, setSearchKey] = useState();
  
  function onChange(event) {
    setSearchKey(event.target.value);
  }
  
  function onSubmit(event) {
    event.preventDefault();
    console.log(searchKey);
  }
  
  return (
    <SearchFormView
      searchKey={searchKey}
      onChange={onChange}
      onSubmit={onSubmit}
    />
  )
}

export default SearchFormContainer;
```
- 기존 컴포넌트는 Container라는 이름으로 바꾸어주고, states와 methods는 그대로 유지함
- UI가 렌더링될 return에는 방금 만든 SearchFormView 컴포넌트를 호출해주고 SearchFormView가 필요로 하는 states와 methods를 props로 내려주면 됨

### (4) Presentation & Container 패턴 특징
- Presentation 컴포넌트를 재사용할 수 있음. 로직이 포함되어 있지 않기 때문에 다른 컴포넌트와의 의존도가 낮아 다양한 container와 조합해 재사용할 수 있음
- 컴포넌트별 역할이 명확(앱 기능과 UI에 대한 구분이 쉬움)하여 코드 구조를 이해하기 쉬움
- 같은 state를 여러 Presentation 컴포넌트에 props로 전달하여 상태를 공유할 수 있음

### (5) 그러나
- 그러나 이 패턴(Presentation & Container)의 창시자인 'Dan Abramov'는 더 이상 이렇게 구성 요소를 분할하지 않는 것이 좋겠다고 언급함
- React에서 Hooks의 개념이 2019년에 생겨나면서 Hooks를 사용하면 임의의 분할 없이 동일한 작업(Hook은 로직과 표현(UI)의 분리를 가능하게 함)을 수행할 수 있고, 기존 Presentation & Container 패턴을 사용하는 경우가 필요에 의해서보다 맹복적으로 사용하는 경우가 많았기 때문이라고 설명함

## 2) Custom Hooks
- 기존의 Presentation & Container 디자인 패턴과 달리 컴포넌트에서 **로직을 hooks로 분리하여 관리**하는 디자인 패턴임
- 기존의 디자인에서는 다른 Container들에서 공통의 로직을 사용할 경우, 각각 정의해주어야 했으나 hooks로 로직을 분리함으로써 **로직의 재사용이 가능**하게 됨

<img src="https://github.com/JaeyeoneeJ/TIL/assets/77138259/00c5171d-f230-482c-8feb-7c48be584955" alt="Presentation & Container Pattern" />
<p style="margin:0" align="right"><a href="https://www.nextree.io/content/images/size/w1000/2023/02/Untitled-1.png">출처: NEXTREE Blog</a></p>

- 위 이미지처럼 UI는 다르지만 동일한 로직을 사용하는 컴포넌트가 두 개 있다고 가정해보자
- 기존에는 각 컴포넌트 내에 로직을 선언했지만 로직을 별도의 파일로 분리하고 각각의 컴포넌트에서 hooks를 호출한다면, 로직 코드를 중복으로 선언할 필요가 없음
- 이해를 위해 Presentation & Container 패턴에서 작성한 SearchForm 예시 컴포넌트를 활용하여 Custom Hooks 패턴을 적용해보자

### (1) 패턴 적용 예시
1. 적용 후 코드
```jsx
function SearchForm() {
  const {searchKey, onChange, onSubmit} = useSearch();

  return (
    <form onSubmit={onSubmit}>
      <div>
        <label>제목</label>
        <input type="text" value={searchKey} onChange={onChange} name="searchKey" />
        <button type="submit">검색</button>
      </div>
    </form>
  )
}

export default SearchForm;
```
- SearchForm 컴포넌트에는 Presentation 컴포넌트와 유사하게 UI 코드만 남기지만, states 및 methods를 props로 받아오는 대신 useSearch라는 hooks에서 받아오는 것으로 선언함

```jsx
// Hooks
export default function useSearch() {
  const [searchKey, setSearchKey] = useState();
  
  function onChange(event) {
    setSearchKey(event.target.value);
  }
  
  function onSubmit(event) {
    event.preventDefault();
    console.log(searchKey);
  }
  
  return {
    searchKey,
    onChange,
    onSubmit
  }
}
```
- useSearch Hooks를 살펴보면 Container 컴포넌트처럼 기존 SearchForm 컴포넌트가 가지고 있던 그대로 로직을 가지고 있음
- 하지만 return에서 특정 컴포넌트를 호출하는 것이 아니라 states와 methods를 반환하고 있음. 따라서 SearchForm 컴포넌트 뿐만 아니라 동일한 로직이 필요한 어느 컴포넌트에서도 useSearch Hooks를 호출하여 사용할 수 있음

### (2) Custom Hooks 디자인 패턴 특징
- 여러 컴포넌트에서 동일한 로직을 공유할 수 있음
- 컴포넌트의 제어가 쉬워지고 사용자가 더 많은 통제권을 가질 수 있음
- 로직이 렌더링과 분리되어 있어 이를 올바르게 연결하려면 컴포넌트의 동작 방식에 대한 깊은 이해가 필요함

## 3) Atomic
- 아토믹 디자인은 컴포넌트의 재활용을 최대화하기 위한 방법론으로, 아토믹이라는 이름에서 알 수 있듯 원자 개념을 사용한 디자인 패턴임
- 자연에서는 원자가 결합하여 분자가 되고, 분자가 모여 복잡한 사물을 이루게 되는데, 이처럼 아토믹 디자인은 작은 컴포넌트가 모여 복잡한 컴포넌트가 되어가는 것을 기본 개념으로 함
- 가장 작은 컴포넌트인 원자(Atoms)부터 분자(Molecules), 유기체(Organisms), 템플릿(Templates), 페이지(Pages)까지 구분하고 있음

<img style="background: white" src="https://github.com/JaeyeoneeJ/TIL/assets/77138259/93083ad3-c3de-4efd-a44f-fa735382216d" alt="Presentation & Container Pattern" />
<p style="margin:0" align="right"><a href="https://www.usabilis.com/atomic-design/">출처: usabilis.com</a></p>

### 1) 아토믹 디자인의 구성
1. 원자
- UI를 구성하는 가장 작은 요소로 상위 컴포넌트에서 활용할 수 있는 최소 단위로 단독적으로는 명확한 기능을 갖지 않음. 하지만 글꼴 크기, 색상 등 추상적인 속성은 가질 수 있음
- 원자 컴포넌트에서는 버튼, 아이콘, 입력 영역, 라디오 버튼, 텍스트 등이 있음

2. 분자
- 여러 개의 원자를 조합하여 형성한 컴포넌트로 용도가 명확하면서도 재사용성을 가지고 있음. 하지만 많은 원자가 사용되어 복잡한 분자가 되면 재사용성이 떨어지므로 단순함을 유지해야 함
- 분자 컴포넌트에는 입력 폼, 내비게이션, 카드 등이 있음
- 예시) '검색 폼'이라는 분자는 '검색' Label 원자, 입력 영역(input) 원자, '검색' 버튼 원자로 구성

3. 유기체
- 분자들이 결합되어 형성된 컴포넌트로 분자가 되지 않은 원자도 사용될 수 있음
- 규모가 작은 원자와 분자와는 달리 유기체는 사이즈가 제각각이며 복잡하기 때문에 재사용성을 고려하지 않음
- 유기체의 예시로는 헤더(Header)나 푸터(Footer)가 있으며, 대표적인 컴포넌트는 입력 폼과 함께 헤더를 감싸거나 카드를 관리하는 그리드(쇼핑몰에서 각 상품(분자)를 반복적으로 표시해주는 것을 모아놓은 것 등) 등이 있음

4. 템플릿
- 유기체들이 모여 배치함으로써 페이지 구조나 레이아웃 구성 등을 나타냄
- 실제 컨텐츠보다 컨텐츠 구조가 어떻게 구성되고 작동하는지 동적 요소를 확인할 수 있음

5. 페이지
- 템플릿에 실제 데이터가 반영된 상태로, 작성한 컴포넌트가 올바르게 작동하는지 확인하는 완성된 하나의 페이지

### 2) 아토믹 디자인의 특징
1. 장점
- 컴포넌트의 재사용성이 극대화될 수 있음
- 컴포넌트의 계층 구조를 알아보기 쉬워 설계 변경이 필요할 시 빠르게 대처할 수 있음
- 디자인 요소가 재사용될 컴포넌트에 일괄로 적용되므로 styles 적용 및 변경이 쉬움

2. 단점
- 컴포넌트가 적절하게 분리되지 않으면 오히려 컴포넌트의 복잡도가 높아져 유지보수가 까다로움
- Page부터 Atom까지 너무 많은 props drilling\*이 일어나 복잡한 상태 관리로 개발 피로도가 증가함. 만약 전달하는 Props의 자료형이 변경되면 거쳐가는 모든 컴포넌트의 매개변수 자료형을 변경해주어야 하기에 불필요한 소모가 큼(특히 TypeScripit에서)

> \* props drilling: props를 오로지 최하위 컴포넌트로 전달하기 위해서 여러 컴포넌트를 거쳐가는 것

## > 디자인 패턴 적용에 대한 생각
- React에 적용 가능한 세 가지의 디자인 패턴에 대해 알아보았음. 이 외에도 다양한 디자인 패턴이 있지만 이러한 디자인 패턴이 우리의 프로젝트에 맞는지에 대한 깊은 고민이 필요함. 디자인 패턴은 개발의 편리함을 위해 고안되었으나 각각 다른 불편함을 가지고 있기 때문임
- Custom Hooks 디자인은 로직의 분리로 로직의 쉬운 재활용이 가능하지만, 컴포넌트와 로직의 긴밀한 동작 방식을 이해하지 못한다면 코드를 이해하기 어려워질 수 있음
- 아토믹 디자인은 컴포넌트 재활용을 골자로 편리한 웹 개발을 위해 고안된 디자인 패턴이지만 잘게 쪼개져 있는 구성으로 인해 오히려 유지보수에 불편함을 초래할 수 있음. 또한, 적절하지 않은 분리는 코드의 재사용성에 오히려 악영향을 끼칠 수 있음

- 따라서 특정 디자인 패턴이 좋다라기 보다는 프로젝트와 개발 구성원을 고려하여 어떤 패턴을 도입하여야 효율적인 결과를 만들 수 있을지에 대해 고민할 필요가 있음
- 필요에 따라 여러 디자인 패턴을 함께 사용할 수도 있고, 디자인 패턴에 따라 생겨나는 폴더/파일 종류와 패키지 구조도 달라지기 때문임


# 7. Styled-components ~ Atomic Design

> [Atomic Design Pattern에 대해 알아보기](./간단하게_알아보는_React_디자인_패턴.md##3.Atomic)

- styled-components와 Atomic Design Pattern을 함께 사용하면 다음과 같은 효율적인 점을 얻을 수 있음

1. 재사용성과 일관성
- 아토믹 디자인은 UI 요소를 작은 단위로 분해하고 조합하는 방식을 채택함
- 이는 재사용 가능한 구성 요소를 생성하고 일관성 있는 UI를 유지할 수 있도록 도와줌
- styled-components를 사용하면 각각의 아토믹 단위를 스타일 컴포넌트로 정의할 수 있으며, 이를 조합하여 원하는 형태의 UI 컴포넌트를 구성할 수 있음. 이는 코드의 재사용성과 유지보수성을 높여줌

2. 스타일 관리 용이성
- styled-components는 컴포넌트 단위로 스타일을 정의하고 적용하는 방식을 제공함. 이는 아토믹 디자인의 철학과 일치하여 UI 구성 요소에 필요한 스타일을 직관적이고 명확하게 정의할 수 있음
- 또한, styled-components는 CSS-in-JS 방식으로 동작하기 때문에 스타일과 관련된 로직을 한 곳에 모아 관리할 수 있음. 이는 스타일의 일관성을 유지하고 스타일 충돌이나 스타일 시트 관리의 어려움을 줄여줌

3. 동적 스타일링
- styled-components는 JavaScript 코드에서 동적으로 스타일을 조장할 수 있는 기능을 제공함
- 이는 아토믹 디자인에서 필요한 다양한 상태나 변형에 따라 동적으로 스타일을 적용할 수 있음을 의미함
- 예를 들어, 프로퍼티를 props로 전달받아 컴포넌트의 스타일을 변경할 수 있음. 이는 컴포넌트의 다양한 상태에 따라 일관된 디자인을 유지하면서도 동적인 변화를 구현하는 데 효과적임

4. 컴포넌트 기반 접근
- 아토믹 디자인과 styled-components는 모두 컴포넌트 기반 접근을 채택하고 있음. 이는 컴포넌트를 단위로 UI를 구성하고 이를 조합하여 확장 가능하고 유연한 UI를 구축할 수 있음을 의미함
- 두 가지 접근 방식을 함께 사용하면 재사용성, 유지보수성, 확장성 등의 이점을 얻을 수 있음

5. 스타일의 범위 제한
- styled-components를 사용하면 컴포넌트 단위로 스타일이 캡슐화 됨. 즉, 컴포넌트 내에서 정의된 스타일은 해당 컴포넌트에서만 적용됨
- 이는 스타일의 범위를 명확하게 제한하고 다른 컴포넌트에 영향을 주지 않도록 함
- 따라서, 프로젝트 전체에서 스타일 충돌을 방지하고 스타일 관리를 보다 예측 가능하게 할 수 있음

6. CSS 클래스 이름 관리 간소화
- styled-components를 사용하면 CSS 클래스 이름을 직접 관리할 필요가 없음. 각 styled-components는 고유한 클래스 이름을 자동으로 생성하고 적용함
- 이는 클래스 이름 충돌을 예방하고 클래스 이름 관리를 더욱 편하게 만들어 줌

7. 테마 관리
- styled-components는 테마(Theme) 기능을 내장하고 있음. 이를 활용하여 애플리케이션의 테마를 전역으로 관리하고 다양한 컴포넌트에서 일관된 디자인 시스템을 유지할 수 있음
- 테마를 사용하면 색상, 글꼴, 간격 등의 스타일 변수를 통합적으로 관리하고 필요에 따라 동적으로 변경할 수 있음

8. 개발자 경험 개선
- styled-components는 CSS를 JavaScript로 작성하는 형태이기 때문에 JavaScript의 풍부한 문법과 기능을 활용할 수 있음. 이는 개발자에게 더욱 편리한 개발 경험을 제공하고, CSS 작성 시 발생할 수 있는 오류를 줄여줌
- 또한, styled-components는 개발자 도구와의 통합이 용이하며, 컴포넌트 단위로 스타일을 확인하고 디버깅할 수 있음. 이는 개발 생산성을 향상시키고 디자인 시스템을 구축하는 데 도움을 줌