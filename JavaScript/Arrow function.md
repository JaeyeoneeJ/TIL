이미 기존에는 function() { } 이 있음에도 불구하고 ES6 이후로 자바스크립트에 함수를 만들 수 있는 문법으로 새롭게 추가된 문법이다.

기존에 함수를 사용하는 이유
- 여러가지 기능을 하는 코드를 한 단어로 묶고 싶을 때
- 여러번 재사용하기 위해
- 입출력 기능을 만들 때

## arrow funciton의 특징
- 입출력 기능이 직관적으로 표현 가능하다.
```javascript
const foo = (x) => {return x * 2}
```
- 파라미터가 하나일 경우, 소괄호 생략이 가능하다.
```javascript
const foo = x => {return x * 2}
```
- 중괄호 생략이 가능하다.
```javascript
const foo = x => x * 2;
```
- this를 사용할 경우, 내부 this가 아닌 외부 this를 그대로 가져온다. 즉, window를 불러온다.
- arrorw funciton은 일반 function과 용도가 완전히 같지 않기 때문에 완벽히 대체할 수 있는 문법이 아니다.
- 내가 예측하던 this 값과 달라질 수 있으므로 사용 시 주의가 필요하다.


<hr>
## ref.
https://codingapple.com/unit/es6-3-arrow-function-why/