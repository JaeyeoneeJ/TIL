- Next.js는 기본적으로 페이지를 SSR을 이용해 pre-rendering한다.
- Server Side에서 html 파일을 구성하여 브라우저 측에 전달해 렌더링한다.
  (이 과정에서 CSS도 렌더링 된다.)
- 이후 JS 파일이 로드되어 자바스크립트 코드가 적용된다.

## CSS-in-JS와 Pre-Rendering
- 자바스크립트 코드가 적용되지 않은 페이지가 미리 렌더링되기 때문에 CSS-in-JS로 스타일링을 하면 스타일이 적용되지 않은 html 코드가 먼저 렌더링 되는 문제가 발생하게 된다.
- NextJS는 이에 대한 해결책으로 html 파일에 CSS-in-JS 형식으로 작성된 스타일 요소들을 주입시켜 스타일이 뒤늦게 적용되는 문제를 해결할 수 있다.

```jsx
// _document.tsx

import Document, { DocumentContext } from "next/document";
import { ServerStyleSheet } from "styled-components";

export default class MyDocument extends Document {
	static async getInitialProps(ctx: DocumentContext) {
		const sheet = new ServerStyleSheet();
		const originalRenderPage = ctx.renderPage;
		
		try {
			ctx.renderPage = () =>
				originalRenderPage({
					enhanceApp: (App) => (props) =>
						sheet.collectStyles(<App {...props} />),
				});
		
			const initialProps = await Document.getInitialProps(ctx);
			return {
				...initialProps,
				styles: (
					<>
						{initialProps.styles}
						{sheet.getStyleElement()}
					</>
				),
			};
		} finally {
			sheet.seal();
		}
	}
}
```

<hr>
## ref.
https://nextjs.org/docs/advanced-features/custom-document#customizing-renderpage
https://nextjs.org/docs/advanced-features/custom-document