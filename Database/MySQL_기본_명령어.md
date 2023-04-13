## 1. MySQL 접속
- mysq에 접속하기 위해 아래 명령어를 입력하고, 비밀번호 입력하라는 메세지가 뜨면 초기 설정에서 등록한 비밀번호를 입력해주면 됨
- -u 는 user(사용자 계정)을 뜻하며, -p 는 password를 입력하겠다는 뜻임
```mysql
mysql -u root -p
```

>비밀번호가 틀리면 아래 메세지가 뜸
```
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
```
- 뒤에 using password가 YES로 되어 있으면 비밀번호를 입력해지만 접근이 거부되었단 뜻이므로 비밀번호가 다르다는 뜻임

- 맞게 입력하면 Welcome 메세지가 뜰 것임
```
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 12
Server version: 8.0.32 Homebrew
Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

- 나가고 싶으면 `exit`을 입력

## 2. 사용자 생성하기
- Spring이나 Spring Boot 프로젝트에서 MySQL 계정을 연결 시킬 때, 보안적인 측면에서 root 계정을 사용하는 것 보다는 사용자(서브) 계정을 하나 생성하여 필요한 권한만 부여한 뒤 사용하는 것이 좋음
- 먼저 root 계정으로 MySQL에 접속해보자
```bash
mysql -u root -p
```
- 패스워드를 입력하라고 뜨면 초기 설정한 패스워드를 입력해줌

- 사용자를 생성하기 위해서는 MySQL에 접속한 뒤 CREATE USER를 사용하여 생성하게 되며, `{username}`에는 사용자 이름을, `{password}`에는 사용자의 패스워드를 입력하게 됨
- '`{username}`'@ 뒤에 '`localhost`'가 붙으면 해당 컴퓨터에서만 접근이 가능하다는 뜻이고,  '`%`' 가 붙으면 모든 클라이언트에서 접근이 가능하다는 뜻임

>명령 쿼리는 대소문자를 가리지 않으니 참고

```mysql
CREATE USER '{username}'@'localhost' IDENTIFIED BY '{password}'
CREATE USER '{username}'@'%' IDENTIFIED BY '{password}'
```

- 테스트 용도로 `username`을 'tester'로, `password`를 'tester1234'로 설정하고 모든 클라이언트에 접근이 가능한 계정을 생성해보겠음
```mysql
CREATE USER 'tester'@'%' IDENTIFIED BY 'tester1234';
```

- `Query OK`란 문구가 뜨면 사용자 계정이 정상적으로 생성된 것임
- 제대로 생성되었는지 확인을 위해 `exit`으로 종료하고 'tester' 계정으로 MySQL에 접속해보자

```bash
mysql -u tester -p
```
- 패스워드 입력창이 뜨면 방금 설정한 비밀번호를 입력함
- `Welcome` 메세지가 뜨면 정상적으로 접속이 된 것임

## 3. 사용자 권한 설정
- 방금 전 생성한 사용자에게 권한을 부여해보자
- 권한을 부여할 때는 GRANT 명령어를 사용하면 됨
- 'tester'라는 사용자 계정에 'test_db'라는 Database에 대하여 SELECT, INSERT, UPDATE, DELETE 권한만 부여해보겠음
```mysql
GRANT SELECT, INSERT, UPDATE, DELETE ON test_db.* TO 'tester'@'%';
```

- 만일 모든 권한을 부여하고 싶다면 아래와 같이 입력
```mysql
GRANT ALL ON test_db.* TO 'tester'@'%';
```

## 4. Database 생성
- 먼저 root 계정으로 MySQL에 접속해보자
```bash
mysql -u root -p
```

- 현재 database의 목록을 보고 싶다면 아래 명령어로 확인
```mysql
show databases;
```

- 아마 기본 database가 아래와 같이 있을 것임
```
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)
```

- 'test_db'라는 새로운 Database를 생성하고 정상적으로 실행되었는지 DB 목록을 확인
```mysql
create database test_db;
show databases;
```

- 아래와 같이 생성되었다면 정상적으로 실행된 것임
```
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| test_db            |
+--------------------+
5 rows in set (0.00 sec)
```

### Database 삭제
```mysql
DROP DATABASE database_name;
```

### Database 사용
```mysql
use database_name;
# Database changed
```

### Table 생성
```mysql
create table table_name (
    id bigint auto_increment primary key not null,
    account_id varchar(255) not null,
    name varchar(255) not null,
    age int not null
    );
```
- id에 큰 정수, auto_increment로 자동 증가, primary key로 고유 키값임
- account_id와 name은 문자열이며, age는 일반 정수임
- 모든 컬럼에 not null을 줌

- 생성한 테이블의 컬럼을 보려면 아래 명령어 사용
```mysql
USE database_name;
DESC table_name;
```

### Table 내 데이터 확인
```mysql
select * from user;
```

### Table 이름 변경
```mysql
RENAME TABLE old_table TO new_table;
```

## time_zone 설정
- mysql 서버에 로그인 함
- 다음 쿼리를 실행해서 현재 시간대를 확인함
```mysql
SELECT @@global.time_zone;
```

- 시간대를 변경하려면 아래와 같이 입력함(아시아 서울)
```mysql
SET GLOBAL time_zone = 'Asia/Seoul';
```

- 변경된 시간대를 확인하기 위해 다시 쿼리를 실행
```mysql
SELECT @@global.time_zone;
# +--------------------+
# | @@global.time_zone |
# +--------------------+
# | Asia/Seoul         |
# +--------------------+
# 1 row in set (0.00 sec)
```

### ERROR 1298 (HY000): Unknown or incorrect time zone: 'Asia/Seoul' 처리
- 이 오류는 MySQL 서버가 지정된 타임존 이름을 인식하지 못해서 발생할 수 있음
- MySQL에서 사용할 수 있는 시간대 이름 목록은 시스템의 `zoneinfo` 데이터베이스에서 가져오기 때문에 해당 시스템에서 사용 가능한 시간대만 사용할 수 있음
- `Asia/Seoul` 타임존이 인식되지 않는 경우에는 다음과 같이 해결할 수 있음

1. 시스템에 `tzdata` 패키지가 설치되어 있는지 확인합니다. 만약 설치되어 있지 않다면 다음 명령어를 사용하여 설치
```bash
sudo apt-get install tzdata
```

2. `mysql_tzinfo_to_sql` 유틸리티를 사용하여 `zoneinfo` 데이터베이스를 업데이트
```bash
mysql_tzinfo_to_sql /usr/share/zoneinfo | mysql -u root mysql -p
# Enter password:           # <- mysql의 root 비밀번호 입력

# Warning: Unable to load '/usr/share/zoneinfo/iso3166.tab' as time zone. Skipping it.
# Warning: Unable to load '/usr/share/zoneinfo/leap-seconds.list' as time zone. Skipping it.
# Warning: Unable to load '/usr/share/zoneinfo/zone.tab' as time zone. Skipping it.
# Warning: Unable to load '/usr/share/zoneinfo/zone1970.tab' as time zone. Skipping it.
```
- `mysql_tzinfo_to_sql` 명령어가 실행되면 MySQL 서버가 사용할 수 있는 `zoneinfo` 데이터베이스를 생성함

3. 다시 시간대를 설정
- mysql에 다시 로그인 후 아래 명령어 입력
```mysql
SET GLOBAL time_zone='Asia/Seoul';
```
- 이제 `Asia/Seoul` 타임존을 사용할 수 있음
