## 설치
- https://www.postgresql.org/download/ 로 이동하여 사용 중인 운영체제에 맞는 다운로드 링크를 찾아 선택
- 설치는 기본 값으로 두고 Next를 클릭하면서 진행
- 설치 진행 중 Postgre 데이터베이스 관리자 ㅍ패스워드 설정에서 적절한 패스워드를 입력하고 진행
	- 설정한 패스워드는 나중에 접속할 때 필요하므로 반드시 기억할 것
- 포트 번호는 기본 값인 5432를 사용
- Locale 설정은 Ko-kr을 선택
- PostgreSQL의 설치가 완료되면 Stack Builder가 자동으로 실행됨
- 선택 박스에서 PostgreSQL xx를 선택
- 이후 Stack Builder에 추가할 애플리케이션을 선택하는 창에서 'Database Server => PostgreSQL vXX(installed)'를 체크하고 next 클릭
- 설치 완료 후 setup Wizard 화면이 나오면 next 클릭
- 설치 마지막 화면에 컴포넌트를 추가하는 체크박스가 뜨면 추가할 필요가 없으므로 체크 해제 후 Finish 클릭

## 실행
- Spotlight(윈도우 검색창 등)에 pgAdmin 4 를 검색 후 실행
- 패스워드 입력창이 뜨면 설치할 때 지정한 패스워드를 입력

## 데이터베이스 확인
- 왼쪽 창에서 Server => PostgreSQL xx => Databases 선택
- PostgreSQL에서는 설치 후 초기화할 때 'postgres'라는 이름으로 데이터베이스가 생성되어 표시됨
- 우선 새로운 데이터베이스를 만들지 않고 postgres 데이터베이스를 사용해보자

## 테이블 생성
- 회원의 정보(데이터)를 저장하는 member 테이블을 생성해보자
- 'Server => PostgreSQL xx => Databases => postgres => Schemas => Tables'를 선택
- 마우스 오른쪽 버튼을 클릭해서 'Create => Table' 선택. 각 탭별로 설정
- General 탭
	- Name 항목에 'member' 라고 입력
	- 여기서는 member가 테이블의 이름이 됨
- Columns 탭
	- 아래 창에서 Columns 오른쪽 끝의 '+'를 클릭해서 입력란을 추가
	- Name 항목에 'id', Data type 항목에 'serial'을 입력하고 'Primary Key?' 항목에서 'Yes' 선택
		- id 칼럼은 1~214748364 까지 일련번호가 부여 됨
		- 레코드의 제약 조건(Constraint)으로 Primary key를 부여함
	- 다시 '+'를 클릭해 입력란을 추가하여 Name: 'name', Data type: 'character varying', length/Precision: '10', Not Null?: 'Yes' 선택
		- name 칼럼은 문자 데이터로 10글자까지 입력되고 필수 입력의 의미로 Not Null 제약 조건을 설정함
- 입력 완료 후 Save 클릭 => member 테이블이 생성되었음

### 제약 조건
- 데이터 타입은 테이블에 저장하는 데이터의 종류를 제한하지만, 데이터 타입에서 제공하는 제약으로는 충분하지 않은 경우가 많음
- SQL에서는 칼럼 및 테이블에 대해서 제약 조건을 정의할 수 있음
- 제약 조건에 따라 테이블 내의 데이터를 자유롭게 제약하여 불일치 앖이 저장되는 것을 막을 수 있음

>제약 조건은 테이블에 존재하는 데이터가 불일치 상태가 되지 않게 하는 규칙임

- 제약 조건의 예
| **제약 조건** | **개요** |
| :---: | :--- |
| NOT NULL | NULL 입력을 허용하지 않음(필수 입력) |
| UNIQUE | 중복값 입력을 허용하지 않음(고유 값) |
| CHECK | 지정한 조건을 만족하지 않는 값의 입력을 허용하지 않음 |
| PRIMARY KEY | 테이블 안에서 레코드를 식별하는 기본키를 설정함. 기본키는 NOT NULL과 UNIQUE가 함께 적용됨 |
| FOREIGN KEY | 관련된 테이블을 연결하는 설정. 외부 키라고도 함 |
| DEFAULT | 칼럼의 초기값 설정 |

