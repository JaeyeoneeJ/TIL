# 1. 애플리케이션 설명
- 지금까지 정리한 내용을 토대로 웹 애플리케이션을 만들겠음(OX Quiz)
- 'OX Quiz'란 제출된 퀴즈에 대해서 O 혹은 X로 대답하는 퀴즈임

### 1) 기능 목록
- 1~4번 기능은 CRUD 처리
- 5번 기능은 등록한 퀴즈를 무작위로 표시하고 답하는 기능
- 작성할 기능 목록

|**No**|**기능**|**설명**|
|---|---|---|
| 1 | 등록 기능 | 퀴즈를 등록함 |
| 2 | 갱신 기능 | 등록된 퀴즈를 갱신함 |
| 3 | 삭제 기능 | 등록된 퀴즈를 삭제함 |
| 4 | 목록 표시 기능 | 등록된 퀴즈의 목록을 표시함 |
| 5 | 게임 기능 | 퀴즈 게임을 실시함 |

- URL 목록

|**No**|**역할**|**HTTP 메서드**|**URL**|
|---|---|---|---|
| 1 | 퀴즈 목록을 표시함 | GET | /quiz |
| 2 | 등록 처리를 실행함 | POST | /quiz/insert |
| 3 | 갱신 화면을 표시함 | GET | /quiz/{id} |
| 4 | 갱신 처리를 실행함 | POST | /quiz/update |
| 5 | 삭제 처리를 실행함 | POST | /quiz/delete |
| 6 | 퀴즈 화면을 표시함 | GET | /quiz/play |
| 7 | 퀴즈 답을 체크함 | POST | /quiz/check |

### 2) 예제 애플리케이션의 레이어
- OX Quiz 애플리케이션은 다음 세 개의 레이어로 나뉨
	- 애플리케이션 레이어
	- 도메인 레이어
	- 인프라스트럭처 레이어
- MVC 모델에서는 업무 기능이나 취급하는 데이터의 처리 요건이 복잡해지면 비즈니스 로직 처리를 담당하는 모델(Model: M) 부분이 많아져서 '모델의 비대화'라는 문제가 발생함
- MVC 모델은 설계 개념상 모델이 담당하는 부분 자체를 줄이는 것은 안 되기 때문에 모델 안에서 역할 분담을 명확하게 하고 애플리케이션의 레이어 구성을 적용하고 비대해지는 모델을 분할하는 것이 레이어를 나누는 목적임
- 각 레이어는 아래 그림에 표시된 구성 요소(컴포넌트)가 포함됨

<img src="https://user-images.githubusercontent.com/77138259/229412240-a34155fe-3aeb-4138-9583-5bd8376b0b39.png" alt="레이어별 컴포넌트" />
<p style="margin:0" align="right"><a href="https://wikibook.co.kr/spring/">출처: 스프링 프레임워크 첫걸음(위키북스)</a></p>

- 애플리케이션 레이어: 클라이언트에서 받은 요청을 제어하고 도메인 레이어를 사용하여 애플리케이션을 제어함
- 도메인 레이어: 도메인 객체에 대해 애플리케이션의 서비스 처리를 실행함
- 인프라스트럭처 레이어: 도메인 객체에 대해 CRUD 조작을 해서 데이터의 영속화(데이터를 영구 저장하는)를 담당함

- 레이어를 나누는 엄격한 규칙으로서 애플리케이션 레이어도 인프라스트럭처 레이어도 도메인 레이어에 의존하지만 도메인 레이어는 다른 레이어에 의존해서는 안 된다는 규칙이 있음
- 즉, 도메인 레이어의 변경에 의해 '애플리케이션 레이어'의 변경은 허용하지만 애플리케이션 레이어의 변경으로 도메인 레이어의 변경이 발생해서는 안 된다는 것임

