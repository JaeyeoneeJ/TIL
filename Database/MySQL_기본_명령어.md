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

## Table 생성
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
select * from table_name;
```

### Table 생성문 확인
- 현재 테이블에 존재하는 인덱스나 제약조건들을 확인해볼 수 있음
```mysql
show create table table_name;
```

### Table에 칼럼 추가
- table 내에 칼럼을 추가하려면 `ALTER TABLE` 문을 사용함
- 아래는 `new_column`이라는 새로운 칼럼을 추가하는 예제임
```mysql
ALTER TABLE table_name ADD COLUMN new_column varchar(255) NOT NULL DEFAULT 'default_value';
```
- 위의 예제에서 `new_column`은 추가할 칼럼의 이름임
- `varchar(255)`는 데이터 타입임
- `NOT NULL`은 칼럼이 null 값을 허용하지 않음을 뜻함
- `DEFAULT 'default_value'`는 새로운 레코드를 추가할 때 기본값으로 `default_value`를 사용함을 의미함. 위 문장에서 `DEFAULT` 절은 생략 가능함
- 이렇게 하면 `videos` 테이블에 `new_column`이라는 칼럼이 추가됨

### Table 이름 변경
```mysql
RENAME TABLE old_table TO new_table;
```

### Table에 데이터 추가
- 만약에 아래와 같은 테이블이 있다고 가정하자
```mysql
desc videos;
# +----------+--------------+------+-----+---------+----------------+
# | Field    | Type         | Null | Key | Default | Extra          |
# +----------+--------------+------+-----+---------+----------------+
# | id       | bigint       | NO   | PRI | NULL    | auto_increment |
# | title    | varchar(255) | NO   |     | NULL    |                |
# | voice_kr | varchar(255) | NO   |     | NULL    |                |
# | voice_en | varchar(255) | NO   |     | NULL    |                |
# | bg       | varchar(255) | NO   |     | NULL    |                |
# +----------+--------------+------+-----+---------+----------------+
# 5 rows in set (0.01 sec)
```

- 여기에 데이터를 한 개 추가한다면 다음 쿼리문을 통해 추가할 수 있음
```mysql
INSERT INTO videos (title, voice_kr, voice_en, bg)
VALUES ('더미 제목', '한국어 발음', '영어 발음', '배경 이미지');
# Query OK, 1 row affected (0.00 sec)
```
- id 값은 auto_increment이며 Primary Key 이므로 값을 지정하지 않아도 유일한 값으로 자동 생성되기 때문에 인자로 넣지 않음
- 만약에 여러 데이터를 한번에 추가하고 싶다면 아래와 같이 작성할 수 있음
```mysql
INSERT INTO videos (title, voice_kr, voice_en, bg) VALUES
('Title 1', 'Korean Voice 1', 'English Voice 1', 'Background Music 1'),
('Title 2', 'Korean Voice 2', 'English Voice 2', 'Background Music 2'),
('Title 3', 'Korean Voice 3', 'English Voice 3', 'Background Music 3');
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

> **Warning 해석**
> 
> - 이러한 경고 메시지는 `mysql_tzinfo_to_sql` 명령어가 `/usr/share/zoneinfo` 디렉토리에서 시간대 정보를 가져오는 동안, 해당 디렉토리의 일부 파일들이 시간대 정보가 아니기 때문에 무시되었다는 것을 나타냄
> 
> - `iso3166.tab` 파일은 국가 코드와 국가 이름을 매핑하는 파일임. 이 파일은 시간대 정보가 아니기 때문에 무시해도 됨
> 
> - `leapseconds` 파일은 윤초 정보를 나타냄. MySQL 서버는 이 파일을 사용하지 않으므로 무시해도 됨
> 
> - `zone.tab` 파일은 시간대와 관련된 지역 정보를 포함하는 파일임. 이 파일의 정보는 `mysql_tzinfo_to_sql` 명령어가 이미 가져온 시간대 정보와 중복될 수 있으므로 무시해도 됨
> 
> - 따라서 이러한 경고 메시지는 시간대 정보에 영향을 미치지 않으므로 무시해도 됩니다.

3. MySQL 서버에서 사용 가능한 시간대 목록 확인
- 다음 명령을 실행하면 MySQL 서버에서 사용 가능한 모든 시간대의 목록이 표시됨
```mysql
select * from mysql.time_zone_name;
# +----------------------------------+--------------+
# | Name                             | Time_zone_id |
# +----------------------------------+--------------+
# | Africa/Abidjan                   |            1 |
# | Africa/Accra                     |            2 |
# | Africa/Addis_Ababa               |            3 |
# | Africa/Algiers                   |            4 |
# | Africa/Asmara                    |            5 |
# | Africa/Asmera                    |            6 |
# | Africa/Bamako                    |            7 |
# ...
```

4. 다시 시간대를 설정
- mysql에 다시 로그인 후 아래 명령어 입력
```mysql
SET GLOBAL time_zone='Asia/Seoul';
```
- 이제 `Asia/Seoul` 타임존을 사용할 수 있음