## 데이터 입력
- PostgreSQL에서의 CRUD 구문
| **CRUD** | **구문** |
| :--- | :--- |
| 생성(Create) | INSERT INTO 테이블명 (칼럼명, 칼럼명...) VALUES(값, 값, ...); |
| 읽기(Read) | SELECT 칼럼명 FROM 테이블명; |
| 갱신(Update) | UPDATE 테이블명 SET 칼럼명 = 값 WHERE 갱신할_레코드를_특정하는_조건;<br>▹ WHERE로 조건을 지정하지 않는 경우 모든 레코드가 대상이 됨 |
| 삭제(Delete) | DELETE FROM 테이블명 WHERE 삭제할_레코드를_특정하는_조건;<br>▹ WHERE로 조건을 지정하지 않는 경우 모든 레코드가 대상이 됨

### 1) INSERT 실행
- Tables => member 를 선택하고 메뉴에서 Tools => Query Tool을 선택하면 Query Editor가 표시됨
<img src="https://user-images.githubusercontent.com/77138259/228242803-2dfff8cc-c74b-43a9-bdef-c5f5df52d285.png" alt="postgreSQL query editor" />
- Query Editor 화면에 다음 SQL 입력
	- ▹ id 칼럼은 serial 타입으로 자동으로 일련번호가 설정되기 때문에 INSERT 문에 설정할 필요가 없음
<img src="https://user-images.githubusercontent.com/77138259/228244102-a6b4a6cc-0bca-40df-b800-981f7fa28302.png" alt="postgreSQL query editor-SQL 입력" />
- 입력 후 노란 박스로 표시된 '►' 버튼을 클릭
<img src="https://user-images.githubusercontent.com/77138259/228245640-1d9a7d74-56fc-45b1-a015-336b20b94517.png" alt="postgreSQL query editor" />
- Tables => member를 선택하고 표 마크인 <span style="background-color: #f7df1f; color: black; padding: 0 4">View Data</span> 를 클릭하면 Query Editor가 표시되고 <span style="background-color: #f77a1f; color: white; padding: 0 4">Data Output에 등록된 데이터</span>를 확인할 수 있음

### 2) SELECT 실행
- Tables => member 를 선택, 메뉴에서 Tools => Query Tool 선택
- Query Editor 화면에 다음 SQL 입력 후 '►' 버튼을 클릭
<img src="https://user-images.githubusercontent.com/77138259/228255888-5e72dcb7-a8c0-436b-9a02-69564053874a.png" alt="postgreSQL query editor" />
- 앞에서 확인한 내용과 같이 member 테이블에서 데이터를 읽어와 보여줌

### 3) UPDATE 실행
- Query Editor 화면에 다음 SQL 입력 후 '►' 버튼을 클릭
<img src="https://user-images.githubusercontent.com/77138259/228257412-65473eac-f841-4408-8609-837bd0ca89e7.png" alt="postgreSQL query editor" />

<img src="https://user-images.githubusercontent.com/77138259/228257752-8e6aeb92-bcae-4e47-bbc4-50c0087a80f8.png" alt="postgreSQL data output" />
- 'View Data'를 클릭해서 데이터가 갱신된 것을 확인

### 4) DELETE 실행
- Query Editor 화면에 다음 SQL 입력 후 '►' 버튼을 클릭
<img src="https://user-images.githubusercontent.com/77138259/228258471-c551848f-fad5-4936-a035-6d232dbd88eb.png" alt="postgreSQL query editor" />

<img src="https://user-images.githubusercontent.com/77138259/228258636-4f18b605-320b-418d-9b07-c64eac087cb0.png" alt="postgreSQL data output" />
- 'View Data'를 클릭해서 데이터가 삭제된 것을 확인