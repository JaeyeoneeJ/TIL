## 1. LESS란
- less는 Leaner Style Sheets의 약자이자, '더 적은'이라는 의미이기도 하다.
- CSS를 더 간결하고 쉽게 작성할 수 있음을 어필하는 이름인 듯 하다.
- 실제로 LESS의 슬로건은 `It's CSS, with just a little more.`이다.
- less는 CSS에 Script 기능(변수, 함수, 연산, 중첩, 스코프 등)을 덧붙여 확장한 언어로 클라이언트 또는 서버 환경(node.js 등) 모두에서 실행된다.
- less는 CSS preprocessor(전처리기)로서 CSS를 변수나 Nested Rules을 이용하여 쉽고 빠르게 체계적으로 프로그래밍할 수 있게 만든 것을 말한다.
- less는 CSS의 확장버전으로 하위호환성이 뛰어나며 CSS의 기존 문법을 그대로 사용하기 때문에 익히기 쉬운 편이다.

> CSS 전처리기에 대한 내용은 [CSS 전처리기와 styled-components](./CSS_전처리기와_styled-components.md) 참조

## 2. LESS 사용 예
### 1) 변수
- 변수를 사용하면 여러번 사용되는 값을 재사용할 수 있다.
- 따라서 공통으로 들어가는 색상이나 width 값 등을 변수로 지정한 후 이후 변경점이 발생했을 때(공통 색상 변경 등), 해당 변수 줄 한 줄만을 수정하여 적용할 수 있다.
```less
@POINT_COLOR: #436755;

h3 {
	color: @POINT_COLOR;
}

#header {
	color: @POINT_COLOR;
}

.title {
	color: @POINT_COLOR;
}
```

- 위처럼 일반 태그, id, class 모두 적용할 수 있다.

### 2) Mixins
- Mixins는 한 클래스 안에서 하나의 속성 이름으로 지정하는 방식을 통해 다른 클래스의 모든 속성을 포함시킬 수 있도록 해준다.
- 만약, 박스의 외부를 둥글게 만들고 싶다면
```less
// 선언
.rounded-corners (@radius: 5px) {
	border-radius: @radius;
	-webkit-border-radius: @radius;
	-moz-border-radius: @radius;
	-ms-border-radius: @radius;
}

// 사용
.container {
	.rounded-corners; // 기본 5px 적용
}
.contentsBox {
	.rounded-corners(10px); // 10px 적용
}

```
- 위에서 주목할 부분은 Mixins with Parameter(파라미터 믹스인) 이다.
- 위처럼 less Mixins는 매개변수를 넘겨받을 수 있으며, 이를 default 값으로 선언할 수도 있다.
- 따라서 default를 5px로 설정한 후 매개변수를 통해 받는 인자를 받아올 수 있다.

- 물론 여러개의 매개변수를 받을 수도 있다.
```less
// 선언
.font (@fontFamily, @fontSize, @color, @fontWeight) {
	font-family: @fontFamily;
	font-size: @fontSize;
	color: @color;
	font-weight: @fontWeight;
}

// 사용
.font(@fontFamily: "sans-serif" , @fontSize: 20px, @color: @POINT_COLOR, @fontWeight: 400);
```
- 위처럼 font와 관련된 보일러플레이트가 많은 경우, 이를 하나의 mixins로 묶어 사용 가능하다. 물론, key 값을 명시적으로 표현하기 위해 `key: value` 형식으로 사용 가능하다.

### 3) Nesting Rules
- CSS 속성 상속을 위해 긴 선택자(selector; .class.class2 > div > p li { ... })를 만드는 방법 대신, 한 선택자를 다른 선택자 안에 포함시키는 것이 가능하다.
- 또한 네스팅한 범위에 대해 Scope(범위) 원칙을 가진다.
- 일반 프로그래밍 언어와 같이, 변수와 믹스인은 먼저 지역 스코프에서 선언 값을 찾고, 찾을 수 없는 경우 컴파일러는 부모 범위 ~ 전역 스코프 순으로 찾게 된다.
- 이를 통해 CSS 속성 상속을 훨씬 간결하게 할 수 있다.
```less
#header {
	.title {
		.font(@fontSize: 30px, @color: @POINT_COLOR, fontWeight: 700);
		text-align: center;
	}
	.subtitle {
		.font(@fontSize: 20px, @color: darkgray, fontWeight: 400)
		p {
			font-size: 12px;
			a {
				text-decoration: none;
				&:hover {
					border-bottom: 1px solid black;
				}
			}
		}
	}
}
```

- 만약 위 less 파일을 일반 css로 변환한다면 아래와 같을 것이다.
```css
#header .title {
	font-family: @fontFamily;
	font-size: @fontSize;
	color: @color;
	font-weight: @fontWeight;
	text-align: center;
}
#header .subtitle {
	font-family: @fontFamily;
	font-size: @fontSize;
	color: @color;
	font-weight: @fontWeight;
} 
#header .subtitle p {
	font-size: 12px;
}
#header .subtitle a {
	text-decoration: none;
}
#header .subtitle a:hover {
	border-bottom: 1px solid black;
}
```

- 따라서 less를 사용할 경우, 이를 통해 보다 명시적으로 CSS 속성을 파악하고 스타일의 종속성을 관리하기 쉽다.

### 4) 주석
- less 주석은 JavaScript와 같이 `//` 이다. 이는 컴파일 되지 않는다.
- `/* */` 주석만이 CSS에 컴파일되어 출력된다.

### 5) import
- @import를 사용하면 개발단계에서 스타일시트를 여러 파일로 분할하여 관리할 수 있으며, 컴파일 과정에서 하나의 CSS 파일로 합칠 수 있다.
- 이 @import를 통해 다른 .less 파일들을 불러와 변수, 믹스인 등을 가져올 수 있다.
```less
@import "../../style/CommonStyle.module.less";
@import "../../style/utils.module.less";

...
```


## 3. 기타
### 1) 스타일 규칙
- less는 다른 CSS 전처리기와는 다르게 중괄호 `{}`와 세미콜론 `;`을 생략할 수 있는 CSS의 확장 언어 중 하나이다.
- less는 좀 더 간결하고 가독성이 좋은 문법을 지원하기 위해 위와 같은 것을 생략할 수 있도록 설계되었다.
```less
// Less 예제
@primary-color: #3498db;

body
	font-family: 'Arial', sans-serif;

.header
	background-color: @primary-color;
	color: white;
```

### 2) 컴파일
- 일반적으로 less 파일은 일반 CSS 파일보다 조금 더 가벼울 수 있다. 이는 less가 중괄호와 세미콜론을 생략할 수 있는 문법을 사용함으로써 불필요한 기호의 양을 줄일 수 있기 때문이다.
- 물론 이 차이는 매우 작을 수 있으며, 실제로는 큰 차이를 느끼기 어렵다.
- 하지만 less의 가장 큰 강점은 브라우저에 내장된 JS 인터프리터만으로 컴파일이 가능하다는 것이다. 즉, 디펜던시에서 자유롭다.



<hr>
## ref.
- https://lesscss.org/