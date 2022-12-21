Promise를 사용하면 여러가지 문제점이 발생한다.
- 동일한 이름의 메서드인 then()을 연쇄적으로 호출할 경우, 몇 번째 then()에서 문제가 발생한 건지 파악하기 어렵다.
- catch() 메서드를 사용하여 예외처리하기 때문에, 동기 코드와 비동기 코드가 섞여 있을 경우, 예외처리가 난해해지거나 예외처리를 누락하는 경우가 생기기 쉽다.
- 복잡한 구조의 비동기 코드를 작성할 경우, then() 메서드의 인자로 넘기는 콜백함수를 사용하면 코드 가독성이 점점 떨어진다.

## async
- async가 붙은 함수는 반드시 Promise를 반환하고, Promise가 아닌 것은 Promise로 감싸 반환한다.

## await
- await는 async 함수 안에서만 동작한다.
- JavaScript는 await 키워드를 만나면 Promise가 처리될 때까지 기다리며 결과는 그 이후 반환된다.
- Promise가 처리되길 기다리는 동안 엔진이 다른일(다른 스크립트를 실행, 이벤트 처리 등)을 할 수 있기 때문에 CPU 리소스가 낭비되지 않는다.
- promise.then 보다 좀 더 세련되게 Promise의 result 값을 얻을 수 있도록 해주는 문법으로 보다 가독성이 좋고 쓰기도 쉽다.


<hr>
## ref.
https://ko.javascript.info/async-await