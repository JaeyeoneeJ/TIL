- 구형 브라우저에서 몇 가지 테스트를 하던 중, `Enter` 키의 입력이 `Click` 이벤트로 동작하지 않는 현상을 발견했다.
- 몇 가지 테스트를 거쳐 해결한 방법을 아래 작성해본다.


## 1. Focus의 문제
- 참으로 당연한 얘기이며, 어쩌면 구형 브라우저 뿐 아닌 모든 상황에서 가장 많이 실수하는 경우가 이 경우가 아닐까 싶다.
- `Enter` 키로 `click` 이벤트를 트리거하려면 해당 'element'에 포커스가 있어야 한다. `enter` 키는 현재 포커스된 'element'에서 발생한 키보드 이벤트로 처리되기 때문이다.
- 'element'에 포커스가 있지 않다면 `enter` 이벤트가 해당 'element'로 전달되지 않는다.


## 2. Element Type의 문제
- 일반적으로 `click` 이벤트는 대부분의 HTML 요소에서 동작한다. 그러나 몇 가지 요소는 `click` 이벤트를 기본적으로 지원하지 않을 수 있다. 예를 들어, `<div>`와 `<button>` 같은 대부분의 클릭 가능한 요소들은 `click` 이벤트를 지원한다. 그러나 `<span>`과 같은 일부 요소들은 특별한 스타일이나 속성이 없는 한 `click` 이벤트를 처리하지 않을 수 있다.
- 그러나 몇 가지 예외적인 상황이나 특정 브라우저의 오래된 버전에서는 위와 같이 기본적으로 지원하는 요소의 이벤트에 문제가 발생할 수 있다.
- 이에 대한 대비책으로 몇 가지 대안이 있다.

### 1) button 요소 사용
- 클릭 가능한 'element'로 `<button>` 요소를 사용하는 것이 안전한 방법 중 하나이다.
- `<button>`은 클릭 가능한 요소로 인식되며, 대부분의 브라우저에서 기본적인 동작을 지원한다.
```js
<button onClick={handleClick}>Click me</button>
```

### 2) role 속성 추가
- 클릭 가능한 'element'에 `role` 속성을 추가하여 역할을 명시적으로 정의할 수 있다.
```js
<div
  role="button"
  tabIndex={0}
  onClick={handleClick}
>
  Click me
</div>
```
- 위와 같은 방법은 `<div>` 'element'를 클릭 가능한 버튼으로 인식하도록 돕는다. 즉, `<div>`의 역할이 'button'으로 인식하도록 한다.
- 여기서 `tabIndex` 속성은 사용자가 키보드로 요소를 탐색할 때의 순서를 결정하는 데 사용된다. `tabIndex`를 포함한 'element'는 탭 키를 사용하여 순서대로 포커스된다.
- `tabIndex={0}`은 해당 요소를 자연스러운 탭 순서로 포커스하도록 한다. 음수 값은 탭 순서를 가지지 않는 요소로 간주되며, 양수 값은 탭 순서를 지정한다. `tabIndex={0}`을 사용하면 해당 요소는 문서의 일반적인 흐름대로 탭 순서에 참여하게 된다.
- 따라서 `tabIndex`를 추가하여 클릭 가능한 요소가 탭 키로도 탐색될 수 있도록 하는 것이 키보드 접근성을 향상시키는 데 도움이 된다.


## 3. 전역 이벤트 핸들러 설정
- 만약 모든 컴포넌트에서 `Enter`키를 입력했을 때, 각 컴포넌트가 `onClick` 이벤트를 가지고 있을 수 있다면, 해당 이벤트를 실행하기 위해 이벤트 핸들러를 전역으로 설정하는 방법이 있다.
- 즉, 이벤트 실행을 Override 하는 방법이다.

```jsx
// App.js

useEffect(() => {
  const enterClick = (event) => {
    if (event.keyCode === 13) {
      // 포커스된 요소가 있을 때
      if (
        document.activeElement &&
        document.activeElement !== document.body &&
        document.activeElement.click
      ) {
        // 포커스된 요소의 click 이벤트 실행
        document.activeElement.click();
      }
    }
  };
  document.addEventListener("keydown", enterClick);

  return () => {
    document.removeEventListener("keydown", enterClick);
  };
}, []);
```
- 하나씩 따라가보자.

### 1) 'enter' 키 입력에 대한 설정
- `Enter` 키 입력에 대한 이벤트 핸들러를 설정한다.
```js
  const enterClick = (event) => {
    if (event.keyCode === 13) {
      ...
    }
  };
```
- 여기서 `event.keyCode === 13`은 `Enter` 키 입력을 뜻한다.
- 일반적으로 최신 브라우저에서는 `event.key`를 지원하는데, `event.keyCode` 보다 명시적으로 해당 키 값이 무엇인지 알 수 있다. 예를 들어 `Enter` 키 입력 시, `event.keyCode === 13` 이라면, `event.key === 'Enter'` 라고 출력된다.
- 하지만 일부 구형 브라우저에서는 `event.key`가 지원되지 않을 수 있으므로 `event.keyCode`를 사용했다.

### 2) 포커스 된 요소 추적
```js
  const enterClick = (event) => {
    if (event.keyCode === 13) {
      // 포커스된 요소가 있을 때
      if (
        document.activeElement &&
        document.activeElement !== document.body &&
        document.activeElement.click
      ) {
        // 포커스된 요소의 click 이벤트 실행
        document.activeElement.click();
      }
    }
  };
```
- `document.activeElement`는 현재 포커스된 요소를 반환하는 JavaScript의 속성이다. 이 속성을 사용하면 현재 사용자가 상호 작용하는 동안 활성화된 요소를 쉽게 식별할 수 있다.
- 만일 활성화된 요소가 `document.body`일 수 있어 해당 경우를 제외했다.
- 현재 포커스된 요소가 click 속성을 가지고 있을 때 해당 요소의 click 이벤트를 실행하도록 하였다.

### 3) 전역 이벤트 핸들러 설정
- 모든 컴포넌트에서 `Enter` 키 입력에 대한 이벤트를 추적하도록 전역 이벤트 핸들러를 설정한다.

```jsx
useEffect(() => {
  const enterClick = (event) => {
    ...
  };
  document.addEventListener("keydown", enterClick);

  return () => {
    document.removeEventListener("keydown", enterClick);
  };
}, []);
```
- `App.js` 파일에서 `useEffect`로 컴포넌트가 마운트 시 전역 이벤트 핸들러를 붙여주고, 언마운트 시 이벤트를 제거해주는 것으로 했다.
- `App.js`에서는 `useEffect`에서 언마운트 시 이벤트 핸들러를 제거하는 것이 필요 없을 수 있으나, 다른 컴포넌트에서도 사용할 수 있으므로 해당 케이스를 추가해봤다.