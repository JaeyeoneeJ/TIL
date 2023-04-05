## RDS 인스턴스 생성하기
- AWS 콘솔에 접속해 검색창에 'rds'를 입력해서 선택하고 rds 대시보드에서 데이터베이스 생성 버튼을 클릭
- 엔진 옵션에서 'MySQL' 선택
- 템플릿에서 '프리 티어' 선택
- 설정에서 DB 인스턴스 식별자를 'test-springboot-webservice'로 작성
- 스토리지에서 '스토리지 자동 조정 활성화' 체크 해제
- 연결에서 '퍼블릭 액세스'를 '예'로 체크(이후 보안 그룹에서 지정된 IP만 접근하도록 막을 예정)
- 추가 구성 → 데이터베이스 옵션 에서 초기 데이터베이스 이름을 'test_springboot_webservice'로 작성(데이터베이스 이름을 지정하지 않으면 Amazon RDS에서 데이터베이스를 생성하지 않음
- 다 마쳤으면 '데이터베이스 생성' 버튼 클릭
- 데이터베이스에서 보면 해당 데이터베이스가 생성 중인 상태가 뜰 것임. 생성되는데는 몇 분정도 걸림

## RDS 운영환경에 맞는 파라미터 설정하기
- RDS를 처음 생성하면 몇 가지 설정을 필수로 해야 함
- 우선 다음 3가지 설정을 차례대로 진행해보겠음
	- 타임존
	- Character Set
	- MaxConnection
- 왼쪽 카테고리에서 '파라미터 그룹' 탭 클릭
- 화면 오른쪽 상단의 '파라미터 그룹 생성' 클릭
- 세부정보 위쪽에 DB엔진을 선택하는 항목이 있음. 여기서 방금 생성한 MySQL과 같은 버전을 맞춰야 함
- 우리는 mysql8.0 으로 생성했기 때문에 똑같이 맞춰줌
- 그룹 이름과 설명에 'test-springboot-webservice'를 작성 및 생성 버튼 클릭
- 잘 생성되었다면 해당 파라미터 그룹을 클릭한 후 오른쪽 상단의 '파라미터 편집' 버튼을 클릭해서 편집 모드로 전환
- 파라미터 검색창에 'time_zone'을 검색하고 값을 `Asia/Seoul`을 선택
- 'character_set'을 검색하여 아래 항목들을 모두 `utf8mb4`로 변경
	- character_set_client
	- character_set_connection
	- character_set_database
	- character_set_filesystem
	- character_set_results
	- character_set_server
- 'collation'을 검색하여 아래 항목들을 모두 `utf8mb4_general_ci`로 변경
	- collation_connection
	- collation_server

>utf8과 utf8mb4
>- utf8은 이모지를 저장할 수 없지만, utf8mb4는 이모지를 저장할 수 있으므로 보편적으로 utf8mb4를 많이 사용함

- max_connections를 수정함. RDS의 max_connections은 인스턴스 사양에 따라 자동으로 정해짐. 150으로 지정
- 이후에 RDS 사양을 높이게 된다면 기본값으로 다시 돌려놓으면 됨
- 설정이 다 되었다면 오른쪽 상단의 '변경 사항 저장' 버튼을 클릭해 최종 저장함

- 생성된 파라미터 그룹을 데이터베이스에 연결하겠음
- 사이드바의 데이터베이스 카테고리를 클릭해 연결할 데이터베이스를 선택하고 오른쪽 상단의 수정 버튼 클릭
- 추가 구성의 '데이터베이스 옵션'에서 DB 파라미터 그룹은 default로 되어 있음
- DB 파라미터 그룹을 방금 생성한 신규 파라미터 그룹으로 변경함
- '계속' 버튼을 누르면 방금 수정한 사항이 요약되어 보여짐
- 수정 예약에서 `즉시 적용`을 체크하고 `DB 인스턴스 수정` 버튼을 클릭
	- 예약된 다음 유지 시간으로 하면 지금 하지 않고, 새벽 시간대에 진행하게 됨
	- 이 수정사항이 반영되는 동안 데이터베이스가 작동하지 않을 수 있으므로 예약 시간을 걸어두라는 의미임
	- 지금은 서비스가 오픈되지 않았기 때문에 즉시 적용함
	- 그럼  `수정 중` 상태가 나타남
- 간혹 파라미터 그룹이 제대로 반영되지 않을 때가 있으므로 정상 적용을 위해 한 번 더 재부팅을 진행함
	- 데이터베이스를 선택하고 오른쪽 상단의 `작업 → 재부팅 → 확인` 버튼을 클릭(상태: `재부팅 중`)
- 재부팅까지 성공했다면 이제 로컬 PC에서 RDS에 한 번 접속해보겠음

## 내 PC에서 RDS에 접속해 보기
- 로컬 PC에서 RDS로 접근하기 위해서 `RDS의 보안 그룹에 본인 PC의 IP를 추가`하겠음
- RDS의 세부정보 페이지(데이터베이스 클릭)에서 `보안 → VPC 보안 그룹` 항목을 클릭함
- RDS의 보안 그룹 정보를 그대로 두고, 브라우저를 새로 열어 봄
- 새로운 브라우저 창에서 보안 그룹 목록 중 `EC2에 사용된 보안 그룹의 그룹 ID`를 복사함(만약 목록이 default만 뜬다면 필터 지우기를 클릭해서 전체를 보자)
- 연결된 보안 그룹 편집에서 인바운드 규칙 편집을 눌러 복사한 보안 그룹 ID와 IP를 보안 그룹의 인바운드로 추가함
<img src="https://user-images.githubusercontent.com/77138259/230019891-23090f3c-09c9-4626-ae7e-3f96680eaf33.png" alt="RDS 보안 그룹 인바운드 수정" />
- 인바운드 규칙 유형에서는 `MYSQL/Aurora`를 선택하면 자동으로 3306 포트가 선택됨
	- 현재 내 PC의 IP를 등록
	- EC2의 보안 그룹을 추가
		- 이렇게 하면 EC2와 RDS 간에 접근이 가능함
		- EC2의 경우 이후에 2대 3대가 될 수도 있는데, 매번 IP를 등록할 수 없으니 보편적으로 이렇게 보안 그룹 간에 연동을 진행함
- RDS와 개인 PC,  EC2 간의 연동 설정은 모두 되었으므로 로컬에서 테스트해보겠음

### DBeaver 연결
- 로컬에서 원격 데이터베이스로 붙을 때 GUI 클라이언트를 많이 사용함
- MySQL의 대표적인 클라이언트로 Workbench, SQLyog(유료), Sequel Pro(맥 전용), DataGrip(유료) 등이 있음
- 여기서는 DBeaver로 연결을 할 것임
- RDS에서 연결할 데이터베이스를 클릭하고 엔드포인트를 복사해 둠
	- 이 엔드 포인트가 접근 가능한 URL임
- DBeaver에서 `connect to a database`를 클릭하고 RDS의 정보를 차례로 등록함
- `Test Connection`을 눌러 연결 테스트를 해봄

<img src="https://user-images.githubusercontent.com/77138259/230022610-0181b0b1-f7a3-466f-a7ed-40864d222aaf.png" alt="RDS 접속 정보 등록" />

- Connected 메세지를 보았다면 컬렉션에서 `mysql`을 확인하고 마우스 오른쪽 버튼을 클릭해 `SQL 편집기 → SQL 편집기`를 선택해 새로운 편집기를 실행해 줌
- 생성된 창에 SQL을 실행해보겠음. 쿼리가 수행될 database를 선택하는 쿼리임
```mysql
use test_springboot_webservice;
```

- 옆의 주황색 화살표를 클릭하면 쿼리문이 실행됨
- 아래와 같은 메세지가 뜨면 정상적으로 수행된 것임
<img src="https://user-images.githubusercontent.com/77138259/230025644-08ecbd2f-4070-4d3e-9e6b-71c366ec21cf.png" alt="쿼리 실행 성공 메세지" />

- 데이터베이스가 선택된 상태(use 쿼리문 실행 후)에서 현재의 character_set, collation 설정을 확인
<img src="https://user-images.githubusercontent.com/77138259/230026413-90541bde-ee99-4bc9-8dec-666d4799109b.png" alt="현재 db 설정 확인" />

- 몇 가지 설정이 변경되지 않은 것을 확인할 수 있음
- 직접 변경을 위해 다음 쿼리를 실행함
```mysql
ALTER DATABASE test_springboot_webservice
CHARACTER SET = 'utf8mb4'
COLLATE = 'utf8mb4_general_ci';
```

- 쿼리를 수행하였다면 다시 chracter set 등을 확인함

- 타임존도 확인
<img src="https://user-images.githubusercontent.com/77138259/230033492-acd15b15-b055-48a6-87ab-14d51c09b5c0.png" alt="현재 db 설정 확인" />
- RDS 파라미터 그룹이 잘 적용되어 한국 시간으로 된 것을 확인

- 마지막으로 한글명이 잘 들어가는지 간단한 테이블 생성과 insert 쿼리를 실행해 봄
- 아래 쿼리를 차례로 실행해야 함
```
create table test (
id bigint(20) not null auto_increment,
content varchar(255) default null,
primary key (id)
) engine=InnoDB;

insert into test(content) values ('테스트');

select * from test;
```

<img src="https://user-images.githubusercontent.com/77138259/230034686-e34c19d9-82a2-42b4-bd02-263288eec486.png" alt="한글 데이터 등록 확인" />

## EC2에서 RDS에서 접근 확인
- EC2에 ssh 접속을 진행하고 MySQL 접근 테스트를 위해 MySQL CLI를 설치하겠음

>실제 EC2의 MySQL을 설치해서 쓰는게 아닌 명령어 라인만 쓰기 위한 설치임


```bash
sudo app-get istall mysql-client
```

- 설치가 다 되었으면 로컬에서 접근하듯이 계정, 비밀번호, 호스트 주소를 사용해 RDS에 접속함

```bash
mysql -u {계정} -p -h {Host주소(엔드포인트))}
```
- 패스워드를 입력하라는 메세지가 나오면 패스워드까지 입력함

- RDS에 접속되었으면 실제로 생성한 RDS가 맞는지 확인해보겠음
```mysql
show databases;
```

<img src="https://user-images.githubusercontent.com/77138259/230037877-e0a05ed5-11a2-467a-9d22-5057f04f0041.png" alt="데이터베이스 목록 확인" />
- 우리가 생성했던 `test_springboot_webservice`라는 데이터베이스가 있음을 확인했음