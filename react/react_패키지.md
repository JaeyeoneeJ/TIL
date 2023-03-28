# react-helmet
---
Helmet 컴포넌트는 문서의 head 태그로 가는 direct link이다.


### 설치
---
```bash
npm i react-helmet
# or
yarn add react-helmet
```


### 활용
---
title 태그만 활용할 수 있는 것이 아니라 favicon 교체, css 적용 등 head 태그에서 할 수 있는 모든 것을 할 수 있다.

```javascript
const App = () => {
	return (
		<div>
			<Helmet>
				<title>페이지 제목</title>
				<link rel="icon" href="%PUBLIC_URL%/favicon.ico" />
				...
			</Helmet>
		</div>
	)
}
```

# React-hook-form
![[React-hook-form]]