### 3) 레이어별 컴포넌트 설명
1. 애플리케이션 레이어
-  Controller: 요청을 처리에 매핑하고 결과를 뷰에 넘겨주는 제어를 함. 주요 처리는 Controller 안에서 실행하지 않고 '도메인 레이어'의 Service를 호출함
- Form: 화면의 폼을 표현함. 화면에서 입력한 값을 Controller에 넘겨줌. 또한 Controller에서 화면에 결과를 출력할 때도 사용함. 도메인 레이어가 애플리케이션 레이어에 의존하지 않도록 Form에서 도메인 객체로 변환하거나 도메인 객체에서 Form으로 변환하는 것을 애플리케이션 레이어에서 수행해야 함
- View: 화면 표시를 담당함

2. 도메인 레이어
- 도메인 객체: 서비스 처리를 실행할 때 필요한 자원임(Entity 등)
- Service: 애플리케이션의 서비스 처리를 담당함
- Repository: Repository는 인터페이스임. 데이터베이스의 데이터 조작 내용만 정의함(구현 내용은 작성하지 않음)

3. 인프라스트럭처 레이어
- Repositorylmpl: 도메인 레이어에서 정의한 Repository의 구현 클래스임. 'O/R Mapper'가 Repository의 구현 클래스를 생성하는 경우도 있음
- O/R Mapper: O(Object; 객체)와 R(Relational; 관계형 데이터베이스) 간의 데이터를 매핑함

### 4) 생성할 컴포넌트 목록

|**No**|**레이어**|**컴포넌트**|**이름**|**비고**|
|---|---|---|---|---|
| 1 | 애플리케이션 레이어 | View | - | 화면 표시 |
| 2 | 애플리케이션 레이어 | Controller | QuizController | 제어 역할 담당 |
| 3 | 애플리케이션 레이어 | Form | QuizForm | 화면의 게임 폼을 표현 |
| 4 | 도메인 레이어 | Service | QuizService | 인터페이스로 생성 |
| 5 | 도메인 레이어 | ServiceImpl | QuizServiceImpl | Service를 구현 |
| 6 | 도메인 레이어 | 도메인 객체 | Quiz | 엔티티 역할 |
| 7 | 도메인 레이어 | Repository | QuizRepository | 인터페이스로 생성 |
| 8 | 인프라스트럭처 레이어 | RepositoryImpl | - | O/R Mapper로 자동 생성 |
| 9 | 인프라스트럭처 레이어 | O/R Mapper | - | 스프링 데이터 JDBC를 사용 |

# 2. OX Quiz용 데이터베이스 생성
### 1) 데이터베이스 생성
- pgAdmin 4를 실행
- 왼쪽 창에서 `Server => PostgreSQL xx => Databases` 선택 후, 마우스 오른쪽 버튼을 클릭해서 `Create → Database`를 선택
<img src="https://user-images.githubusercontent.com/77138259/229427762-31ef3e83-2491-4b80-a02f-1d23265885ff.png" alt="create database" />

### 2) 테이블 생성
- 해당 예제에서는 알기 쉽도록 테이블 하나만 사용함
- 정규화(Normalization; 관계형 데이터베이스의 설계에서 중복을 최소화하도록 데이터를 구조화하는 과정)는 하지 않음
- 테이블 목록

|**No**|**테이블명**|**설명**|
|---|---|---|
|1|quiz|퀴즈 정보를 저장함|

- 테이블 정의

|**No**|**칼럼**|**타입**|**제약**|**설명**|
|---|---|---|---|---|
|1|id|serial|PK|퀴즈 정보 식별 ID|
|2|question|text|NOT NULL|퀴즈 내용|
|3|answer|boolean|NOT NULL|퀴즈 답|
|4|author|character varying(20)|NOT NULL|작성자|


<img src="https://user-images.githubusercontent.com/77138259/229429109-0540e5ca-d028-49be-9257-8a6ef3655b03.png" alt="테이블 생성" />

### 3) 프로젝트 생성
- [Spring Initializr](./Spring_initializr_사용방법.md)에서 다음 설정 내용을 참조해서 다운로드 후 압축을 해제함

