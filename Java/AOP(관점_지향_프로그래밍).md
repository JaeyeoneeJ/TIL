## 관점 지향 프로그래밍(Aspect Oriented programming)
- 데이터베이스 액세스 처리에는 예외 발생 시 처리하는 내용이 반드시 포함되어야 함
- 예외처리를 하지 않으면 프로그램이 중지되고 자바의 경우, 예외 처리를 프로그램에 포함하지 않으면 컴파일에 실패함

- 다수의 데이터베이스 액세스 처리 코드를 작성하다 보면 예외 처리의 내용은 항상 동일하지만, 예외 처리는 필수이므로 항상 작성해야 함
- 예외 처리를 포함하면 프로그램 코드가 증가하고 복잡해짐
- 구현하고 싶은 프로그램은 데이터베이스의 액세스 처리이며 예외 처리는 구현하고 싶은 프로그램에 부수적인 내용이 됨

- 스프링 프레임워크에서 제공하는 AOP 기능을 활용하여 '중심적 관심사(액세스 처리 등)'와 '횡단적 관심사(예외 처리 등)'를 분리하여 프로그램을 쉽게 만들 수 있음

### AOP 고유 용어
| **용어** | **개요** |
| :--- | :--- |
| Advice | 횡단적 관심사의 구현(메서드). 로그 출력 및 트랜잭션 제어 등 |
| Aspect | 어드바이스를 정리한 것(클래스) |
| JoinPoint | 어드바이스를 중심적인 관심사에 적용하는 타이밍. 메서드(생성자) 실행 전, 메서드(생성자) 실행 후 등 실행되는 타이밍 |
| Pointcut | 어드바이스를 삽입할 수 있는 위치. 예를 들어 메서드 이름이 get으로 시작할 때만 처리하는 조건을 정의할 수 있음 |
| Interceptor | 처리의 제어를 인터셉트하기 위한 구조 또는 프로그램. 스프링 프레임워크에서는 인터셉트라는 메커니즘으로 어드바이스를 중심 관심사에 추가한 것처럼 보이게 함 |
| Target | 어드바이스가 도입되는 대상 |

- 스프링 프레임워크가 제공하는 중심적 관심사에 적용하는 어드바이스는 실행 제어 내용별로 다섯 가지 종류가 있음

- 어드바이스의 다섯 가지 종류
| **어드바이스** | **내용** | **어노테이션** |
| :--- | :--- | :--- |
| Before Advice | 중심적 관심사가 실행되기 '이전'에 횡단적 관심사를 실행 | @Before |
| After Returning Advice | 중심적 관심사가 '정상적으로 종료된 후'에 횡단적 관심사를 실행 | @AfterReturning |
| After Throwing Advice | 중심적 관심사로부터 '예외가 던져진 후'로 횡단적 관심사를 실행 | @AfterThrowing |
| After Advice | 중심적 관심사의 '실행 후'에 횡단적 관심사를 실행(정상 종료나 예외 종료 등의 결과와 상관없이 실행) | @After |
| Around Advice | 중앙적 관심사 호출 전후에 횡단적 관심사를 실행 | @Around |

### 포인트컷 식
- 직접 어드바이스를 만드는 경우 패키지, 클래스, 메서드 등 어드바이스 삽입 대상을 조건으로 지정할 수 있음
- 지정하는 조건 방법에는 포인트컷 식을 사용함
- 포인트컷 표현식은 여러 가지가 있지만 예제에서는 'execution' 지시자를 설명함

>**execution 지시자의 구문**
>: execute(반환값 패키지.클래스.메서드(인수))

- 포인트컷 식은 와일드카드를 이용하여 유연하게 적용 범위를 지정할 수 있음

- 와일드카드
| **와일드카드** | **내용** |
| :--- | :--- |
| \*(애스터리스크) | 임의의 문자열을 나타내고, 패키지를 나타낼 때는 임의의 패키지 한 계층을 나타냄. 메서드의 인수에서는 한 개의 인수를 나타내 반환값으로도 이용할 수 있음 |
| ..(점 두 개) | 패키지를 타나내는 경우 0개 이상의 패키지를 나타냄. 메서드의 인수를 표현하는 경우에는 0개 이상의 임의의 인수를 나타냄 |
| +(플러스) | 클래스명 뒤에 기술해 클래스와 그 서브클래스 및 구현 클래스 모두를 나타냄 |

- execution 지시자의 구현 예
| **구현 예** | **내용** |
| :--- | :--- |
| execution(\* com.example.service.DemoService.\*(..)) | DemoService 클래스의 메서드에 어드바이스를 적용 |
| execution(\* com.example.service.DemoService.select\*(..)) | DemoService 클래스의 select로 시작하는 메서드에 어드바이스를 적용 |
| execution(String com.example.service.DemoService.\*(..)) | DemoService 클래스의 반환값이 String 타입인 메서드에 어드바이스를 적용 |
| execution(\* com.example.service.DemoService.\*(String,..)) | DemoService 클래스의 첫 번째 인수가 String 타입인 메서드에 어드바이스를 적용 |
| execution(\* com.example.service.\*.\*(..)) | 지정된 패키지 아래의 모든 클래스의 메서드에 어드바이스를 적용(서브 패키지는 포함하지 않음) |
| execution(\* com.example.service..\*.\*(..)) | service 패키지 바로 아래와 하위 패키지의 모든 클래스에 어드바이스를 적용 |
| execution(\* com.example.service.DemoService.\*.(\*)) | DemoService 클래스의 인수가 하나인 메서드에 어드바이스를 적용 |

## AOP 프로그램 만들기
- 직접 어드바이스를 만들고 포인트컷 식에서 어드바이스 삽입 위치를 지정하여 AOP의 동작 방식을 확인할 수 있는 프로그램을 만들어보자
- chapter03.aop라는 패키지를 생성 후, AOP를 사용하기 위해 build.gradle에 AOP를 추가한다.
```build
// build.gradle
...
dependencies {  
   implementation 'org.springframework.boot:spring-boot-starter'  
   implementation 'org.springframework.boot:spring-boot-starter-aop' // AOP 추가  
   developmentOnly 'org.springframework.boot:spring-boot-devtools'  
   testImplementation 'org.springframework.boot:spring-boot-starter-test'  
}
...
```

- 'SampleAspect' 클래스를 생성하고 어드바이스를 기술하는 클래스에는 @Aspect 어노테이션을 부여함
- 인스턴스를 생성하기 위해 @Component 어노테이션을 부여함
>['build.gradle'에서 추가한 의존성이 반영되지 않거나 @Aspect가 인식되지 않을 경우 해결방법](./build.gradle_오류_해결.md)

- SampleAspect 클래스에