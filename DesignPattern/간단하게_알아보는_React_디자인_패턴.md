> 본문은 [NEXTREE에서 작성한 'React 디자인 패턴'의 기술 블로그](https://www.nextree.io/react-design-pattern/)를 인용하여 조금씩 수정하고 살을 붙여 작성한 자료입니다.

## > 개요
- 디자인 패턴이란 프로그램을 개발하는 과정에서 사용되는 설계 패턴들을 정의한 것임
- 과거의 웹 사이트와 비교했을 때, 현재는 화면 작업이 훨씬 복잡해졌고 React, Vue와 같은 개발 프레임워크를 활용하여 **컴포넌트 단위로 웹 페이지를 개발**하고 있음
- 더욱 효율적인 웹사이트 구축이나 유지 보수를 위해 **컴포넌트 단위의 활용**이 중요하게 되었는데, 이에 컴포넌트를 어떻게 구성할 것인가에 대한 고민이 더해지게 되었음
- 다양한 디자인 패턴이 있으며 어떤 디자인 패턴을 프로젝트에 적용할 것인지 각 프로젝트의 특성에 맞게 고려해야 함

## 1. Presentation & Container
- React 디자인 패턴 중 가장 기본적인 패턴으로, 데이터 로직을 수행하는 Container 컴포넌트와 데이터를 출력하는 Presentation 컴포넌트를 분리하여 구현하는 디자인 패턴임
- 역할별 컴포넌트 분리로 인해 각각의 명확한 기능과 책임을 갖게 됨. 만약 컴포넌트를 분리하지 않고 하나의 컴포넌트 안에 로직, View를 구현하는 코드가 모두 있게 되면 컴포넌트 재사용이 어렵고 컴포넌트 의존도가 높아지게 됨

### 1) Container 컴포넌트
- API 호출, State 관리, Event 처리 등의 작업을 수행하는 컴포넌트
- 변경된 상태 값을 props를 통해 Presentation 컴포넌트로 전달함

### 2) Presentation 컴포넌트
- UI를 표시하는 컴포넌트
- 직접 상태값을 관리하지 않고 Container 컴포넌트가 전달해준 props를 받아 출력함
<img src="https://github.com/JaeyeoneeJ/TIL/assets/77138259/11ba88d4-60e2-4353-aabb-9ce8e8d94dd7" alt="Presentation & Container Pattern" />
<p style="margin:0" align="right"><a href="https://www.nextree.io/content/images/size/w1000/2023/02/Untitled.png">출처: NEXTREE Blog</a></p>

- 코드를 분리한다는 것이 복잡하게 느껴질 수 있지만, 위 이미지처럼 하나의 컴포넌트를 간단히 하나의 컴포넌트가 Logic을 담당하는 컴포넌트와 UI를 담당하는 컴포넌트로 분리된다고 이해하면 쉬움
- 다음 코드를 보며 어떤 방식으로 코드를 분리하는지 확인해보자

### 3) 패턴 적용 예시
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

### 4) Presentation & Container 패턴 특징
- Presentation 컴포넌트를 재사용할 수 있음. 로직이 포함되어 있지 않기 때문에 다른 컴포넌트와의 의존도가 낮아 다양한 container와 조합해 재사용할 수 있음
- 컴포넌트별 역할이 명확(앱 기능과 UI에 대한 구분이 쉬움)하여 코드 구조를 이해하기 쉬움
- 같은 state를 여러 Presentation 컴포넌트에 props로 전달하여 상태를 공유할 수 있음

### 5) 그러나
- 그러나 이 패턴(Presentation & Container)의 창시자인 'Dan Abramov'는 더 이상 이렇게 구성 요소를 분할하지 않는 것이 좋겠다고 언급함
- React에서 Hooks의 개념이 2019년에 생겨나면서 Hooks를 사용하면 임의의 분할 없이 동일한 작업(Hook은 로직과 표현(UI)의 분리를 가능하게 함)을 수행할 수 있고, 기존 Presentation & Container 패턴을 사용하는 경우가 필요에 의해서보다 맹복적으로 사용하는 경우가 많았기 때문이라고 설명함

## 2. Custom Hooks
- 기존의 Presentation & Container 디자인 패턴과 달리 컴포넌트에서 **로직을 hooks로 분리하여 관리**하는 디자인 패턴임
- 기존의 디자인에서는 다른 Container들에서 공통의 로직을 사용할 경우, 각각 정의해주어야 했으나 hooks로 로직을 분리함으로써 **로직의 재사용이 가능**하게 됨

<img src="https://github.com/JaeyeoneeJ/TIL/assets/77138259/00c5171d-f230-482c-8feb-7c48be584955" alt="Presentation & Container Pattern" />
<p style="margin:0" align="right"><a href="https://www.nextree.io/content/images/size/w1000/2023/02/Untitled-1.png">출처: NEXTREE Blog</a></p>

- 위 이미지처럼 UI는 다르지만 동일한 로직을 사용하는 컴포넌트가 두 개 있다고 가정해보자
- 기존에는 각 컴포넌트 내에 로직을 선언했지만 로직을 별도의 파일로 분리하고 각각의 컴포넌트에서 hooks를 호출한다면, 로직 코드를 중복으로 선언할 필요가 없음
- 이해를 위해 Presentation & Container 패턴에서 작성한 SearchForm 예시 컴포넌트를 활용하여 Custom Hooks 패턴을 적용해보자

### 1) 패턴 적용 예시
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

### 2) Custom Hooks 디자인 패턴 특징
- 여러 컴포넌트에서 동일한 로직을 공유할 수 있음
- 컴포넌트의 제어가 쉬워지고 사용자가 더 많은 통제권을 가질 수 있음
- 로직이 렌더링과 분리되어 있어 이를 올바르게 연결하려면 컴포넌트의 동작 방식에 대한 깊은 이해가 필요함

## 3. Atomic
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


<hr>
## ref.
- https://www.nextree.io/react-design-pattern/
- https://velog.io/@ouo_yoonk/presentational-container-%ED%8C%A8%ED%84%B4
- https://ghost4551.tistory.com/255
- https://fe-developers.kakaoent.com/2022/220505-how-page-part-use-atomic-design-system/