리액트에서 라이프사이클(생명주기)의 개념은 클래스 컴포넌트를 사용할 때 명확히 사용되는 부분이다.
React 16.8 이후 리액트 훅(함수형 컴포넌트)의 개념이 등장하면서 생명주기의 개념도 이전처럼 명확하게 구분되어 사용되진 않는다.

하지만, React에서는 공식적으로 클래스 컴포넌트와 함수형 컴포넌트 모두 지원할 것이라고 발표했고, 이전에 작성된 코드들이 대부분 클래스 컴포넌트로 작성되어 있기 때문에 우리는 리액트의 라이프사이클을 알아야 한다.

또한 함수형 컴포넌트에서도 리액트 라이프사이클을 사용하고 있기 때문에 올바른 프로그래밍을 위해 라이프사이클 개념을 익혀야만 한다.

## 라이프사이클이란
React는 컴포넌트 기반의 View를 중심으로 한 라이브러리이다. 때문에 각각의 컴포넌트에는 라이프사이클(생명주기) 즉, 컴포넌트의 수명 주기가 존재하는데 컴포넌트의 수명은 보통 페이지에서 렌더링되기 전인 준비과정에서 시작하여 페이지가 사라질 때 끝이 난다.
라이프 사이클은 크게 세 가지 유형으로 분류할 수 있으며 각각 생성될 때, 업데이트할 때, 제거할 때로 구분할 수 있다.
주의할 부분은 바로 업데이트 부분이며, 업데이트는 다음과 같은 4가지 상황에서 발생한다.
- props가 바뀔 때
- state가 바뀔 때
- 부모 컴포넌트가 리렌더링 될 때
- this.forceUpdate로 강제로 렌더링을 트리거할 때

## 마운트
### constructor
constructor(생성자)는 컴포넌트를 만들면서 가장 먼저 실행되는 메서드로 초기 state 값을 정할 수 있다.
- 클래스형: 초기 state를 정할 때, constructor를 사용해야 한다.
- 함수형: useState hook을 사용하여 초기 상태를 설정해줄 수 있다.
```javascript
// Class
class Example extends React.Component {
  constructor(props) {
    super(props);
      this.state = { count: 0 };
}

// Hooks
const Example = () => {
  const [count,setCount] = useState(0);
}
```

### render
가장 기초적인 메서드이며, 가장 중요한 메서드이다. 컴포넌트를 렌더링할 때 필요한 메서드로 유일한 필수 메서드이기도 하다.
함수형 컴포넌트에서는 render를 쓰지 않고 컴포넌트를 렌더링할 수 있다.
```javascript
// Class
class Example extends React.Component {
	render() {
		return <div>컴포넌트</div>
	}
}

// Hooks
const example = () => {
	return <div>컴포넌트</div>
}
```

### componentDidMount
컴포넌트의 첫 번째 렌더링이 마치고 나면 호출되는 메서드로 이 메서드가 호출되는 시점에는 만든 컴포넌트가 화면에 나타난 상태이다.
여기선 외부 라이브러리를 연동하거나 해당 컴포넌트에서 필요로 하는 데이터를 요청하기 위해 axios, fetch 등을 통하여 데이터를 요청하거나 DOM의 속성을 읽거나 직접 변경하는 작업을 진행한다.
함수형 컴포넌트에서는 useEffect를 활용하여 구현할 수 있으며, 이 때 의존성 배열([ ])을 비워야 componentDidMount 컴포넌트와 똑같은 메서드를 구현할 수 있다.
```javascript

// Class
class Example extends React.Component {
	componentDidMount() {
		...
	}
}

// Hooks
const Example = () => {
	useEffect(() => {
		...
	}, []);
}
```

... 작성 중

https://whales.tistory.com/m/146