## REST란
- Representaltional State Transfer의 약자로 자원을 이름(자원의 표현)으로 구분하여 해당 자원의 상태(정보)를 주고 받는 모든 것을 의미한다.
- 즉, 자원(resource)의 표현(represetation)에 의한 상태 전달
	- 자원: 해당 소프트웨어가  관리하는 모든 것
		- ex) 문서, 그림, 데이터, 해당 소프트웨어 자체 등
	- 자원의 표현: 그 자원을 표현하기 위한 이름
		- ex) DB의 학생 정보가 자원일 때, 'students'를 자원의 표현으로 정한다.
- 상태(정보) 전달
	- 데이터가 요청되어지는 시점에서 자원의 상태(정보)를 전달한다.
	- JSON 또는 XML을 통해 데이터를 주고 받는 것이 일반적이다.
- HTTP URI(Uniform Resource Identifier)를 통해 자원(Resource)을 명시하고, HTTP Method(POST, GET, PUT, DELETE)를 통해 해당 자원에 대한 CRUD Operation을 적용하는 것을 의미한다.
- CRUD Operation
	- Create: 생성(POST)
	- Read: 조회(GET)
	- Update: 수정(PUT)
	- Delete: 삭제(DELETE)
	- HEAD: header 정보 조회(HEAD)

<hr>
## ref.
https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html