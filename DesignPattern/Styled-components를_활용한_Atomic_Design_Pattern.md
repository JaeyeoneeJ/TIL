- 이 장에서 서술할 내용은 React 프로젝트에서 Styled-components 라이브러리를 활용하여 프로젝트에 Atomic Design Pattern을 적용하는 과정을 작성하고자 한다.
- Styled-components에 대한 설명과 장/단점에 대해서는 아래 링크를 참고하기 바람

> [Styled-components에 대해 알아보기](../react/styled-components.md)


## Styled-components ~ Atomic Design

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


## styled-components는 디버깅이 어렵다는데
- styled-components는 스타일을 동적으로 생성하기 위해 임의의 클래스 이름을 생성함. 이로 인해 외부에서 코드를 읽거나 디버깅할 때 일부 어려움을 겼을 수 있음. 그러나 styled-components는 디버깅을 용이하게 하기 위해 몇 가지 도구와 기능을 제공함

1. 개발자 도구에서 component 확인
- 크롬 개발자 도구를 열고 extension으로 react devtools를 설치하면 react 프로젝트일 경우 component 탭이 생성됨. 이 컴포넌트 탭에서 tree 구조로 해당 컴포넌트에 동적으로 생성된 클래스 이름을 확인하고 해당 스타일을 식별할 수 있음
- 만약 displayName 속성을 부여했다면 displayName에 부여한 값이 있을 것임. 이를 통해 개발자 도구에서 컴포넌트의 이름을 식별하기 쉽게 할 수 있음
```jsx
const StyledButton = styled.button`
  /* 스타일 정의 */
`

StyledButton.displayName = "스타일한 버튼"
```

2. CSS 클래스 추적
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


## styled-components - Atomic Design Pattern 적용 예시
