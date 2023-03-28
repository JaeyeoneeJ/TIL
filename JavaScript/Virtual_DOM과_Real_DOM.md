## DOM
- Document Object Model, 즉 문서 객체 모델이라는 뜻
- DOM은 문서의 구조화된 표현을 제공하며 프로그래밍 언어가 DOM 구조에 접근할 수 있는 방법을 제공하여 그들이 문서 구조, 스타일, 내용 등을 변경할 수 있게 돕는다.
- DOM은 웹 페이지의 객체 지향 표현이며, JavaScript와 같은 스크립팅 언어를 이용해 DOM을 수정할 수 있다.
- 트리 구조로 되어 있으며 바닐라 JS가 querySelector() 등으로 각 element에 접근할 수 있도록 하는 객체 모델이다.
- 웹 애플리케이션의 전체 UI를 트리 데이터 구조로 나타낸다.

### 특징
- 트리가 거대해지면 속도 이슈가 발생한다.
- 잦은 DOM 업데이트로 오류와 UI에 문제가 발생할 수 있다.
	- 변경을 적용한 문서가 리렌더링 될 경우, 

## Virtual DOM
- 주로 SPA에서 적용되는 개념으로, 하나의 큰 HTML을 Real DOM으로 칭하고 해당 DOM을 컴포넌트 단위로 쪼개서 추상화한 개념이다.
- Real DOM에서 직접 처리하는 것이 아니라 Virtual DOM이 버퍼 역할을 해줌으로서 어떤 부분이 바뀔지를 미리 계산하고 수정 후에 수정된 부분만 업데이트 해준다.
- 한 마디로 자바스크립트로 이루어진 가상의 DOM에서 한 번 렌더링하고, 기존 DOM과 비교한 다음 변화가 필요한 곳만 업데이트 해주는 개념이다.
<img src="https://velog.velcdn.com/images/dudqls5271/post/0a3553f6-0bea-4dab-833d-60a453cd8bd2/image.png" alt="Virtual DOM 개념">

https://developer.mozilla.org/ko/docs/Web/API/Document_Object_Model/Introduction
https://velog.io/@dudqls5271/React-%EB%AC%B8%EC%84%9C