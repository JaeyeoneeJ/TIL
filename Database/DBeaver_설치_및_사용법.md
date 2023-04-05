- Database를 조작할 때 MySQL Workbench나 shell 등을 써도 되지만 가독성과 편의성을 위한 DB 조작용 프로그램이 있음
- 많이 사용하는 MySQL Workbench의 경우, mac M1 이후 칩에서 설치/실행 오류가 자주 발생하는 편임(사용하는 OS 버전과 일치하는 workbench 버전에서만 동작 등)
- 나는 mac m1 칩을 쓰고 있으므로 DBeaver를 설치하겠음

## URL
https://dbeaver.io/download/

## 설치
- 위의 링크를 클릭해서 각자 OS 환경에 맞게 설치
- DBeaver Community 버전을 설치
- mac 유저의 경우, m1 등의 환경에서 반드시 Apple Silicon 버전을 다운로드 할 것

## 실행
- DBeaver를 실행하면 sample database를 생성할 것인지 물어봄
- 각자의 취향에 따라 선택하면 됨(나는 설치하지 않았음)

## 데이터베이스 연결
- 아래 왼쪽 상단에 콘센트 모양의 노란색 박스 영역을 클릭(Connect to a database)하면 새로운 Connection을 만들 수 있음
<img src="https://user-images.githubusercontent.com/77138259/229953945-5bebbb57-c35f-4560-83db-f9181f432963.png" alt="DBeaver 초기 화면" />

- Connect to a database 창이 뜨면 MySQL을 선택
<img src="https://user-images.githubusercontent.com/77138259/229954373-38179bbc-4225-4b11-a234-412efee31d9a.png" alt="DBeaver_connect to a database" />

- 우리는 로컬 환경에서 MySQL의 연결을 시도해볼 것이기 때문에 Main 탭만 다루겠음
- Authentication 영역에서 Username(기본 root)과 Password를 입력해주고 완료 버튼을 클릭
<img src="https://user-images.githubusercontent.com/77138259/229955185-b9aa2bb4-c96d-464b-89fa-48137a7bb5a9.png" alt="DBeaver_connect to a database" />

- 다운로드 하겠다고 하면 다운로드 클릭
- 앞에서 만든 test_db가 생성되어 있는 것을 보아 정상적으로 데이터베이스 연결이 된 것을 알 수 있음
 <img src="https://user-images.githubusercontent.com/77138259/229955020-610e098c-2349-43fa-9afd-01adeb88725d.png" alt="DBeaver_database 연결 확인" />

- 같은 방법으로 tester 계정도 하나 연결해보자
>[사용자 계정 추가 및 권한 부여 방법](./MySQL_기본_명령어.md)
<img src="https://user-images.githubusercontent.com/77138259/229955418-458c7b49-a13d-48bc-af93-a67649d91416.png" alt="DBeaver_connect to a database" />

- 왼쪽 하단의 Test Connection을 클릭해서 연결이 되는지 확인하고 완료 버튼을 누름
<img src="https://user-images.githubusercontent.com/77138259/229955850-c28e8a10-2611-4756-a2c6-701a4af360b9.png" alt="DBeaver_connect to a database_test connection" />

- 아래 그림과 같이 커넥션이 하나 더 생기게 됨
- localhost 커넥션은 root 계정으로 접속한 것이기 때문에 test_db와 sys라는 DB가 모두 나오지만,
- test_db 커넥션은 tester 계정으로 접속한 것이기 때문에 test_db만 나오는 것을 확인할 수 있음
<img src="https://user-images.githubusercontent.com/77138259/229956865-e7e416d7-d411-47ad-9f31-3f073e48beaf.png" alt="DBeaver_database 연결 확인" />

- 쿼리문 테스트를 위해 `test_db - localhost:3306` 에 마우스 오른쪽 버튼을 클릭해 `SQL 편집기 → SQL 편집기`를 선택해 새로운 편집기를 실행해 줌
- tester 계정은 SELECT, INSERT, UPDATE, DELETE 권한만 있기 때문에, 아래와 같이 CREATE 쿼리를 입력하면 오류가 발생하게 됨
```mysql
CREATE table test_table (
	t1 varchar(20) null
)
```

<img src="https://user-images.githubusercontent.com/77138259/229957402-22b57b0e-2906-478c-bf7f-00681c90060e.png" alt="DBeaver_쿼리문 오류" />

## JDBC 설정 변경
- 8.0 버전부터 로컬 개발 시 보안 강화로 설정을 변경해줘야 함
- connect to a database에서 Driver properties 탭에서 아래와 같이 설정을 변경
	- allowPublicKeyRetrieval: True // Public Key를 서버에서 받아오는 것
	- useSSL: False // 데이터 암호화 인증 사용