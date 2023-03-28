## Vercel
- Vercel은 Next.js에서 공식적으로 제공하는 호스팅 사이트로 [빌드 + 배포 + 호스팅] 서비스를 제공한다.
- Next.js에 최적화되어 있어 개인 프로젝트 등을 Vercel을 통해 배포하기에 좋다.

## 배포하기
### 1) deploy 추가
- 배포하고자 하는 프로젝트의 package.json 파일에 scripts 부분을 변경해주어야 한다.

```json
// package.json
{
	...
	"scripts": {
		"dev": "next dev",
		"build": "next build",
		"start": "next start",
		"lint": "next lint",
		"vercel-deploy": "next build && next export" // 이 부분 추가
	},
	...
}
```

### 2) Vercel에 Git Repository 추가
- vercel에 접속해 new project 버튼을 클릭하면 나와 연결된 github의 레포지토리 리스트가 나온다.
- 이 중 배포하고자 하는 프로젝트(나의 경우, NextJS로 만든)의 import 버튼을 누른다.

### 3) 배포하기
<img src="https://user-images.githubusercontent.com/77138259/222339176-212a82dd-b5e8-480f-8f92-09dced01a4f0.png" />
- Framework Preset에 Next.js로 들어와 있는 것을 확인하고 Deploy를 누른다.
- 만일 환경변수가 필요하다면 Environment Variables에서 설정할 수 있다.

## 주의사항
- 나의 경우, 몇 가지 오류가 떠서 공유하고자 한다.
- 친절하게도, Deploy 중 Error는 어느 컴포넌트의 몇번째 줄에서 났는지 알려주기 때문에 처리하는데 어려움이 없었다.

### 1) 특수문자 처리
- ", ' 등 이러한 특수문자는 html 앤티티 코드로 변경해서 넣어야만 deploy가 정상적으로 진행된다.
- 나의 경우, 2가지 문제가 생겨 아래와 같이 변경해주었다.
	- " => &quot;
	- ' => &apos;
<a href="https://aneok.tistory.com/86">HTML 자주 사용하는 특수문자 코드표</a>


<hr>
## ref.
- https://nextjs.org/docs/deployment#managed-nextjs-with-vercel