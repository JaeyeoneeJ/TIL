## 1. CSS 전처리기(CSS preprocessor)
- CSS 전처리기는 전처리기 고유의 문법을 통해 CSS를 생성하게 해주는 프로그램임
- 대부분의 전처리기는 순수 CSS에 없는 기능을 가지고 있으며, 이 기능들 덕분에 CSS 구조가 더 읽기 쉬워지고, 유지보수 역시 용이해짐
- 가장 널리 쓰이는 CSS 전처리기에는 Sass, LESS, Stylus, PostCSS 등이 있음

> [MDN | CSS preprocessor](https://developer.mozilla.org/en-US/docs/Glossary/CSS_preprocessor)


## 2. styled-components는 전처리기인가?
- 공식 문서에서 `styled-components`를 하나의 단어로 정의하고 있지는 않으며, 다른 자료를 보면 'Styled-components는 CSS-in-JS 스타일링 프레임워크'라고 설명하고 있음(또다른 CSS-in-JS 라이브러리/프레임워크 중에는 `emotion`이 있음)
- styled-components는 stylis라는 CSS 전처리기를 사용함. stylis는 자동 중첩 스타일에 필요한 SCSS 유사 문법을 지원함
- 따라서 styled-components 자체로는 CSS 전처리기라고 정의할 수는 없어 보임

> [Benefits of using styled-components in React](https://blog.logrocket.com/benefits-using-styled-components-react/)  
> [Styled Components, Styled Systems and How They Work](https://rangle.io/blog/styled-components-styled-systems-and-how-they-work/)


## 3. Sass와 styled-components의 특징
### 1) Sass

1. 장점
    - variables, nesting, mixins 등의 강력한 기능을 갖고 있음
    - 추후 번들링하면 파일 별로 나누어 작성할 수 있으므로 깔끔하고 유지보수가 쉬움
    - 자유도가 높음

2. 단점
    - 자유도가 높은 만큼 복잡한 코드가 작성될 수 있어서 주니어 개발자나 백엔드가 개발자가 협업 시 건드리기 어려워질 수 있음
    - 관심사의 분리라고는 하나 마크업과 스타일링 파일을 오가며 작성하는 것이 불편함
    - 네이밍에 대한 고민에서 벗어날 수 없음

### 2) styled-components(CSS-in-JS)

1. 장점
    - 네이밍과 화면 전환 문제를 해결
    - 한 화면에 작성되므로 스타일링의 스코프가 특정 컴포넌트로 제한되며, 유지보수도 쉬움. 이는 작은 단위의 재사용 가능한 컴포넌트 작성 시 이상적인 형태이기도 함
    - CSS 코드가 JS 코드 내에 있으므로 자바스크립트 기능을 스타일링에 적용할 수 있음. 이는 CSS-in-JS의 가장 큰 장점임

2. 단점
    - 이전에는 퍼포먼스 이슈가 늘 회자되었음. 그러나 v5 릴리즈와 동시에 퍼포먼스와 번들 사이즈가 크게 향상되었다고 알려짐

> [Sass vs Tailwind CSS vs Styled-components: A CSS methodology comparison](https://www.reddit.com/r/reactjs/comments/my6dnw/styled_components_vs_sass_sheets/)


<hr>
## ref.
- https://velog.io/@jheeju/CSS-%EC%A0%84%EC%B2%98%EB%A6%AC%EA%B8%B0-Sass-styled-component