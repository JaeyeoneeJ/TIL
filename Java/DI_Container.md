## DI Container란
- DI 컨테이너란 의존성 주입(Dependency Injection), 줄여서 DI의 실현을 돕는 프레임워크임

<hr>

## 의존성
- 의존에는 다음 두 가지 유형이 있음
	- 클래스 의존(구현 의존)
	- 인터페이스 의존
		- 인터페이스는 참조를 받는 유형으로 사용할 수 있으므로 변수의 이름을 변경하지 않아도 됨
		- 인터페이스가 선언된 메서드를 이용하면 클래스가 바뀌어도 메서드 명을 변경하지 않아도 됨

<hr>

## 인터페이스에 의존하는 프로그램 만들기
- '계산 처리' 역할을 하는 인터페이스와 인터페이스를 구현한 '더하기'와 '빼기' 처리를 하는 클래스를 각각 만들어서 인터페이스 의존의 동작 방식을 확인해보자

### 1) 인터페이스와 구현 클래스 생성(chapter03.used.pkg)
```java
// Calculator 인터페이스
package chapter03.used;
  
// 계산 처리  
public interface Calculator {  
	// 계산 처리를 함  
	// @param x;  
	// @param y;  
	// @return Integer;  

	Integer calc(int x, int y);  
}
```

```java
// 구현 클래스(AddCalc)
package chapter03.used;  
  
// Calculator 구현 클래스  
// 덧셈 처리  
public class AddCalc implements Calculator {  
    @Override  
    public Integer calc(int x, int y) {  
        return x + y;  
    }  
}
```

```java
// 구현 클래스(SubCalc)
package chapter03.used;  
  
// Calculator 구현 클래스  
// 뺄샘 처리  
public class SubCalc implements Calculator {  
    @Override  
    public Integer calc(int x, int y) {  
        return x - y;  
    }  
}
```

### 2) '사용하는 객체' 클래스 생성(chapter03.use.pkg)
```java
// Call 클래스
package chapter03.use;  
  
import chapter03.used.AddCalc;  
import chapter03.used.Calculator;  
  
// 인터페이스 의존 확인  
public class Call {  
    public static void main(String[] args) {  
        Calculator calculator = new AddCalc();  
        Integer result = calculator.calc(10, 5);  
        System.out.println("덧셈 계산 결과는 " +  result + "입니다.");  
    }  
}
```

### 3) '사용되는 객체' 클래스 변경(AddCalc => SubCalc)
```java
package chapter03.use;  
  
import chapter03.used.SubCalc; // 수정 부분  
import chapter03.used.Calculator;  
  
// 인터페이스 의존 확인  
public class Call {  
    public static void main(String[] args) {  
        Calculator calculator = new SubCalc(); // 수정 부분  
        Integer result = calculator.calc(10, 5);  
        System.out.println("뺄셈 계산 결과는 " +  result + "입니다."); // 문구 수정  
    }  
}
```

- 이처럼 '사용하는 객체' 클래스의 한 곳만 수정해서 처리를 변경할 수 있음

<hr>

## DI 컨테이너

### 의존성 주입
- 의존하는 부분이란 '사용하는 객체' 클래스에서 '사용되는 객체' 클래스가 작성된 상태임
- 외부로부터 주입이란 '사용하는 객체' 클래스의 밖에서 '사용되는 객체' 인스턴스를 주입하는 것

- 지금까지 인스턴스를 생성하는데 new 키워드를 사용했지만 인스턴스 생성과 같은 작업을 프레임워크에 맡길 수 있고 그 역할을 하는 것이 DI 컨테이너임
- 스프링 프레임워크는 임의로 구현한 클래스를 인스턴스로 만들어주는 기능을 제공함
- 즉, DI 컨테이너가 그 기능을 가지고 있음

### 다섯가지 규칙
DI 컨테이너에 인스턴스 생성을 맡기고 다음의 규칙을 지키는 것으로 '사용하는 객체' 클래스를 전혀 수정할 필요가 없게끔 만들 수 있음
- 1) 인터페이스를 이용하여 의존성을 만든다.
	- 의존하는 부분에 인터페이스를 이용한다는 것