| **항목** | **값** |
| :--- | :--- |
| Project | Gradle - Groovy |
| Language | Java |
| Spring Boot | 3.0.5 |
| Artifact | quiz |
| Packaging | jar |
| Package name | com.example.quiz |
| Java | 17 |
| Dependencies | Spring Boot DevTools(개발 툴)<br>Lombok(개발 툴)<br>Spring Data JDBC<br>PostgreSQL Driver<br>Validation(I/O)<br>Thymeleaf(템플릿 엔진)<br>Spring Web(웹) |

- Spring Boot DevTools:  스프링 부트 개발 툴. 자동 재실행 등 개발에 편리한 기능이 포함되어 있음
- Lombox(개발 툴): 어노테이션을 부여하는 것으로 getter와 setter 등을 코드로 작성하지 않아도 자동으로 구현해 줌
- Spring Data JDBC: 스프링 데이터에서 제공하는 OR Mapper임
- PostgreSQL Driver: PostgreSQL 데이터베이스에 접속하는 데 필요한 드라이버임
- Validation: 유효성 검사 기능인 'Bean Validation'과 'Hibernate Validator'를 사용할 수 있게 함
- Thymeleaf: 스프링 부트에서 추천하는 템플릿 엔진
- Spring Web: 스프링 MVC임

<img src="https://user-images.githubusercontent.com/77138259/229441557-123c78d8-0308-4ea1-8441-568bb9a40d8f.png" alt="spring initializr 화면 구성" />

- IntelliJ IDEA에서 열기로 폴더를 선택함

# 3. 데이터베이스 조작
- 도메인 객체, Repository, RepositoryImpl, O/R Mapper를 구현하고 quizdb 데이터베이스의 quiz 테이블을 대상으로 CRUD를 처리해보겠음

### 1) application.properties 설정
- `application.properties`는 스프링 부트 프로젝트에서 환경 설정을 담당하는 파일임
- 여기서는 `quizdb` 데이터베이스에 접속하는 설정을 추가함
- `src/main/resource → application.properties`를 클릭해서 연 다음 아래 설정을 추가함

```properties
spring.datasource.driver-class-name=org.postgresql.Driver  
spring.datasource.url=jdbc:postgresql://localhost:5432/quizdb  
spring.datasource.username=postgres  
spring.datasource.password=[앞에서 설정한 패스워드]
```
- `spring.datasource.password`에는 앞에서 설정한 패스워드를 입력함

### 2) 도메인 객체 생성
- O/R Mapper로 스프링 데이터 JDBC를 사용함. RepositoryImpl은 스프링 데이터 JDBC가 자동으로 생성해줘서 우리가 만들어야 할 것은 '도메인 객체'와 'Repository'임. 우선 도메인 객체부터 만들어보자
- 도메인 객체는 비즈니스 로직을 처리하는 데 필요한 객체임. 여기서는 엔티티(Entity)와 같은 의미를 가짐
- 엔티티는 데이터베이스에서 테이블의 1행에 대응되는 클래스임
- quiz 테이블에 대응하는 Quiz 엔티티를 생성하겠음
- '`src/main/java → com.example.quiz`'에서 마우스 오른쪽 버튼을 클릭해 '`새로 만들기 → 패키지`'를 선택하여 `com.example.quiz.entity` 패키지를 생성
- `com.example.quiz.entity` 패키지에서 '`새로 만들기 → Java 클래스`' 선택 및 클래스명을 `Quiz`로 지정
- 클래스 내용을 다음과 같이 입력
```java
// Quiz 클래스

package com.example.quiz.entity;  
  
import lombok.AllArgsConstructor;  
import lombok.Data;  
import lombok.NoArgsConstructor;  
import org.springframework.data.annotation.Id;  
  
@Data  
@NoArgsConstructor  
@AllArgsConstructor  
public class Quiz {  
    // 식별 ID    @Id  
    private Integer id;  
    // 퀴즈 내용  
    private String question;  
    // 퀴즈 답  
    private Boolean answer;  
    // 작성자  
    private String author;  
}
```
- 클래스에 `@Data` 어노테이션을 부여하면 모든 필드를 getter/setter로 액세스할 수 있게 됨
- `@NoArgsConstructor` 어노테이션은 기본 생성자를 자동으로 생성해 줌
- `@AllArgsConstructor` 어노테이션은 모든 필드에 대해 초깃값을 인수로 받는 생성자를 생성함
- `@Id` 어노테이션은 테이블의 PK(기본키)인 id와 매핑됨

