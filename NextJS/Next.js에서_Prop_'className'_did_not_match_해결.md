오늘은 Next.js에서 styled-components를 사용하면서 겪은 문제에 대해 얘기해보고자 한다.

Next.js에서 styled-components를 사용하면 이러한 경고가 뜬다.
<img src="https://user-images.githubusercontent.com/77138259/222355735-c5722fc5-84db-4486-87f2-9c338a88b7ee.png" />

서버와 클라이언트의 클래스 명이 다른 것이 원인이다.
Next.js는 첫 페이지 로드가 SSR로 동작하기 때문에, 서버에서 생성된 컴포넌트와 CSR로 클라이언트에서 생성된 컴포넌트의 클래스명이 서로 달라지게 된다.

Next.js 12버전부터(현재는 13) babel 대신 swc를 사용하여 컴파일하도록 변경되었다.
next.config.js 에서 styledComponents 설정을 해주면 swc를 사용하면서 문제를 해결할 수 있다.
```javascript
// next.config.js

/** @type {import('next').NextConfig} */
const nextConfig = {
	reactStrictMode: true,
	compiler: {
		styledComponents: true,
	},
};

module.exports = nextConfig;
```

물론 next.config.js를 수정하면 서버를 껐다 켜주는 것을 잊지 말자