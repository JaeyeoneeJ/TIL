Next.js를 처음 사용했을 때, 당황스러웠던 점은 window나 document와 같은 전역 객체를 사용하고자 했을 때, 에러가 나는 것이었다.

## 왜 Error가 날까?
- Next.js에서 SSR을 할 때, 페이지를 처음 렌더링하는 과정에서는 window나 document와 같은 전역 객체가 존재하지 않는다.
- 즉, 클라이언트에서는 존재하지만 서버에서는 존재하지 않는다는 것이다.
- 나의 경우, custom hook을 사용하고 있었는데, 여기서 문제가 발생했다.
```jsx
function getWindowWidth() {
	const width = window.innerWidth;
	return width;
}

function useWindowWidth() {
	...
}

export default useWindowWidth;
```
- 위의 경우를 보면, window가 아직 정의되지 않았다고 뜬다.

## 해결 방법
### 1) typeof 연산자
- window 객체의 존재여부를 파악하는 것이다.
- 위 예제를 활용해서 이렇게 해결하였다.
```jsx
function getWindowWidth() {
	if (typeof window !== "undefined") {
		const width = window.innerWidth;
		return width;
	} else {
		const width = 0;
		return width;
	}
}

function useWindowWidth() {
	...
}

export default useWindowWidth;
```

### 2) useEffect
- useEffect는 렌더링 이후에 호출되기 때문에 오직 클라이언트 사이드에서만 동작함을 보장할 수 있으므로 안전하게 전역 객체에 접근이 가능하다.
```jsx
function getWindowWidth() {
	...
}

function useWindowWidth() {
	const [windowWidth, setWindowWidth] = useState<number>(getWindowWidth());
	
	useEffect(() => {
		function handleResize() {
			setWindowWidth(getWindowWidth());
		}
		
		window.addEventListener("resize", handleResize);
		return () => window.removeEventListener("resize", handleResize);
	}, []);
	
	return windowWidth;
}

export default useWindowWidth;
```

### 3) next/dynamic
- Next.js는 ES2020의 dynamic import를 지원한다.
- ssr: false 옵션을 사용하면 SSR 없이 컴포넌트를 동적으로 불러올 수 있다.
```javascript
import dynamic from 'next/dynamic'

const DynamicComponentWithNoSSR = dynamic(
	()=> import('@/components/useWindowWidth'),
	{ ssr: false }
)
```

<hr>
## ref.
https://nextjs.org/docs/advanced-features/dynamic-import