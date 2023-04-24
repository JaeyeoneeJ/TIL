- 최근에 MySQL이나 AWS S3 버킷 등을 사용하게 되면서 해당 프로젝트를 외부에 공유할 때, 키 값 등을 숨길 필요성을 느끼게 되었음
- 우리는 리액트에서 .env를 통해 환경변수를 설정해본 경험이 있고, Spring Boot에서도 똑같이 ID, PW, Key, Url 등 외부에 노출이 되지 않게 환경 변수로 숨겨야하는 경우가 종종 발생함
- java에서 환경변수를 설정하는 방법은 여러가지가 있지만 이 중에서 application.properties 파일을 활용하여 환경 변수를 설정하는 방법을 알아보자

### 1) application.properties 파일에서 변수 설정
- 먼저 `application.properties` 파일에서 환경 변수를 설정하려면 아래와 같이 작성함
```properties
app.local.src=/usr/local/bin
app.aws.bucketName=test-bucket-001
app.aws.accessKey=thisisaccesskey002
app.aws.secretKey=thisissecretkey003
```
위와 같이 변수 이름을 마음대로 설정하고 `=` 뒤에 원하는 값을 넣으면 됨

### 2) Environment 인터페이스 사용
- 간단한 예시로 Controller에서 Environment 인터페이스를 통해 변수를 받아와보겠음
```java
@Autowired
private Environment environment;
```
- 위 코드는 스프링 빈으로 등록된 클래스에서 `Environment` 인터페이스를 주입받아서 사용하겠다는 의미임
- `Environment` 인터페이스는 `getProperty()` 메소드를 제공하여 `application.properties` 파일에 설정한 변수 값을 가져올 수 있음.

- 예를 들어 위에서 설정한 변수 값을 가져오려면 다음과 같이 작성함
```java
@Autowired
private Environment environment;

String localPath = environment.getProperty("app.local.src");
String bucketName = environment.getProperty("app.aws.bucketName");
String accessKey = environment.getProperty("app.aws.accessKey");
String secretKey = environment.getProperty("app.aws.secretKey");
```