### 3) Repository 생성
- `Repository`는 인터페이스임. 데이터베이스의 데이터 정의만 작성함
- '`src/main/java → com.example.quiz`'에서 마우스 오른쪽 버튼을 클릭해 '`새로 만들기 → 패키지`'를 선택하여 `com.example.quiz.repository` 패키지를 생성
- `repository` 패키지를 선택하고 마우스 오른쪽 버튼을 클릭해서 `새로 만들기 → Java 클래스`를 선택한 후 `QuizRepository` 인터페이스를 생성

### 4) RepositoryImpl 생성
- 앞에서 만든 `QuizRepository` 인터페이스에 스프링 데이터에서 제공하는 `CrudRepository`를 상속해서 `RepositoryImpl`을 만듦
- `CrudRepository`의 타입 파라미터는 엔티티 타입인 `Quiz`와 `@Id` 어노테이션을 부여한 필드의 타입인 `Integer` 순으로 지정
- `QuizRepository` 인터페이스의 내용은 아래와 같음
```java
// QuizRepository 인터페이스

package com.example.quiz.repository;  
  
import com.example.quiz.entity.Quiz;  
import org.springframework.data.repository.CrudRepository;  
  
// Quiz 테이블: RepositoryImpl  
public interface QuizRepository extends CrudRepository<Quiz, Integer> {  
}
```
- 스프링 데이터에서 제공하는 `CrudRepository`를 상속하는 것으로 자동으로 CRUD를 제공하는 메서드를 사용할 수 있음
- `<Quiz, Integer>`는 '저장 대상 객체의 타입'과 '저장 대상 객체의 기본키'를 나타냄
- `QuizRepository`의 CRUD 메서드

|**반환값**|**메서드**|**개요**|
|---|---|---|
|long|count()|취득한 엔티티의 수를 돌려줌|
|void|delete(Member entity)|지정한 엔티티를 삭제함|
|void|deleteAll()|리포지토리에서 관리하는 모든 엔티티를 삭제함|
|void|deleteAll(Iterable entities)|지정한 복수의 엔티티를 삭제함|
|void|deleteById(Integer id)|지정한 ID에 해당하는 엔티티를 삭제함|
|boolean|existsById(Integer id)|지정한 ID에 해당하는 엔티티의 존재 여부를 확인함|
|Iterable|findAll()|모든 엔티티를 반환함|
|Iterable|findAllById(Iterable ids)|지정한 복수의 ID에 해당하는 엔티티를 반환함|
|Optional|findById(Integer id)|지정한 ID에 해당하는 엔티티를 반환함
|Quiz|save(Quiz entity)|지정한 엔티티를 저장함|
|Iterable|saveAll(Iterable entities)|지정한 모든 엔티티를 저장함|

### 5) 등록 처리
- 스프링 부트 애플리케이션의 구동 클래스인 `QuizApplication`의 필드에 `QuizRepository`를 `@Autowired`로 주입하고 `QuizRepository`의 동작 방식을 확인하겠음
- 보통 클래스의 동작 확인은 JUnit 등으로 단위 테스트를 통해 확인하지만 여기서는 스프링 부트의 구동 클래스에 주입하여 확인하겠음
- 또한 코드를 수정해서 저장하면 Spring Boot DevTools가 자동으로 재기동해 주지만 여기서는 명확하게 하기 위해 수동으로 정지하고 시작하는 방법을 이용하겠음

