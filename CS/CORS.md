## CORS란
- 교차 출처 리소스 공유(CORS, Cross-Origin Resource Sharing)는 추가 HTTP 헤더를 사용하여, 한 출처에서 실행 중인 웹 애플리케이션이 다른 출처의 선택한 자원에 접근할 수 있는 권한을 부여하도록 브라우저에 알려주는 체제이다.
- 즉, 출처가 다른 자원들을 공유한다는 뜻으로, 한 출처에 있는 자원에서 다른 출처에 있는 자원에 접근하도록 하는 개념이다.
- A 도메인의 프론트 엔드 JS 코드가 XMLHttpRequest를 사용해서 B 도메인의 data.json을 요청하는 경우, 교차 출처 요청이라고 볼 수 있다.
- 타 출처 요청(GET 제외)으로 서버 데이터가 임의로 수정되는 보안 문제 등이 발생할 수 있어 브라우저는 스크립트에서 시작한 교차 출처 HTTP 요청을 제한한다.

## CORS 허용 방법
- Access-Control-Allow-Origin 서버측 응답 헤더 세팅에서 접근 권한을 주는 헤더를 추가하여 해결할 수 있으며, 모든 cross-origin 요청에 대한 응답을 막기 위해 특정 도메인이나 특정 요청에만 응답하게 옵션을 설정할 수 있다.
- 교차 출처 리소스 공유 표준은 웹 브라우저에서 해당 정보를 읽는 것이 허용된 출처를 서버에서 설명할 수 있는 새로운 HTTP 헤더를 추가함으로써 동작한다.
- 예를 들어 서버 데이터를 수정하는 HTTP 요청 메서드(GET 제외)가 발생하면 브라우저는 요청을 서버에 보내기 전, Options 메서드로 프리플라이트하여 지원하는 메서드를 요청하고 서버의 허가가 떨어지면 실제 요청을 보내도록 요구한다.

## mac Chrome Browser CORS
- 허용
```bash
open -na Google\ Chrome --args --user-data-dir=/tmp/temporary-chrome-profile-dir --disable-web-security
```

- 원복
```bash
open -a "Google Chrome" --args --profile-directory=Default --enable-web-security
```


<hr>
## ref.
https://developer.mozilla.org/ko/docs/Web/HTTP/CORS