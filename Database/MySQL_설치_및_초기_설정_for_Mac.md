## 1. 준비사항
- homebrew가 설치되어 있어야 함
- 터미널에 `brew -v` 명령어를 입력하여 버전 정보가 뜨면 설치가 정상적으로 되어 있는 것임

## 2. 설치하기
- `brew search mysql` 명령어를 입력하여 버전 등을 확인하기
- 최신 릴리즈 버전으로 설치하고자 하면 `brew install mysql` 명령어를 입력
- 설치가 정상적으로 되었는지 확인하기 위해 버전을 확인
```bash
mysql -V
```

## 3. 서버 시작/종료
- 정상적으로 설치되면 MySQL을 시작함
```bash
mysql.server start
```

- Starting MySQL 문구가 뜨고 SUCCESS ! 가 뜨면 서버가 정상 작동되고 있는 것임
- 설치할 때 MySQL Database가 root Password 없이 설치되었다는 문구를 봤을 것임

- 서버를 끄고 싶으면 아래 명령어를 입력
```bash
mysql.server stop
```

## 4. 초기 설정
- 아래 명령어를 통해 기본 설정을 시작하자
```bash
mysql_secure_installation
```

- 순서대로 아래 질문들이 나오면 y 또는 n을 입력하여 설정해 줌

### 1) 비밀번호 복잡도 검사 과정
```
VALIDATE PASSWORD COMPONENT can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD component?
```
- 유효성 검사를통해 비밀번호를 테스트하고 보안을 강화한다는 내용임
- 나는 n 을 입력함

### 2) 비밀번호 입력/확인
```
Please set the password for root here.
```
- 루트 비밀번호 설정

```
Re-enter new password
```
- 한번 더 입력(확인)

### 3) 익명 사용자 삭제 여부
```
By default, a MySQL installation has an anonymous user, allowing anyone to log into MySQL without having to have a user account created for them. This is intended only for testing, and to make the installation go a bit smoother.
You should remove them before moving into a production environment.
```
- 기본적으로 MySQL 설치에는 익명의 사용자가 있어 로그인 없이 누구나 MySQL에 접근할 수 있는 익명 계정이 있음
- 익명 사용자를 제거하기 위해 n 입력

### 4) 원격 접속 허용 여부
```
Normally, root should only be allowed to connect from 'localhost'. This ensures that someone cannot guess at the root password from the network.
```
- 일반적으로 루트는 localhost이기 때문에 네트워크의 루트 암호를 누군가가 추측할 수 없음
- 루트 로그인을 원격으로 허용하지 않기 위해 y 입력

### 5) test DB 삭제 여부
```
By default, MySQL comes with a database named 'test' that anyone can access. This is also intended only for testing, and should be removed before moving into a production environment.
```
- 기본적으로 MySQL은 '`test`'라는 DB를 같이 제공하며, 테스트용으로만 사용됨
- 테스트해볼 것이기 때문에 n(삭제하지 않음) 입력

### 6) previlege table 리로드 여부
```
Reloading the privilege tables will ensure that all changes made so far will take effect immediately.
```
- `previlege table(권한 테이블)`을 리로드 하면 설정한 모든 사항이 즉시 적용됨
- 즉시 적용하기 위해 y 입력

- `All done!` 메세지가 뜨면 모든 ==초기 설정==을 정상적으로 마친 것임
- 언제든지 아래 명령을 입력해서 다시 설정할 수 있음
```bash
mysql_secure_installation
```

## 5. 참고
- MySQL에서 터미널만으로는 데이터베이스의 관리가 힘듦
- 주로 Sequel Pro나 DBeaber 등의 데이터베이스 클라이언트를 설치하여 사용하는 편임