1. 프로그램 작성
- QuizApplication 클래스를 생성하고 아래 내용을 작성
```java
// QuizApplication 클래스

package com.example.quiz;  
  
import com.example.quiz.entity.Quiz;  
import com.example.quiz.repository.QuizRepository;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.boot.SpringApplication;  
import org.springframework.boot.autoconfigure.SpringBootApplication;  
  
@SpringBootApplication  
public class QuizApplication {  
   // 구동 메서드  
   public static void main(String[] args) {  
      SpringApplication.run(QuizApplication.class, args)  
            .getBean(QuizApplication.class).execute();  
   }  
     
   // 주입(인젝션)  
   @Autowired  
   QuizRepository repository;  
   // 실행 메서드  
   private void execute() {  
      // 등록 처리  
      setup();  
   }  
     
   // == 퀴즈 2건을 등록함 ==   private void setup() {  
      // 엔티티 생성  
      Quiz quiz1 = new Quiz(null, "Spring은 프레임워크입니까?", true, "홍길동");  
      // 등록 실행  
      quiz1 = repository.save(quiz1);  
      // 등록 확인  
      System.out.println("등록한 퀴즈는 " + quiz1 + "입니다.");  
  
      // 엔티티 생성  
      Quiz quiz2 = new Quiz(null, "스프링 MVC는 배치 처리를 제공합니까?", false, "홍길동");  
      // 등록 실행  
      quiz2 = repository.save(quiz2);  
      // 등록 확인  
      System.out.println("등록한 퀴즈는 " + quiz2 + "입니다.");  
   }  
}
```
- `@Autowired`로 `QuizRepository`를 주입(인젝션)함
- `main` 메서드에서 `execute`를 호출하고, `execute`에서 `setup` 메서드를 호출함
- `setup` 메서드에서 `CrudRepository`를 상속해서 사용할 수 있게 된 `save` 메서드를 호출해서 지정한 엔티티를 데이터베이스에 저장함
- 또한 `save` 메서드는 `@Id` 어노테이션이 부여한 필드가 `null`인 경우에는 `INSERT` 문을 실행하고 `null`이 아닌 경우에는 `UPDATE` 문을 실행함

2. 실행과 확인
- `QuizApplication` 파일을 선택하고 마우스 오른쪽 버튼을 클릭해서 `실행`을 선택함
- 정상적으로 실행되면 `CrudRepository`를 상속한 `QuizRepository` 클래스의 등록 처리가 호출됨

<img src="https://user-images.githubusercontent.com/77138259/229455417-1b9a9cf2-4f72-4075-a543-a82538c7477e.png" alt="실행 결과(콘솔)" />

- 데이터베이스에 퀴즈 정보가 2건 등록된 것을 확인함
<img src="https://user-images.githubusercontent.com/77138259/229456111-605826e7-bbc8-4424-8a83-fd1360b49a13.png" alt="실행 결과(quiz 테이블)" />

### 6) 모든 데이터 취득
1. 프로그램 작성
- `QuizApplication` 클래스에 아래 메서드를 추가함
```java
// QuizApplication 클래스

...

@SpringBootApplication  
public class QuizApplication {  
	...
	// 모든 데이터 취득  
	private void showList() {  
	   System.out.println("--- 모든 데이터를 취득합니다. ---");  
	   // 리포지토리를 이용해 모든 데이터를 취득해서 결과를 반환  
	   Iterable<Quiz> quizzes = repository.findAll();  
	   for (Quiz quiz: quizzes) {  
	      System.out.println(quiz);  
	   }  
	   System.out.println("--- 모든 데이터 취득 완료 ---");  
	}
}
```
- `showList` 메서드에서 `CrudRepository`를 상속받아서 사용 가능해진 `findAll` 메서드를 실행해서 엔티티 전체를 취득함
- 또한 `QuizApplication` 클래스의 execute 메서드를 아래와 같이 수정함
- 앞에서 실행한 `setup` 메서드를 주석 처리하고 `execute` 메서드의 `showList` 메서드를 호출함
```java
// QuizApplication 클래스 내 execute 메서드 수정

private void execute() {  
   // 등록 처리  
   // setup(); <- 주석처리  
   
   // 전체 항목 취득  
   showList();  
}
```