- 2) 인스턴스를 명시적으로 생성하지 않는다.
	- 인스턴스 생성에 new 키워드를 사용하지 않는다는 것을 의미함
- 3) 어노테이션을 클래스에 부여한다.
- 4) 스프링 프레임워크에서 인스턴스를 생성한다.
	- 3)과 4)는 인스턴스를 생성하려는 클래스에 인스턴스 생성 어노테이션(@Component 등)을 부여한다는 것
	- 스프링 프레임워크는 시작할 때 대상 프로젝트의 모든 패키지를 스캔함. 이 기능을 컴포넌트 스캔(Component Scan)이라고 함
	- 컴포넌트 스캔 후 스프링 프레임워크는 인스턴스 생성 어노테이션이 부여된 클래스를 추출하고 추출한 클래스의 인스턴스를 생성함
- 5) 인스턴스를 이용하고 싶은 곳에 어노테이션을 부여한다.
	- 스프링 프레임워크에 의해 생성된 인스턴스를 이용하는 클래스에 참조를 받는 필드를 선언하고 필드에 @Autowired 어노테이션을 부여함

### 인스턴스 생성 어노테이션
| **어노테이션** | **개요** |
| :---: | :--- |
| @Controller | 인스턴스 생성 지시. 스프링 MVC를 이용할 때 컨트롤러에 부여 |
| @Service | 인스턴스 생성 지시. 트랜잭션 경계가 되는 도메인(서비스) 기능에 부여 |
| @Repository | 인스턴스 생성 지시. 데이터베이스 액세스(리포지토리) 기능에 부여 |
| @Component | 위 용도 이외의 클래스에 부여 |

<hr>

## DI 프로그램 만들기
'인사' 역할을 하는 인터페이스와 인터페이스를 구현한 '아침 인사'와 '저녁 인사'를 처리하는 클래스를 각각 만들고 어노테이션을 부여하는 것으로 DI의 동작 방식을 확인할 수 있는 프로그램을 작성해보자

- IntelliJ IDEA Community Edition을 기준으로 하기 때문에 Spring Initializr에서 생성한 파일을 사용해서 프로젝트를 만들어 사용해보자
- 웹 브라우저로 https://start.spring.io/ 에 접속해서 다음 설정 내용을 참조해서 설정하고 [GENERATE]를 클릭해 파일을 내려받자
| **항목** | **값** |
| :---: | :--- |
| Project | Gradle - Groovy |
| Language | Java |
| Spring Boot | 3.0.5(안정 버전이면 다른 버전도 괜찮음. Snapshot: 만들고 있는 버전, M1: 정식으로 릴리즈되지 않은 버전) |
| Java | 17 |
| Dependencies | 'ADD...' 를 클릭해 Spring Boot DevTools를 검색해서 선택 |

- 다운로드 한 demo.zip 의 이름을 임의로 변경 후 압축을 풀고 IntelliJ IDEA에서 압축 푼 폴더를 선택한다.
- IntelliJ IDEA가 자동으로 필요한 라이브러리의 다운로드를 시작한다.

### 1) '사용되는 객체' 인터페이스와 구현 클래스 생성
```java
// Greet 인터페이스
package com.example.demo.chapter03.used;  
  
// 인사 인터페이스  
public interface Greet {  
    // 인사하기  
    void greeting();  
}
```

```java
// 구현 클래스(MorningGreet)
package com.example.demo.chapter03.used;  
  
import org.springframework.stereotype.Component;  
  
// Greet 구현 클래스  
// 아침 인사 하기  
@Component // 규칙 3, 4 적용  
public class MorningGreet implements Greet{  
    @Override  
    public void greeting() {  
        System.out.println("-----------------");  
        System.out.println("좋은 아침입니다.");  
        System.out.println("-----------------");  
    }  
}
```

```java
// 구현 클래스(EveningGreet)
package com.example.demo.chapter03.used;  
  
// Greet 구현 클래스  
// 저녁 인사 하기  
public class EveningGreet implements Greet {  
    @Override  
    public void greeting() {  
        System.out.println("-----------------");  
        System.out.println("좋은 저녁입니다.");  
        System.out.println("-----------------");  
    }  
}
```

