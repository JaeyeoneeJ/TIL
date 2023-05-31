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
- 컴포넌트 간 의존도가 낮고, Presentation 컴포넌트를 재사용할 수 있음
- 컴포넌트별 역할이 명확하여 코드 구조를 이해하기 쉬움
- state를 여러 컴포넌트에 props로 전달하여 상태를 공유할 수 있음