2. 실행과 확인
- `QuizApplication` 파일을 선택하고 마우스 오른쪽 버튼을 클릭해서 `실행`을 선택함
- 정상적으로 실행되면 `CrudRepository`를 상속한 `QuizRepository` 클래스의 전체 항목 취득 처리가 호출됨
<img src="https://user-images.githubusercontent.com/77138259/229458314-d26f33ca-bba0-49e0-a266-aee3900a1ffb.png" alt="실행 결과(콘솔)" />

### 7) 한 건 데이터 취득
1. 프로그램 작성
- `QuizApplication` 클래스에 아래 메서드를 추가함
```java
// QuizApplication 클래스

...

@SpringBootApplication  
public class QuizApplication {  
	...
	
	// == 한 건 데이터 취득 ==
	private void showOne() {  
	   System.out.println("--- 1건 취득 개시 ---");  
	   // 리포지토리를 사용해서 1건의 데이터를 취득해서 결과를 반환(반환값은 Optional)   Optional<Quiz> quizOpt = repository.findById(1);  
	   // 반환값이 있는지 확인  
	   if (quizOpt.isPresent()) {  
	      System.out.println(quizOpt.get());  
	   } else {  
	      System.out.println("해당 데이터는 존재하지 않습니다.");  
	   }  
	   System.out.println("--- 1건 취득 완료 ---");  
	}
}
```
- `showOne` 메서드에서 `CrudRepository`를 상속해서 `findById` 메서드를 사용함
- 여기서는 기본키인 id의 값을 1로 지정함
- `findById` 메서드의 반환값은 `Optional`임. `Optional(java.util.Optional)`은 `null`의 가능성이 있는 값을 처리할 때 사용하는  클래스로서 값을 래핑해서 사용함
- `isPresent` 메서드로 값이 있는지 확인하고 다음 줄의 get 메서드로 래핑한 값을 취득함
- 값이 존재하지 않는 경우에는 런타임 예외(NoSuchElementException)를 던짐

- `QuizApplication` 클래스의 `execute` 메서드를 아래와 같이 수정함
```java
// QuizApplication 클래스 내 execute 메서드 수정

private void execute() {  
   // 등록 처리  
	// setup(); <- 주석처리  
	  
	// 전체 항목 취득  
	// showList(); <- 주석 처리  
	  
	// 1건 취득  
	showOne();
}
```
- `showList` 메서드를 주석 처리하고, `execute` 메서드에서 `showOne` 메서드를 호출함

2. 실행과 확인
 - `QuizApplication` 파일을 선택하고 마우스 오른쪽 버튼을 클릭해서 `실행`을 선택함
- 정상적으로 실행되면 `CrudRepository`를 상속한 `QuizRepository` 클래스의 한 건 처리가 실행됨
<img src="https://user-images.githubusercontent.com/77138259/229655878-0825a26e-7135-4390-8716-b54324f5c8a6.png" alt="실행 결과(콘솔)" />

### 8) 변경 처리
1. 프로그램 작성
- `QuizApplication` 클래스에 아래 메서드를 추가함
```java
// QuizApplication 클래스

...

@SpringBootApplication  
public class QuizApplication {  
	...
	
	// == 변경 처리 ==
	private void updateQuiz() {  
	   System.out.println("--- 변경 처리 개시 ---");  
	   // 변경할 엔티티를 생성  
	   Quiz quiz1 = new Quiz(1, "스프링은 프레임워크입니까?", true, "변경 담당");  
	   // 변경 처리  
	   quiz1 = repository.save(quiz1);  
	   // 변경 결과 확인  
	   System.out.println("변경된 데이터는 " + quiz1 + "입니다.");  
	   System.out.println("--- 변경 처리 완료");  
	}
}
```
- `updateQuiz` 메서드에서 `CrudRepository`를 상속해서 `save` 메서드를 사용해 지정한 엔티티를 데이터베이스에 저장함
- `save` 메서드는 `@Id` 어노테이션이 부여된 필드가 `null`인 경우에는 `INSERT` 문을 실행하고, `null`이 아닌 경우에는 `UPDATE` 문을 실행하기 때문에 이번에 실행될 `save` 메서드에서는 `UPDATE` 문이 실행됨

