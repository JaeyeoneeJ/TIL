## Introduction
- 웹 페이지를 작업할 때, 정해진 해상도에서 작업하기 보다는 대부분 반응형 웹 페이지로 제작하게 된다.
- 특히, 모바일 기기의 경우, 픽셀이 같아도 각 기기가 가진 해상도가 다르고, 내부에서 폰트의 사이즈를 적용했을 때, 웹 페이지 또는 웹 뷰가 적용된 앱의 폰트 사이즈가 함께 조절되어야 한다.
- `word-break` CSS 속성은 텍스트가 콘텐츠 상자를 넘칠 때마다 줄 바꿈을 표시할지 여부를 설정한다.
- 나의 경우, 글자가 커졌을 때 단어 단위로 줄바꿈이 되었으면 할 때 자주 사용하는 속성이기 때문에 간단한 소개를 하고자 한다.

- 중국어/일본어/한글의 경우 W3C문서에서 **CJK(Chinese, Japanes, Korean 의 약자)**로 사용하고 있으며, 본 문서에서도 **CJK**로 지칭한다.
- 그 외 언어는 **non-CJK(숫자, 영어, 베트남어 등)**으로 지칭한다.
- 중단점은 ‘ · ’로 표기한다.

## Syntax

### Value
1. `normal`
- 기본 줄바꿈 규칙을 사용함

2. `break-all`
- overflow를 방지하려면 두 문자 사이에 단어 분리를 삽입해야 함(중국어/일본어/한국어 텍스트 제외)

3. `keep-all`
- 중국어/일본어/한국어(CJK) 텍스트에는 단어 분리를 사용하면 안 됨
- 비 CJK 텍스트 동작은 `normal`과 동일함

4. `break-word`
- `overflow-wrap` 속성의 실제 값과 관계없이 `word-break: normal`과 결합된 `overflow-wrap: where`와 동일한 효과를 가짐

> Note: `word-break: break-word` 및 `overflow-wrap: break-word`(overflow-wrap 참조)와 달리 `word-break: break-all`은 텍스트가 컨테이너를 넘칠 정확한 위치에 줄 바꿈을 만듬(단어 전체를 한 줄에 넣으면 줄 바꿈이 필요하지 않음)

<hr>
## ref.
- https://developer.mozilla.org/en-US/docs/Web/CSS/word-break
- https://wit.nts-corp.com/2017/07/25/4675