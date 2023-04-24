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
@RestController public class MyController {
	@Autowired
	private Environment environment;
	
	@GetMapping("/local")
	public String getLocalPath() {
		String localPath = environment.getProperty("app.local.src");
		return localPath;
	}
}
```

- 위의 경우에서 만일 localPath를 전역에서 사용하고 싶다면 다음과 같이 사용할 수 있음
```java
@RestController public class MyController {
	@Autowired
	private Environment environment;
	
	private String localPath;
	
	@PostConstruct
	public void init() {
		localPath = environment.getProperty("app.local.src");
	}
	
	@GetMapping("/local")
	public String getLocalPath() {
		return localPath;
	}
	
	@GetMapping("/local2")
	public String getLocalPath2() {
		return localPath;
	}
}
```
- 위 코드에서 init() 메서드에서 `localPath` 변수를 초기화하였음
- 그리고 getLocalPath()와 getLocalPath2() 메서드에서 해당 변수를 리턴하여 사용하였음
- 단, init() 메서드가 호출되어야만 localPath 변수가 초기화되므로 해당 메서드를 먼저 호출하기 위해 @PostConstruct 어노테이션을 추가하여 Bean을 생성하고 의존성 주입이 완료된 후에 init() 메서드가 호출되도록 하였음

### 3) @Value 어노테이션 사용
- 해당 변수를 전역으로 사용하고자 한다면 @Value 어노테이션을 사용하여 `application.properties` 파일의 값을 할당할 수 있음
```java
@RestController public class MyController {
	@Value("${app.local.src}")
	private String localPath;

	@GetMapping("/local")
	public String getLocalPath() {
		return localPath;
	}
	
	@GetMapping("/local2")
	public String getLocalPath2() {
		return localPath;
	}
}
```
- @Value 어노테이션은 해당 클래스에서만 동작하기 때문에 전역으로 사용하기에 부적절할 수 있음. 이 경우, 위의 Environment를 사용하여 전역에서 설정 값을 읽어오고 @PostConstruct 어노테이션을 사용하는 것이 올바른 경우일 수 있음