- `QuizApplication` 클래스의 `execute` 메서드를 아래와 같이 수정함
```java
// QuizApplication 클래스 내 execute 메서드 수정

private void execute() {  
   // 등록 처리  
	// setup(); <- 주석처리  
	  
	// 전체 항목 취득  
	// showList(); <- 주석 처리  
	  
	// 1건 취득  
	// showOne(); <- 주석 처리  
	  
	// 변경 처리  
	updateQuiz();
}
```
- `showOne` 메서드를 주석 처리하고, `execute` 메서드에서 `updateQuiz` 메서드를 호출함

2. 실행과 확인
 - `QuizApplication` 파일을 선택하고 마우스 오른쪽 버튼을 클릭해서 `실행`을 선택함
- 정상적으로 실행되면 `CrudRepository`를 상속한 `QuizRepository` 클래스의 변경 처리가 호출됨
<img src="https://user-images.githubusercontent.com/77138259/229656723-f1971d52-4bbf-46fb-8d96-5ff3959302e8.png" alt="실행 결과(콘솔)" />

<img src="https://user-images.githubusercontent.com/77138259/229656933-0b8c53f3-0d79-4585-8712-38815fccbd01.png" alt="실행 결과(quiz 테이블)" />

### 9) 삭제 처리
1. 프로그램 작성
- `QuizApplication` 클래스에 아래 메서드를 추가함
```java
// QuizApplication 클래스

...

@SpringBootApplication  
public class QuizApplication {  
	...
	
	// == 삭제 처리 ==
	private void deleteQuiz() {  
	   System.out.println("--- 삭제 처리 개시 ---");  
	   // 삭제 실행  
	   repository.deleteById(2);  
	   System.out.println("--- 삭제 처리 완료 ---");  
	}
}
```
- `deleteQuiz` 메서드에서 `CrudRepository`를 상속해서 `deleteById` 메서드를 사용해 지정한 엔티티를 삭제함
- 여기서는 기본키인 id에 값으로 2를 지정함

- `QuizApplication` 클래스의 `execute` 메서드를 아래와 같이 수정함
```java
// QuizApplication 클래스 내 execute 메서드 수정

private void execute() {  
   // 등록 처리  
	// setup(); <- 주석처리  
	  
	// 전체 항목 취득  
	// showList(); <- 주석 처리  
	  
	// 1건 취득  
	// showOne(); <- 주석 처리  
	  
	// 변경 처리  
	// updateQuiz(); <- 주석 처리  
	  
	// 삭제 처리  
	deleteQuiz();
}
```
- `updateQuiz` 메서드를 주석 처리하고, `execute` 메서드에서 `deleteQuiz` 메서드를 호출함

2. 실행과 확인
 - `QuizApplication` 파일을 선택하고 마우스 오른쪽 버튼을 클릭해서 `실행`을 선택함
- 정상적으로 실행되면 `CrudRepository`를 상속한 `QuizRepository` 클래스의 삭제 처리가 실행됨
<img src="https://user-images.githubusercontent.com/77138259/229657389-0485dad4-79c3-4e9b-bb5f-bf340ca32257.png" alt="실행 결과(콘솔)" />

- 동시에 데이터베이스에서 데이터가 삭제된 것을 확인할 수 있음
<img src="https://user-images.githubusercontent.com/77138259/229657462-33cb7ec8-1d24-42aa-84e2-82c2fe534399.png" alt="실행 결과(quiz 테이블)" />