### 2) '사용하는 객체' 클래스 생성
- Spring Initializr에서 스프링 부트 프로젝트를 생성하면 기본적으로 사용자가 만든 Project Name + Application 클래스가 생성됨
- 이 클래스에는 @SpringBootApplication 주석이 부여됨
- 메인 메서드는 포함한 클래스에서 @SpringBootApplication 어노테이션을 부여하면 스프링 부트 어플리케이션이라고 인식됨
- 여기서는 DemoApplication 클래스가 스프링 부트 애플리케이션의 기동 클래스라고 생각하면 됨

```java
// 사용하는 쪽 클래스인 DemoApplication
package com.example.demo;  
  
import org.springframework.boot.SpringApplication;  
import org.springframework.boot.autoconfigure.SpringBootApplication;  
  
// 스프링 부트 시작 클래스  
@SpringBootApplication  
public class DemoApplication {  
  
   public static void main(String[] args) {  
      SpringApplication.run(DemoApplication.class, args);  
   }  
  
}
```

### 3) 규칙 1, 2, 5 구현
- 스프링 프레임워크에 의해 생성된 인스턴스를 이용하고 싶은 곳에서 참조를 받는 필드를 선언하고 필드에 @Autowired 어노테이션을 부여함
- 여기서는 '사용하는 객체' 클래스인 DemoApplication에 '사용되는 객체' 인터페이스인 Greet 필드를 선언하고 @Autowired 어노테이션을 부여함
```java
package com.example.demo;  
  
import com.example.demo.chapter03.used.Greet;  
  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.boot.SpringApplication;  
import org.springframework.boot.autoconfigure.SpringBootApplication;  
  
// 스프링 부트 시작 클래스  
@SpringBootApplication  
public class DemoApplication {  
  
   // main 메서드  
   // @param args  
   public static void main(String[] args) {  
      SpringApplication.run(DemoApplication.class, args)  
            .getBean(DemoApplication.class).execute();  
   }  
  
   // 주입하는 곳(인터페이스)  
   @Autowired  
   Greet greet;  
  
   // 실행 메서드  
   private void execute(){ // 메서드 명을 execute로 함  
      greet.greeting();  
   }  
  
}
```

- 실행하면 "좋은 아침입니다." 가 출력됨

- '사용되는 객체' 클래스를 변경하게 되었다고 가정해보겠음
- EveningGreet의 greeting 메서드를 호출하도록 변경하고 MorningGreet 클래스의 인스턴스 생성 어노테이션인 @Component는 삭제 또는 주석 처리함

> 기존 @Component를 삭제 또는 주석처리 하지 않을 경우 아래와 같은 메세지가 뜨면서 컴파일 에러가 발생함
> **error:**  `Field greet in com.example.demo.DemoApplication required a single bean, but 2 were found:`

```java
// 구현 클래스(MorningGreet)
...
// @Component // 주석 처리
public class MorningGreet implements Greet{  
	...
}
```

```java
// 구현 클래스(EveningGreet)
...
@Component // 어노테이션 부여
public class EveningGreet implements Greet{  
	...
}
```

```console
// 결과(console)
-----------------
좋은 저녁입니다.
-----------------
```

- 스프링 프레임워크는 기동 시 컴포넌트 스캔에 의해 MorningGreet 클래스에 @Component 어노테이션이 부여되어 MorningGreet 인스턴스가 생성됨
- @Autowired 어노테이션에 따라 MorningGreet 클래스의 인스턴스가 클래스의 greet 필드에 주입되어 실행되면 MorningGreet 클래스의 greeting 메서드가 실행됨

- 설계 변경에 대응하기 위해 기존 @Component 어노테이션을 삭제 또는 주석처리하고 EveningGreet 클래스에 @Component 어노테이션을 부여하면 @Autowired 어노테이션에 따라 EveningGreet 클래스의 인스턴스가 클래스의 greet 필드에 주입되어 실행 시, EveningGreet 클래스의 greeting 메서드가 실행됨