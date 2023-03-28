- 처음 CRA를 설치하는 과정에서 typescript를 설치해줄 수 있음.

## Installation

To start a new Create React App project with [TypeScript](https://www.typescriptlang.org/), you can run:

```
npx create-react-app my-app --template typescript
```

or

```
yarn create react-app my-app --template typescript
```

---
- 중간에 추가할 경우 아래를 참고

## Add

To add [TypeScript](https://www.typescriptlang.org/) to an existing Create React App project, first install it:

```bash
npm install --save typescript @types/node @types/react @types/react-dom @types/jest
```

or

```bash
yarn add typescript @types/node @types/react @types/react-dom @types/jest
```


---
ref. https://create-react-app.dev/docs/adding-typescript/


# 타입스크립트 설치 간 오류
1. index.tsx
```javascript
const root = ReactDOM.createRoot(document.getElementById('root') as HTMLElement);
```
해결 방법은 간단했다. getElementById를 통해 받아오는 객체의 Type을 지정해주면 된다. TypeScript가 데이터 타입을 알아볼 수 있도록 해주는 것. 수정된 코드는 아래와 같고, 핵심은 **as HTMLElement** 다.

기존: document.getElementById('root')
변경: document.getElementById('root') as HTMLElement

2. yarn add 시 tsconfig.json이 없는 경우
- jsconfig.json을 tsconfig.json으로 바꾸거나 tsconfig.json 파일을 생성한다.
- tsconfig.json 초기내용
```javascript
{
	"compilerOptions": {
		"target": "es5",
		"lib": [
			"dom",
			"dom.iterable",
			"esnext"
		],
		"allowJs": true,
		"skipLibCheck": true,
		"esModuleInterop": true,
		"allowSyntheticDefaultImports": true,
		"strict": true,
		"forceConsistentCasingInFileNames": true,
		"noFallthroughCasesInSwitch": true,
		"module": "esnext",
		"moduleResolution": "node",
		"resolveJsonModule": true,
		"isolatedModules": true,
		"noEmit": true,
		"jsx": "react-jsx"
	},
	"include": [
		"src"
	]
}
```


# 타입스크립트를 지원하는 패키지 설치

```bash
npm i --save-dev @types/{패키지 이름}

# 예시
npm i --save-dev @types/react-query  
```

or

```bash
yarn add @types/{패키지 이름}

# 예시
yarn add @types/react-query  
```

