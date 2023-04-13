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

### Table 이름 변경
```mysql
RENAME TABLE old_table TO new_table;
```


