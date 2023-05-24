## Postman이란
- Postman은 API 개발 및 테스트를 위한 인기 있는 도구임. 개발자는 Postman을 사용하여 API 요청을 만들고, 응답을 검사하고, 테스트 환경을 관리하고, API 문서를 생성할 수 있음
- Postman은 사용자 친화적인 인터페이스를 제공하며, 다양한 기능을 갖추고 있음. 사용자는 요청을 보내기 위해 HTTP 메서드, URL, 헤더, 바디 등을 구성할 수 있으며, 인증 정보를 추가하고, 요청을 자동화하고, 테스트 스크립트를 작성할 수도 있습니다. 응답은 시각적으로 표시되며, 사용자는 응답 데이터를 검사하고 분석할 수 있음
- Postman은 개발자들이 API를 빠르게 프로토타이핑하고 테스트하는 데 도움이 되는 강력한 기능을 제공함. 또한 팀과의 협업을 위해 API 문서를 작성하고 공유할 수 있으며, 테스트 환경을 공유하고 관리할 수도 있음
- Postman 클라우드 서비스를 통해 팀원과의 협업, API 문서 공유, 히스토리 및 동기화 기능을 활용할 수 있음

## Postman 설치
- 포스트맨은 웹 화면에서도 사용이 가능하나 분리성 및 CORS 정책 예외처리 등 편의성을 위해 데스크탑 용 프로그램(앱)을 설치하는 것을 권장함
- 먼저 [홈페이지](https://www.postman.com/)에 접속해 `Sign Up` 버튼을 클릭하여 회원가입 진행
- [다운로드 페이지](https://www.postman.com/downloads/)로 이동하여 본인의 환경에 맞는 버전을 다운로드
> Postman은 크로스 플랫폼을 지원하며, Windows, macOS, Linux에서 사용할 수 있음

- 설치가 완료되었다면 아까 회원가입한 계정으로 다시 로그인 진행

## Postman 사용법
- Postman을 사용하는 경우는 대체로 프론트엔드의 경우, 화면 구성 전 백엔드 서비스를 테스트하기 위함이며, 백엔드의 경우, 자신이 개발한 서비스를 직접 호출해서 적절한 응답이 나오는지 테스트하기 위함임

- Postman을 실행하면 Home이 뜰 것임
- WorkSpaces 탭의 My Workspace 클릭
- Create a new request -> Http 선택
- Request 창이 생기면 GET 메서드를 선택하고 URL에 api url을 입력. 여기서는 네이버 증권에서 뉴스를 가져오기로 함
- 요청 URL: https://m.stock.naver.com/api/news/list?category=ranknews&page=1&pageSize=5

<img src="https://github.com/JaeyeoneeJ/TIL/assets/77138259/5368f539-a992-49f9-a984-494040a8a6e1" alt="spring initializr 화면 구성" />

- 위의 주소를 입력하면 네이버 증권에서 뉴스 데이터를 5개 가져와 보여주게 됨
- send 버튼을 클릭하면 응답 값(body)과 쿠키, 헤더가 들어오게 됨
- 만약 현재 Http 요청(request) 값을 저장하고 싶다면 Save 버튼을 클릭하여 저장하면 되며, 사용 용도에 따라 Collection을 활용하여 그룹을 생성하면 됨



<hr>
## ref.
- https://learning.postman.com/docs/introduction/overview/
- https://nhj12311.tistory.com/393