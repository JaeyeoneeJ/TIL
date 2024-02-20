
## 1. 개요
- 만일 우리가 특정 라이브러리를 사용하고 있고, 라이브러리를 수정할 방법이 없다고 가정하자. 그리고 라이브러리에서 타겟의 이벤트 객체를 사용하고 있지만, 우리가 원하는대로 이벤트 객체의 셋팅이 되지 않는다면 골치 아플 것이다.
- 이 경우, 이벤트 객체를 가로채 원하는 값으로 수정할 수 있다.

>물론 readOnly인 이벤트 객체를 직접 수정하는 것은 권장되지 않는 방법이긴 하다.

- 일반적으로 이벤트 핸들링의 순서는 **이벤트 캡처링, 타겟팅, 이벤트 버블링** 순으로 진행된다.
```js
const modifyEvent = () => {
  ...
}
  
element.addEventListener("keyup", modifyEvent, true);
```
 - 만약 특정 라이브러리가 `keyup` 이벤트를 처리하고 있다면 해당 이벤트 핸들러가 먼저 호출되는 것이 일반적이다. 즉, addEventListner에서 선언한 `modifyEvent` 함수는 라이브러리보다 이후에 호출될 것이다.
 - 이 특정 라이브러리에서 이벤트를 제어할 수 있는 옵션이 있다면 좋겠지만 아닌 경우가 훨씬 많다.

 - 그렇다면, 이벤트 객체가 라이브러리로 전달되기 전에, **이벤트 캡처링** 단계에서 이벤트 객체의 수정본을 전달한다면 어떨까? 그렇다면 라이브러리에서 받은 이벤트 객체는 우리가 커스터마이징한 객체가 전달될 수 있지 않을까?

### 1) Object.defineProperty
 - `Object.defineProperty`는 JavaScript에서 객체의 속성을 정의하거나 수정할 때 사용되는 메서드이다. 이 메서드를 사용하면 객체의 새로운 속성을 정의하거나 이미 존재하는 속성의 속성을 수정할 수 있다.
 - `Object.defineProperty` 메서드는 세 가지 매개변수를 받는다.
	1. **객체 (Object):** 속성을 정의하거나 수정하려는 대상 객체
	2. **속성 이름 (String):** 정의하거나 수정하려는 속성의 이름
	3. **속성 설명자 (Descriptor):** 속성의 특성을 설명하는 객체. 이 객체에는 속성의 값, writable 여부, enumerable 여부, configurable 여부 등이 포함될 수 있음

### 2) element.addEventListener와 useCapture
```js
element.addEventListener(event, handler, useCapture);
```
- `event`: 이벤트 유형을 나타내는 문자열이다.
- `handler`: 이벤트가 발생했을 때 호출되는 함수 또는 객체이다.
- `useCapture`: 이 매개변수가 `true`이면 **이벤트 핸들러가 캡처 단계에서 호출**되고, `false`이면 버블링 단계에서 호출된다.

- 여기서 주목할 부분은 `useCapture`다. 이를 통해 특정한 이벤트가 호출되었을 때, 원하는 함수(`handler`)를 캡처 단계에서 호출할 수 있다. 이렇게 하면 `handler` 함수가 해당 이벤트의 캡처 단계에서 가장 먼저 호출된다.

- 또한 여기서 유의할 점은, 이 세번째 인자는 객체 옵션을 받을 수 있다는 점이다.
```js
element.addEventListener('keyup', yourHandler, {
  capture: true, // 또는 false, useCapture 값에 해당
  passive: true, // 또는 false
});
```

1. **`useCapture` (true 또는 false)**:
    - `true`로 설정할 경우: 이벤트 핸들러가 이벤트의 캡처 단계에서 호출된다.
    - `false`로 설정할 경우: 이벤트 핸들러가 이벤트의 버블링 단계에서 호출된다.
2. **`passive` (true 또는 false)**:
    - `true`로 설정할 경우: 브라우저에게 이벤트에 대한 preventDefault를 수행하지 않을 것임을 알린다. 브라우저는 성능 향상을 위해 이벤트 핸들러가 preventDefault를 호출하지 않을 것으로 가정하고 최적화를 수행할 수 있다.
    - `false`로 설정할 경우: 이벤트 핸들러에서 preventDefault를 호출할 수 있음을 나타낸다. 이 경우 브라우저는 이벤트에 대한 preventDefault 호출을 기다리게 된다.

- 만일 세 번째 인자에 위와 같은 객체가 아닌 `true` 값을 넣었다고 가정하면, `capture: true, passive: false(기본적으로)`가 된다.
- `capture`와 `passive`를 동시에 사용하려면 위와 같이 객체를 이용하여 설정하면 된다.
- 따라서, `useCapture`는 이벤트 핸들러가 호출되는 단계를 지정하는 반면에, `passive`는 브라우저에 대한 preventDefault 동작을 제어한다.


## 2. 코드 작성
```js
... 

if (typeof window === "object") {
  const modifyEvent = (event) => {
    Object.defineProperty(event, "key", {
      get: function () {
        return ...; // 처리할 어떠한 로직
      },
    });
  };
  
  document.addEventListener("keyup", modifyEvent, true);
}

...
```
 - 어떤 이벤트를 전역으로 받기 위해 `element`가 아닌 `document`로 이벤트 리스너를 달았다.
 - 나의 경우, 구형 브라우저에서 `event.key` 를 지원하지 않는 경우가 종종 발생해서 위와 같이 작성해보았다.
 - 이렇게 하면 `modifyEvent` 함수는 다른 라이브러리에 이벤트 객체를 전달하고 실행하기 전에 **이벤트 캡처링** 단계에서 실행되고, 변경된 이벤트 객체를 라이브러리 등 이벤트 객체를 사용하고 있는 곳에 온전히 전달할 수 있게 된다.

### 1) 예시 코드
```js
  if (!("key" in window.KeyboardEvent.prototype)) {
    const getKeyOption = (event) => {
      console.log(
        "KeyDown Event / This browser does not have a 'key' value for KeyboardEvent"
      );

      if (event.keyCode >= 48 && event.keyCode <= 57) {
        Object.defineProperty(event, "key", {
          get: function () {
            return this.keyCode - 48;
          },
        });
      }
    };

    document.addEventListener("keydown", getKeyOption, true);
  }
```
- 나의 경우에는 `<input>` 요소가 아닌 곳에서 숫자 키 입력이 필요하지만 `key`를 받지 못하여 `key` 옵션을 생성하여 가져오게 하는 코드를 작성해보았다.
- 0-9까지의 `keyCode`는 48~59 로, 각 `keyCode`에서 48씩을 빼 `key` 라는 옵션에 추가하여 이벤트 객체를 반환했다.