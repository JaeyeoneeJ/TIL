이전에는 비동기 작업을 처리할 때에는 주로 콜백 함수를 다른 함수의 인자로 넘겨서 비동기 처리를 했으나, 비동기 작업이 많아질 경우 코드 가독성이 현저하게 떨어지게 되며 이를 소위 콜백지옥이라고 부르며, 이 문제를 해결하기 위한 방법 중 하나가 Promise이다.

프로미스는 비동기 작업을 조금 더 편하게 처리할 수 있도록 ES6에 도입된 기능이다.
Promise는 현재에는 당장 얻을 수는 없지만 가까운 미래에는 얻을 수 있는 어떤 데이터에 접근하기 위한 방법을 제공한다.

Promise는 then()과 catch() 메서드를 통해 동기 처리에서 사용하는 try-catch 블록과 유사한 방법으로 비동기 처리 코드를 작성할 수 있도록 한다.

<hr>
## ref.
https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise
https://www.daleseo.com/js-async-promise/