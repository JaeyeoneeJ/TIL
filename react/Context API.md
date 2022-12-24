## Context란
Context는 리액트 컴포넌트 간에 어떠한 값을 공유할 수 있게 해주는 기능이다.
주로 Context는 전역(global)으로 필요한 값을 다룰 때 사용하나 꼭 전역일 필요는 없다.
Context는 재사용성이 높은 컴포넌트를 만들 때에도 매우 유용하므로 Context를 단순히 리액트 컴포넌트에서 Props가 아닌 또 다른 방식으로 컴포넌트 간에 값을 전달하는 방법이라고 접근하는 것이 좋다.

컴포넌트에 Props로 데이터를 전달해주어야 할 경우, 여러 컴포넌트를 거치면 계속 Props를 넘겨주어야하기 때문에 불편하고 실수할 가능성이 높아진다.(Props Drilling 발생)

Context는 리액트 패키지에서 createContext라는 함수를 불러와서 만들 수 있으며, Context 객체 내 Provider 컴포넌트에 공유하고자 하는 값을 value라는 Props로 설정하면 자식 컴포넌트들이 useContext를 사용하여 Context에 넣은 값에 바로 접근할 수 있어 Props Drilling을 막을 수 있고 코드를 가독성 있게 표현할 수 있다.


<hr>
## ref.
https://velog.io/@velopert/react-context-tutorial