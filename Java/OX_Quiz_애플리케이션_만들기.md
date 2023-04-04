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

# 4. 비즈니스 로직 처리
- 현재 등록한 퀴즈 중 1건을 받아오는 기능은 위에서 만들었기 때문에 1건을 받아올 때 인수로 전달되는 값을 랜덤으로 생성하는 방법을 구현해야 함

### 1) 비즈니스 로직 처리 만들기
1. Service 생성
- Service는 인터페이스임
- 인터페이스에서는 메서드의 구체적인 처리 내용을 작성하지 않는 '추상 메서드'를 작성함
- '`src/main/java → com.example.quiz`'에서 마우스 오른쪽 버튼을 클릭해 '`새로 만들기 → 패키지`'를 선택하여 `com.example.quiz.service` 패키지를 생성
- `com.example.quiz.service` 패키지에서 '`새로 만들기 → Java 클래스`' 선택 및 인터페이스명을 `QuizService`로 지정
- 인터페이스 내용을 다음과 같이 입력

```java
// QuizService 인터페이스

package com.example.quiz.service;  
  
import com.example.quiz.entity.Quiz;  
  
import java.util.Optional;  
  
// Quiz 서비스: service  
public interface QuizService {  
    // 등록된 모든 퀴즈 정보를 가져옴  
    Iterable<Quiz> selectAll();  
    // id를 키로 사용해 퀴즈 정보를 한 건 가져옴  
    Optional<Quiz> selectOneById(Integer id);  
    // 퀴즈 정보를 랜덤으로 한 건 가져옴  
    Optional<Quiz> selectOneRandomQuiz();  
    // 퀴즈의 정답/오답 여부를 판단함  
    Boolean checkQuiz(Integer id, Boolean myAnswer);  
    // 퀴즈를 등록함  
    void insertQuiz(Quiz quiz);  
    // 퀴즈를 변경함  
    void updateQuiz(Quiz quiz);  
    // 퀴즈를 삭제함  
    void deleteQuizById(Integer id);  
}
```
- `QuizService` 인터페이스에서는 추상 메서드를 작성함
- 위의 `작성할 기능 목록` 중 `게임 기능`에서 사용할 메서드로서, 퀴즈 정보를 랜덤으로 한 건 가져오는 `selectOneRandomQuiz` 메서드, 퀴즈의 정답/오답을 판단하는 `checkQuiz` 메서드와 CRUD를 처리하는 메서드임

2. ServiceImpl 생성
- 앞에서 작성한 인터페이스를 구현할 `ServiceImpl`을 생성하겠음
- '`src/main/java → com.example.quiz.service`'에서 마우스 오른쪽 버튼을 클릭해 '`새로 만들기 → Java 클래스`' 선택 및 클래스명을 `QuizServiceImpl`로 지정
- `QuizServiceImpl` 클래스의 내용을 다음과 같이 입력
```java
// QuizServiceImpl 클래스

package com.example.quiz.service;  
  
import com.example.quiz.entity.Quiz;  
import com.example.quiz.repository.QuizRepository;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.stereotype.Service;  
  
import java.util.Optional;  
  
@Service  
public class QuizServiceImpl implements QuizService {  
    // Repository: 인젝션  
    @Autowired  
    QuizRepository repository;  
  
    @Override  
    public Iterable<Quiz> selectAll() {  
        return repository.findAll();  
    }  
  
    @Override  
    public Optional<Quiz> selectOneById(Integer id) {  
        return repository.findById(id);  
    }  
  
    @Override  
    public Optional<Quiz> selectOneRandomQuiz() {  
        // 랜덤으로 id 값을 가져오기  
        Integer randId = repository.getRandomId(); // <- 현재 오류  
  
        // 퀴즈가 없는 경우  
        if (randId == null) {  
            // 빈 Optional 인스턴스를 반환  
            return Optional.empty();  
        }  
        return repository.findById(randId);  
    }  
      
    @Override  
    public Boolean checkQuiz(Integer id, Boolean myAnswer) {  
        // 퀴즈 정답/오답 판단용 변수  
        Boolean check = false;  
        // 대상 퀴즈를 가져오기  
        Optional<Quiz> optQuiz = repository.findById(id);  
        // 퀴즈를 가져왔는지 확인  
        if (optQuiz.isPresent()) {  
            Quiz quiz = optQuiz.get();  
            // 퀴즈 정답 확인  
            if (quiz.getAnswer().equals(myAnswer)) {  
                check = true;  
            }  
        }  
        return check;  
    }  
      
    @Override  
    public void insertQuiz(Quiz quiz) {  
        repository.save(quiz);  
    }  
      
    @Override  
    public void updateQuiz(Quiz quiz) {  
        repository.save(quiz);  
    }  
      
    @Override  
    public void deleteQuizById(Integer id) {  
        repository.deleteById(id);  
    }  
}
```
- `@Service` 어노테이션을 클래스에 부여해서 인스턴스 생성 대상으로 지정함
- `@Autowired`로 `QuizRepository`를 주입(인젝션)함
- `selectOneRandomQuiz` 메서드에서 퀴즈 정보를 랜덤으로 한 건 가져오는 내요응ㄹ 작성함
- `getRandomId` 메서드는 `CrudRepository`를 상속해서 사용하는 메서드가 아니라 별도로 작성해야 하는 메서드임(==현재는 작성하지 않았기 때문에 에러가 표시될 것임==)
- `checkQuiz` 메서드에서 퀴즈의 정답/오답을 판단하는 내용을 작성함
- 그 밖의 메서드는 `QuizRepository`에 처리를 위임함

3. RepositoryImpl에 추가
- `CrudRepository`에 없는 메서드는 해당 메서드에 `@Query` 어노테이션을 부여해서 어노테이션의 인수에 SQL을 추가하는 것으로 정의할 수 있음
- `QuizRepository` 인터페이스에 아래 메서드를 추가함
```java
// QuizRepository 인터페이스

...

public interface QuizRepository extends CrudRepository<Quiz, Integer> {  
    @Query("SELECT id FROM quiz ORDER BY RANDOM() limit 1")  
    Integer getRandomId();  
}
```
- `@Query` 어노테이션의 인수에 quiz 테이블의 id 칼ㄹ럼을 랜덤으로 1건 가져오는 SQL을 작성함

3. ServiceImpl 추가
- `QuizServiceImpl` 클래스에 `@Transactional` 어노테이션을 부여하면 그 클래스의 모든 메서드에 트랜잭션 제어를 할 수 있음
- `QuizServiceImpl` 클래스에 `@Transactional` 어노테이션을 부여해서 아래와 같이 수정함

>[트랜잭션에 대해 알아보기](./트랜잭션_관리.md)

```java
// QuizServiceImpl 클래스

... 

@Service  
@Transactional   // <- 추가
public class QuizServiceImpl implements QuizService {
	...
}
```

### 2) quiz 테이블의 초기화
- `pgAdmin 4`를 실행해서 `Servers → PostgreSQL XX → Database → quizdb → Schemas → Tables`를 선택함
- `Tables → quiz`를 선택하고 메뉴바에서 `Tools → Query Tool`을 선택함
- `Query Editor` 화면에 다음 SQL을 입력함
```sql
DELETE FROM quiz;
SELECT setval('quiz_id_seq', 1, false);
```
- quiz 테이블의 데이터를 모두 삭제하고, setval로 quiz 테이블의 id 칼럼의 serial 타입 일련번호를 '1'로 초기화함
- 실행 버튼을 누르고 `View Data`를 눌러보면 초기화 된 것을 확인할 수 있음

### 3) QuizApplication 수정
- 아래와 같이 `QuizApplication`을 수정함
```java
// QuizApplication 클래스 전체 수정

package com.example.quiz;  
  
import com.example.quiz.entity.Quiz;  
import com.example.quiz.service.QuizService;  
  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.boot.SpringApplication;  
import org.springframework.boot.autoconfigure.SpringBootApplication;  
  
import java.util.ArrayList;  
import java.util.Collections;  
import java.util.List;  
import java.util.Optional;  
  
@SpringBootApplication  
public class QuizApplication {  
   // 구동 메서드  
   public static void main(String[] args) {  
      SpringApplication.run(QuizApplication.class, args)  
            .getBean(QuizApplication.class).execute();  
   }  
  
   // 주입(인젝션)  
   @Autowired  
   QuizService service;  
  
   // 실행 메서드  
   private void execute() {  
      // 등록 처리  
      // setup();  
  
      // 전체 항목 취득  
      // showList();  
  
      // 1건 취득  
      // showOne();  
  
      // 변경 처리  
      // updateQuiz();  
  
      // 삭제 처리  
      // deleteQuiz();  
  
      // 퀴즈 실행  
      // doQuiz();  
   }  
  
   // == 퀴즈 5건을 등록함 ==
   private void setup() {  
      System.out.println("--- 등록 처리 개시 ---");  
      // 엔티티 생성  
      Quiz quiz1 = new Quiz(null, "자바는 객체지향 언어입니다.", true, "등록 담당");  
      Quiz quiz2 = new Quiz(null, "스프링 데이터는 데이터 액세스에 관련된 기능을 제공합니다.", true, "등록 담당");  
      Quiz quiz3 = new Quiz(null, "프로그램이 많이 등록되어 있는 서버를 라이브러리라고 합니다.", false, "등록 담당");  
      Quiz quiz4 = new Quiz(null, "@Component는 인스턴스 생성 어노테이션입니다.", true, "등록 담당");  
      Quiz quiz5 = new Quiz(null, "스프링 MVC에서 구현하고 있는 디자인 패턴에서 모든 요청을 하나의 컨트롤러에 받는 것을 싱글 컨트롤러 패턴이라고 합니다.", false, "등록 담당");  
      // 리스트에 엔티티를 저장  
      List<Quiz> quizList = new ArrayList<>();  
      // 첫 인수에 저장될 객체를, 두 번째 인수부터는 저장할 엔티티를 넘겨줌  
      Collections.addAll(quizList, quiz1, quiz2, quiz3, quiz4, quiz5);  
      // 등록 실행  
      for (Quiz quiz: quizList) {  
         // 등록 실행  
         service.insertQuiz(quiz);  
      }  
      System.out.println("--- 등록 처리 완료 ---");  
   }  
  
   // == 모든 데이터 취득 ==
   private void showList() {  
      System.out.println("--- 모든 데이터 취득 개시 ---");  
      // 리포지토리를 이용해 모든 데이터를 취득해서 결과를 반환  
      Iterable<Quiz> quizzes = service.selectAll();  
      for (Quiz quiz: quizzes) {  
         System.out.println(quiz);  
      }  
      System.out.println("--- 모든 데이터 취득 완료 ---");  
   }  
  
   // == 한 건 데이터 취득 ==
   private void showOne() {  
      System.out.println("--- 1건 취득 개시 ---");  
  
      // 리포지토리를 사용해서 1건의 데이터를 취득해서 결과를 반환(반환값은 Optional)
      Optional<Quiz> quizOpt = service.selectOneById(1);  
  
      // 반환값이 있는지 확인  
      if (quizOpt.isPresent()) {  
         System.out.println(quizOpt.get());  
      } else {  
         System.out.println("해당 데이터는 존재하지 않습니다.");  
      }  
      System.out.println("--- 1건 취득 완료 ---");  
   }  
  
   // == 변경 처리 ==
   private void updateQuiz() {  
      System.out.println("--- 변경 처리 개시 ---");  
      // 변경할 엔티티를 생성  
      Quiz quiz1 = new Quiz(1, "스프링은 프레임워크입니까?", true, "변경 담당");  
      // 변경 처리  
      service.updateQuiz(quiz1);  
      // 변경 결과 확인  
      System.out.println("변경된 데이터는 " + quiz1 + "입니다.");  
      System.out.println("--- 변경 처리 완료 ---");  
   }  
  
   // == 삭제 처리 ==
   private void deleteQuiz() {  
      System.out.println("--- 삭제 처리 개시 ---");  
      // 삭제 실행  
      service.deleteQuizById(2);  
      System.out.println("--- 삭제 처리 완료 ---");  
   }  
     
   // == 랜덤으로 퀴즈를 취득해서 퀴즈의 정답/오답을 평가 ==
   private void doQuiz() {  
      System.out.println("--- 퀴즈 1건 취득 개시 ---");  
      // 리포지토리를 이용해서 1건의 데이터를 받기(반환값은 Optional)
      Optional<Quiz> quizOpt = service.selectOneRandomQuiz();  
      // 반환값이 있는지 확인  
      if (quizOpt.isPresent()) {  
         System.out.println(quizOpt.get());  
      } else {  
         System.out.println("해당 데이터는 존재하지 않습니다.");  
      }  
      System.out.println("--- 퀴즈 1건 취득 완료 ---");  
      // 답 평가  
      Boolean myAnswer = false;  
      Integer id = quizOpt.get().getId();  
      if (service.checkQuiz(id, myAnswer)) {  
         System.out.println("정답입니다!");  
      } else {  
         System.out.println("오답입니다..");  
      }  
   }  
}
```
- `QuizService`를 `@Autowired` 어노테이션으로 주입(인젝션)해서 비즈니스 로직을 처리함
- `main` 메서드에서 `execute` 메서드를 호출해서 `execute` 메서드 안의 애플리케이션에서 실행하려는 각 메서드를 호출함(현재 주석처리 되어 있음)

### 4) 등록/참조 처리
1. execute 메서드 수정
- QuizApplication 클래스의 내용은 아래와 같음
- setup(), showList(), showOne()의 주석 처리를 제거함

2. 실행과 확인
- `QuizApplication` 파일을 선택하고 마우스 오른쪽 버튼을 클릭해서 `실행`을 선택함
- 정상적으로 실행되면 `CrudRepository`를 상속한 `QuizRepository` 클래스의 등록 처리와 실행 처리가 되는 것을 확인할 수 있음

<img src="https://user-images.githubusercontent.com/77138259/229684055-f4887cb5-15db-4dbe-97f0-90b80ea06169.png" alt="실행 결과(콘솔)" />

<img src="https://user-images.githubusercontent.com/77138259/229684239-d112373e-b750-45a0-be40-b0bc433afb7d.png" alt="실행 결과(quiz 테이블)" />

### 5) 갱신/삭제 처리
1. execute 메서드 수정
- QuizApplication 클래스의 내용은 아래와 같음
- 이번에는 setup(), showList(), showOne()을 주석 처리함
- updateQuiz(), deleteQuiz() 메서드의 주석 처리를 제거함

2. 실행과 확인
- `QuizApplication` 파일을 선택하고 마우스 오른쪽 버튼을 클릭해서 `실행`을 선택함
- 정상적으로 실행되면 `CrudRepository`를 상속한 `QuizRepository` 클래스의 변경 처리와 삭제 처리가 되는 것을 확인할 수 있음
- quiz 테이블에서는 id가 '1'인 항목이 변경되었고, id가 '2'인 항목이 삭제되었음

<img src="https://user-images.githubusercontent.com/77138259/229684649-daaaba87-cca1-4239-93e8-0bc8773cef2c.png" alt="실행 결과(콘솔)" />

<img src="https://user-images.githubusercontent.com/77138259/229684709-ec89a6f6-686b-4d09-9c0f-9ea124e946b7.png" alt="실행 결과(quiz 테이블)" />

### 6) 퀴즈 처리
1. execute 메서드 수정
- QuizApplication 클래스의 내용은 아래와 같음
- doQuiz() 메서드 외에는 모두 주석 처리함
- doQuiz 메서드에서는 랜덤으로 퀴즈를 한 건 취득한 후 해답을 false로 설정해서 QuizServiceImpl의 checkQuiz 메서드로 퀴즈의 정답/오답을 판단함

2. 실행과 확인
- `QuizApplication` 파일을 선택하고 마우스 오른쪽 버튼을 클릭해서 `실행`을 선택함
- 정상적으로 실행되면 `CrudRepository`를 상속한 `QuizRepository` 클래스의 `getRandomId` 메서드가 실행된 것을 확인할 수 있음
- 또한 `checkQuiz` 메서드의 실행 결과도 확인할 수 있음

<img src="https://user-images.githubusercontent.com/77138259/229685250-ffd13cfe-15fc-45b6-bac4-0b585220daca.png" alt="실행 결과(콘솔)" />

### 7) QuizApplication 수정
- QuizServiceImpl 클래스의 모든 메서드의 동작이 확인되었으므로 다음 진행을 위해서 QuizApplication을 원래대로 돌려놓음
```java
// QuizApplication 클래스

package com.example.quiz;  
  
import org.springframework.boot.SpringApplication;  
import org.springframework.boot.autoconfigure.SpringBootApplication;  
  
@SpringBootApplication  
public class QuizApplication {  
   // 구동 메서드  
   public static void main(String[] args) {  
      SpringApplication.run(QuizApplication.class, args)  
            .getBean(QuizApplication.class, args);  
   }  
}
```

# 5. 애플리케이션 레이어 만들기
- 3개의 HTML 파일을 생성할 것임
	- crud.html: 등록/변경/삭제/참조 화면
	- play.html: 퀴즈를 랜덤으로 표시하는 화면
	- answer.html: 해답 화면

## 목록 표시
### 1) Form 생성
- Form은 화면에 표시되는 내용과 대응되는 폼을 말함
>[Form 클래스 확인하러 가기](./입력값을_받는_프로그램_만들기.md)

- '`src/main/java → com.example.quiz`'에서 마우스 오른쪽 버튼을 클릭해 '`새로 만들기 → 패키지`'를 선택하여 `com.example.quiz.form` 패키지를 생성
- `com.example.quiz.form` 패키지에서 '`새로 만들기 → Java 클래스`' 선택 및 클래스명을 `QuizForm`으로 지정
- 클래스 내용을 다음과 같이 입력

```java
// QuizForm 클래스

package com.example.quiz.form;  
  
import jakarta.validation.constraints.NotBlank;  
import lombok.AllArgsConstructor;  
import lombok.Data;  
import lombok.NoArgsConstructor;  
  
@Data  
@NoArgsConstructor  
@AllArgsConstructor  
public class QuizForm {  
    // 식별 ID    private Integer id;  
    // 퀴즈 내용  
    @NotBlank  
    private String question;  
    // 퀴즈 해답  
    private Boolean answer;  
    // 작성자  
    @NotBlank  
    private String author;  
    // 등록 또는 변경 판단용  
    private Boolean newQuiz;  
}
```
- `@NotBlank`는 단일 항목 검사로 빈(null) 객체를 허용하지 않는 어노테이션임
>[유효성 검사 확인하러 가기](./유효성_검사.md)

- `crud.html`은 등록과 변경을 표시할 때 내용을 바꿔야 하므로 '등록 또는 변경' 판단용으로 `Boolean` 필드(`newQuiz`)를 만들었음
- `true`일 때는 등록, `false`일 때는 변경의 뜻으로 사용함

### 2) Controller 생성
- Controller에서 요청 핸들러 메서드를 생성함
>[요청 핸들러 메서드 알아보기](./스프링_MVC.md)

- '`src/main/java → com.example.quiz`'에서 마우스 오른쪽 버튼을 클릭해 '`새로 만들기 → 패키지`'를 선택하여 `com.example.quiz.controller` 패키지를 생성
- `com.example.quiz.controller` 패키지에서 '`새로 만들기 → Java 클래스`' 선택 및 클래스명을 `QuizController`으로 지정
- 클래스 내용을 다음과 같이 입력
```java
// QuizController 클래스

package com.example.quiz.controller;  
  
import com.example.quiz.entity.Quiz;  
import com.example.quiz.form.QuizForm;  
import com.example.quiz.service.QuizService;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.stereotype.Controller;  
import org.springframework.ui.Model;  
import org.springframework.web.bind.annotation.GetMapping;  
import org.springframework.web.bind.annotation.ModelAttribute;  
import org.springframework.web.bind.annotation.RequestMapping;  
  
@Controller  
@RequestMapping("/quiz")  
public class QuizController {  
    // DI 대상  
    @Autowired  
    QuizService service;  
  
    // form-backing bean의 초기화  
    @ModelAttribute  
    public QuizForm setUpForm() {  
        QuizForm form = new QuizForm();  
        // 라디오 버튼의 초깃값 설정  
        form.setAnswer(true);  
        return form;  
    }  
  
    // Quiz 목록 표시  
    @GetMapping  
    public String showList(QuizForm quizForm, Model model) {  
        // 신규 등록 설정  
        quizForm.setNewQuiz(true);  
  
        // 퀴즈 목록 취득  
        Iterable<Quiz> list = service.selectAll();  
  
        // 표시용 모델에 저장  
        model.addAttribute("list", list);  
        model.addAttribute("title", "등록 폼");  
  
        return "crud";  
    }  
}
```
- `@Controller` 어노테이션으로 인스턴스를 생성함
- `@RequestMapping("/quiz")`를 지정한 클래스의 요청 핸들러 메서드를 호출하는 경우에는 URL에 '/quiz'를 붙임
- `@Autowired` 어노테이션으로 `QuizService`를 주입하고, 입력 체크를 하기 위해서 `@ModelAttribute` 어노테이션으로 `QuizForm을 form-backing bean`으로 만들 때 라디오 버튼에 초깃값을 설정함
- `quizForm.setNewQuiz(true)`를 설정해서 등록 화면을 표시함
- 마지막으로 반환값으로 "crud"를 넘겨주는 것으로 `crud.html`을 표시함

### 3) crud.html 생성
- `src/main/resources/templates` 폴더에서 마우스 오른쪽 버튼을 클릭해 '`새로 만들기 => HTML 파일`'를 선택하여 '`crud.html`' 파일 생성
- 아래와 같이 입력
```html
<!-- crud.html -->

<!DOCTYPE html>  
<html lang="en" xmlns:th="http://www.thymeleaf.org">  
<head>  
    <meta charset="UTF-8">  
    <title>OX 퀴즈 애플리케이션: CRUD</title>  
</head>  
<body>  
	<h1>OX 퀴즈 애플리케이션: CRUD</h1>  
	<h3 th:text="${title}">제목</h3>  
	<!--등록/변경 완료 메세지-->  
	<p th:if="${complete}" th:text="${complete}" style="color:blue"></p>  
	<!--⬇⬇⬇ Form ⬇⬇⬇-->  
	<form method="POST"  
	    th:action="${quizForm.newQuiz}? @{/quiz/insert} : @{/quiz/update}" th:object="${quizForm}">  
	    <label>퀴즈 내용: </label><br>  
	    <textarea rows="5" cols="50" th:field="*{question}"></textarea>  
	    <br>    <div th:if="${#fields.hasErrors('question')}" th:errors="*{question}" style="color:red">  
	    </div>    <label>퀴즈 정답: </label><br>  
	    <input type="radio" value="true" th:field="*{answer}"> O  
	    <input type="radio" value="false" th:field="*{answer}"> X  
	    <br>  
	    <div th:if="${#fields.hasErrors('answer')}" th:errors="*{answer}" style="color:red"></div>  
	    <label>작성자: </label><br>  
	    <input type="text" th:field="*{author}" />  
	    <br>    <div th:if="${#fields.hasErrors('author')}" th:errors="*{author}" style="color:red"></div>  
	    <input th:if="${id}" type="hidden" th:field="*{id}">  
	    <input type="submit" value="송신">  
	</form>  
	<!--⬆⬆⬆ Form ⬆⬆⬆-->  
	<br>  
	<!--===== 여기까지가 상부 영역 =====--><hr>  
	<!--===== 여기서부터 하부 영역 =====--><!--⬇⬇⬇ 신규 등록할 때만 표시 ⬇⬇⬇-->  
	<div th:if="${quizForm.newQuiz}" style="margin: 10px">  
	    <h3>등록된 퀴즈 목록  
	        <a th:href="@{/quiz/play}">플레이</a><br>  
	    </h3>    <!--삭제 완료 메세지-->  
	    <p th:if="${delcomplete}" th:text="${delcomplete}" style="color:blue"></p>  
	    <p th:if="${msg}" th:text="${msg}" style="color:red"></p>  
	    <!--⬇⬇⬇ 퀴즈 정보가 있으면 표시 ⬇⬇⬇-->  
	    <table border="1" th:unless="${#lists.isEmpty(list)}" style="table-layout: fixed;">  
	        <tr>            <th>ID</th>  
	            <th>내용</th>  
	            <th>해답</th>  
	            <th>작성자</th>  
	            <th>변경</th>  
	            <th>삭제</th>  
	        </tr>        <tr th:each="obj : ${list}" align="center">  
	            <td th:text="${obj.id}"></td>  
	            <td th:text="${obj.question}" align="center"></td>  
	            <td th:text="${obj.answer} == true ? 'O' : 'X'"></td>  
	            <td th:text="${obj.author}"></td>  
	            <!--변경 버튼-->  
	            <td>  
	                <form method="GET" th:action="@{/quiz/{id}(id=${obj.id})}">  
	                    <input type="submit" value="변경">  
	                </form>            </td>            <!--삭제 버튼-->  
	            <td>  
	                <form method="POST" th:action="@{/quiz/delete}">  
	                    <input type="hidden" name="id" th:value="${obj.id}">  
	                    <input type="submit" value="삭제">  
	                </form>            </td>        </tr>    </table>    <!--⬆⬆⬆ 퀴즈 정보가 있으면 표시 ⬆⬆⬆-->  
	    <!--⬇⬇⬇ 퀴즈 정보가 없으면 표시 ⬇⬇⬇-->  
	    <p th:if="${#lists.isEmpty(list)}">등록된 퀴즈가 없습니다.</p>  
	    <!--⬆⬆⬆ 퀴즈 정보가 없으면 표시 ⬆⬆⬆-->  
	</div>  
	<!--⬆⬆⬆ 신규 등록할 때만 표시 ⬆⬆⬆-->  
	<!--⬇⬇⬇ 신규 등록이 아닐 때 표시 ⬇⬇⬇-->  
	<p th:unless="${quizForm.newQuiz}">  
	    <a href="#" th:href="@{/quiz}">CRUD 화면에 돌아가기</a>  
	</p>  
	<!--⬆⬆⬆ 신규 등록이 아닐 때 표시 ⬆⬆⬆-->  
</body>  
</html>
```
- `<form>` 태그에서 "`${quizForm.newQuiz} ? @{/quiz/insert} : @{/quiz/update}`"로 등록과 변경 처리를 분리하고 있음
- 같은 `<form>` 태그에서 `th:object="${quizForm}"`을 설정해서 모델에 저장된 Form 클래스의 소문자 카멜케이스를 지정해서 사용함
- `Form` 클래스의 필드와 관련 짓기 위해 th:field 속성에 "`*{필드명}`"을 지정함
- `th:if="${quizForm.newQuiz}"`를 사용해서 등록할 때만 표시하는 하부 영역을 작성함. 등록할 때 하부 영역에는 퀴즈 목록 이 표시됨
- `th:unless="${#lists.isEmpth(list)}"`를 이용해 퀴즈 정보가 있을 경우 퀴즈 목록을 표시함
- `th:if="${#lists.isEmpty(list)}"`를 사용해서 등록된 퀴즈가 없는 경우 '==등록된 퀴즈가 없습니다=='를 표시
- `th:action="@{./quiz/{id}(id=${obj.id})}"`에서 URL 경로 안에 { }로 감싼 변수 끝에 ( )를 사용해서 값을 대입함

### 4) quiz 테이블을 초기화함
- `pgAdmin 4`를 실행해서 `Servers → PostgreSQL XX → Database → quizdb → Schemas → Tables`를 선택함
- `Tables → quiz`를 선택하고 메뉴바에서 `Tools → Query Tool`을 선택함
- `Query Editor` 화면에 다음 SQL을 입력함
```sql
DELETE FROM quiz;
SELECT setval('quiz_id_seq', 1, false);
```
- quiz 테이블의 데이터를 모두 삭제하고, setval로 quiz 테이블의 id 칼럼의 serial 타입 일련번호를 '1'로 초기화함
- 실행 버튼을 누르고 `View Data`를 눌러보면 초기화 된 것을 확인할 수 있음

### 5) quiz URL 확인
- `QuizApplication` 파일을 선택하고 마우스 오른쪽 버튼을 클릭해서 `실행`을 선택함
- 애플리케이션이 시작된 것을 확인한 후에 브라우저를 열어서 주소 표시줄에 'http://localhost:8080/quiz'를 입력하면 CRUD 화면이 표시됨
- 상부 영역에는 등록 화면이, 하부 영역에는 목록 화면이 표시됨
- quiz 테이블을 초기화해서 등록된 퀴즈가 없기 때문에 등록된 퀴즈가 없다는 메세지가 표시됨

<img src="https://user-images.githubusercontent.com/77138259/229710952-78ffa2a7-f997-488b-b261-95222d3186e4.png" alt="실행 결과(CRUD 화면)" />
## 등록/변경/삭제 기능
### 1) 등록 기능 만들기
- Controller에 등록 기능을 작성함
- 앞의 Form 생성에서 Form의 단일 항목 검사를 설정했으므로 @Validated 어노테이션과 BindingResult 인터페이스를 사용해서 유효성 검사를 완성하겠음
- QuizController 클래스에 insert 메서드를 추가함
```java
// QuizController 클래스

...

@Controller  
@RequestMapping("/quiz")  
public class QuizController {
	...
	
	// Quiz 데이터를 1건 등록  
	@PostMapping("/insert")  
	public String insert(@Validated QuizForm quizForm, BindingResult bindingResult, Model model, RedirectAttributes redirectAttributes) {  
	    // Form에서 Entity로 넣기  
	    Quiz quiz = new Quiz();  
	    quiz.setQuestion(quizForm.getQuestion());  
	    quiz.setAnswer(quizForm.getAnswer());  
	    quiz.setAuthor(quizForm.getAuthor());  
	    // 입력 체크  
	    if (!bindingResult.hasErrors()) {  
	        service.insertQuiz(quiz);  
	        redirectAttributes.addFlashAttribute("complete", "등록이 완료되었습니다.");  
	        return "redirect:/quiz";  
	    } else {  
	        // 에러가 발생한 경우에는 목록 표시로 변경  
	        return showList(quizForm, model);  
	    }  
	}
}
```
- `@Validated` 어노테이션을 단일 항목 검사 어노테이션을 설정하는 `Form` 클래스에 부여하면 유효성 검사가 실행되고 실행한 결과(에러 정보)는 `BindingResult` 인터페이스에 저장됨
- `BindingResult` 인터페이스의 `hasErrors` 메서드의 반환값(true: 에러 있음, false: 에러 없음)으로 결과 확인이 가능함. 에러가 발생한 경우에는 목록 표시 처리를 함
- 메서드에서 `RedirectAttributes`를 인수로 설정하고 '`redirectAttributes.addFlashAttribute("complete", "등록이 완료되었습니다.")`'로 작성하여 리다이렉트할 화면에 전달할 값을 설정할 수 있음
- 이것이 `Flash Scope`라는 범위로서 한 번의 리다이렉트에서만 유효한 스코프임

>'quiz/insert' URL 확인
- CRUD 화면에서 퀴즈 정보 입력을 해보겠음
- 우선 아무것도 입력하지 않은 상태에서 `송신` 버튼을 클릭해서 입력 체크가 정상적으로 작동하고 에러 메세지가 표시되는 것을 확인함
<div style="display:flex; width:100%; box-sizing:border-box">
<img style="width:50%" src="https://user-images.githubusercontent.com/77138259/229714182-3a976741-0944-4622-a1f9-739975ad93ab.png" alt="확인: 초기 화면" />
<img style="width:50%" src="https://user-images.githubusercontent.com/77138259/229714207-043541c5-1abb-4462-9585-f4cd7e3b35d7.png" alt="확인: 등록 처리(실패)" />
</div>

- 이번에는 퀴즈 정보를 입력한 후 `송신` 버튼을 클릭해서 등록이 성공했다는 메세지와 등록한 퀴즈가 목록에 표시되는 것을 확인함
<div style="display:flex; width:100%; box-sizing:border-box">
<img style="width:50%" src="https://user-images.githubusercontent.com/77138259/229714638-4eb05b12-9914-4f7f-ac91-65837cec60ed.png" alt="확인: 초기 화면/입력 중" />
<img style="width:50%" src="https://user-images.githubusercontent.com/77138259/229714723-e09f1ebb-2e84-4bcf-9efe-d9792cfd85b5.png" alt="확인: 등록 처리(성공)" />
</div>

### 2) 변경 기능 만들기
- Controller에 변경 기능을 작성함
- 우선 지정한 퀴즈 id를 가진 엔티티를 취득해서 변경 화면을 표시하고 변경 처리를 함
- QuizController 클래스에 변경 기능을 수행하는 메서드를 추가함
```java
// QuizController 클래스

...

@Controller  
@RequestMapping("/quiz")  
public class QuizController {
	...

	// Quiz 데이터를 1건 취득해서 폼 안에 표시  
	@GetMapping("/{id}")  
	public String showUpdate(QuizForm quizForm, @PathVariable Integer id, Model model) {  
	    // Quiz를 취득(Optional로 래핑)  
	    Optional<Quiz> quizOpt = service.selectOneById(id);  
	    // QuizForm에 채워넣기  
	    Optional<QuizForm> quizFormOpt = quizOpt.map(t -> makeQuizForm(t));  
	    // QuizForm이 null이 아니라면 값을 취득  
	    if (quizFormOpt.isPresent()) {  
	        quizForm = quizFormOpt.get();  
	    }  
	      
	    // 변경용 모델 생성  
	    makeUpdateModel(quizForm, model);  
	    return "crud";  
	}  
	  
	// 변경용 모델 생성  
	private void makeUpdateModel(QuizForm quizForm, Model model) {  
	    model.addAttribute("id", quizForm.getId());  
	    quizForm.setNewQuiz(false);  
	    model.addAttribute("quizForm", quizForm);  
	    model.addAttribute("title", "변경 폼");  
	}  
	  
	// id를 키로 사용해 데이터를 변경  
	@PostMapping("/update")  
	public String update(  
	        @Validated QuizForm quizForm,  
	        BindingResult result,  
	        Model model,  
	        RedirectAttributes redirectAttributes) {  
	    // QuizForm에서 Quiz로 채우기  
	    Quiz quiz = makeQuiz(quizForm);  
	    // 입력 체크  
	    if (!result.hasErrors()) {  
	        // 변경 처리, Flash scope를 사용해서 리다이렉트 설정  
	        service.updateQuiz(quiz);  
	        redirectAttributes.addFlashAttribute("complete", "변경이 완료되었습니다.");  
	        // 변경 화면을 표시  
	        return "redirect:/quiz/" + quiz.getId();  
	    } else {  
	        // 변경용 모델을 생성  
	        makeUpdateModel(quizForm, model);  
	        return "crud";  
	    }  
	}  
	  
	// --------- 아래는 Form과 도메인 객체를 다시 채우기 ---------
	// QuizForm에서 Quiz로 다시 채우기, 반환값으로 돌려줌  
	private Quiz makeQuiz(QuizForm quizForm) {  
	    Quiz quiz = new Quiz();  
	    quiz.setId(quizForm.getId());  
	    quiz.setQuestion(quizForm.getQuestion());  
	    quiz.setAnswer(quizForm.getAnswer());  
	    quiz.setAuthor(quizForm.getAuthor());  
	    return quiz;  
	}  
	  
	// Quiz에서 QuizForm으로 다시 채우기, 반환값으로 돌려줌  
	private QuizForm makeQuizForm(Quiz quiz) {  
	    QuizForm form = new QuizForm();  
	    form.setId(quiz.getId());  
	    form.setQuestion(quiz.getQuestion());  
	    form.setAnswer(quiz.getAnswer());  
	    form.setAuthor(quiz.getAuthor());  
	    form.setNewQuiz(false);  
	    return form;  
	}
}
```
- `showUpdate` 메서드에서 퀴즈 `id`에 대응하는 엔티티를 취득함
- `map` 메서드는 `Optional` 메서드로 값이 있을 때만 그 값으로 채움
- `map(t -> makeQuizForm(t))`로 람다식 표기를 함
- `makeQuizForm` 메서드를 사용해서 `Quiz`에서 `QuizForm`으로 값을 채워 넣음
- `makeUpdateModel`은 변경 화면을 만들기 위한 메서드임
- `update` 메서드는 등록 기능의 `insert` 메서드와 같은 역할을 함

>'quiz/{id} URL 확인'
- CRUD 화면 하부 영역의 목록에서 변경하고 싶은 퀴즈 정보 레코드의 `변경` 버튼을 클릭하면 변경 화면이 표시됨
<div style="display:flex; width:100%; box-sizing:border-box">
<img style="width:50%" src="https://user-images.githubusercontent.com/77138259/229719692-8c332c05-e754-4ef4-8e5a-1cf561088b5f.png" alt="확인: 초기 화면" />
<img style="width:50%" src="https://user-images.githubusercontent.com/77138259/229719796-5b1b4225-d920-4bf1-871c-d1b643270c75.png" alt="확인: 변경 화면 표시" />
</div>

>'quiz/update' URL ghkrdls
- 변경 화면에서 퀴즈 정보를 다 지우고 `송신` 버튼을 클릭하면 입력 체크에서 걸려서 에러 메세지가 표시됨
<img src="https://user-images.githubusercontent.com/77138259/229720178-a1040c7e-f1f7-4c9d-b759-6b728984e0b1.png" alt="변경 처리(실패)" />

- 다시 변경 내용을 입력하고 `송신` 버튼을 클릭하면 변경이 성공하고 메세지가 표시됨
- CRUD 화면으로 돌아오면 목록에 변경된 내용이 반영되어 있음
<div style="display:flex; width:100%; box-sizing:border-box">
<img style="width:50%" src="https://user-images.githubusercontent.com/77138259/229720679-09640961-fe5e-4e60-87a6-45a32db1dfec.png" alt="변경 처리(진행 중)" />
<img style="width:50%" src="https://user-images.githubusercontent.com/77138259/229720766-54acf19e-075a-43f2-8614-5247b8bac380.png" alt="변경 처리(성공)" />
</div>
<img src="https://user-images.githubusercontent.com/77138259/229721072-fdc7df8b-21cd-43bf-abcd-5bc4ee42f6de.png" alt="CRUD 화면" />

### 3) 삭제 기능 만들기
- `Controller`에 삭제 기능을 추가함
- 지정한 퀴즈 id의 퀴즈 정보를 삭제함. 삭제하면 목록에서 레코드가 사라짐
- `QuizController` 클래스에 `delete` 메서드를 추가함
```java
// QuizController 클래스

...

@Controller  
@RequestMapping("/quiz")  
public class QuizController {
	...
	// id를 키로 사용해 데이터를 삭제  
	@PostMapping("/delete")  
	public String delete(  
	        @RequestParam("id") String id,  
	        Model model,  
	        RedirectAttributes redirectAttributes) {  
	    // 퀴즈 정보를 1건 삭제하고 리다이렉트  
	    service.deleteQuizById(Integer.parseInt(id));  
	    redirectAttributes.addFlashAttribute("delcomplete", "삭제 완료했습니다.");  
	    return "redirect:/quiz";  
	}
}
```
- 마지막 줄의 'redirect:' 의 반환값으로 리다이렉트됨

>'quiz/delete URL 확인'
- 테스트를 위해 2개 값을 추가로 넣음
- CRUD 화면 하부 영역의 목록에서 삭제하고 싶은 퀴즈 정보 레코드에서 `삭제` 버튼을 클릭하면 퀴즈 정보가 삭제되고 목록에서 사라짐
- 삭제가 성공하면 성공 메세지가 표시됨
<div style="display:flex; width:100%; box-sizing:border-box">
<img style="width:50%" src="https://user-images.githubusercontent.com/77138259/229723051-bd5639e8-5173-4a29-8b31-8766c09c69c7.png" alt="확인: 초기 화면" />
<img style="width:50%" src="https://user-images.githubusercontent.com/77138259/229723395-e7fa8885-51bc-4352-a8a7-354f048b9585.png" alt="확인: 삭제 처리(성공)" />
</div>


## 게임 기능
### 1) play.html 생성
- `src/main/resources/templates` 폴더에서 마우스 오른쪽 버튼을 클릭해 '`새로 만들기 => HTML 파일`'를 선택하여 '`play.html`' 파일 생성
- 아래와 같이 입력
```html
<!-- play.html -->

<!DOCTYPE html>  
<html lang="en" xmlns:th="http://www.thymeleaf.org">  
<head>  
    <meta charset="UTF-8">  
    <title>OX 퀴즈 애플리케이션: PLAY</title>  
</head>  
<body>  
	<h1>OX 퀴즈 애플리케이션: PLAY</h1>  
	<h3>퀴즈</h3>  
	<th:block th:if="${msg}">  
	    <p th:text="${msg}" style="color: red"></p>  
	    <a href="@{/quiz/}">CRUD 화면에 돌아가기</a>  
	</th:block>  
	<th:block th:unless="${msg}">  
	    <p th:text="${quizForm.question}">퀴즈 내용</p>  
	    <form th:action="@{/quiz/check}" th:object="${quizForm}" method="POST">  
	        <input type="hidden" th:field="*{id}">  
	        <button name="answer" value="true">O</button>  
	        <button name="answer" value="false">X</button>  
	    </form>
	</th:block>  
</body>  
</html>
```
- `${msg}` 값의 유무로 처리가 나뉨
- `${msg}`에 값이 있으면 '`등록된 문제가 없습니다.`'가 표시되고, `${msg}`에 값이 없으### 2) 면 랜덤으로 가져온 퀴즈가 표시됨

### 2) answer.html 생성
- `src/main/resources/templates` 폴더에서 마우스 오른쪽 버튼을 클릭해 '`새로 만들기 => HTML 파일`'를 선택하여 '`answer.html`' 파일 생성
- 아래와 같이 입력
```html
<!-- answer.html -->

<!DOCTYPE html>  
<html lang="en" xmlns:th="http://www.thymeleaf.org">  
<head>  
  <meta charset="UTF-8">  
  <title>OX 퀴즈 애플리케이션: 해답</title>  
</head>  
<body>  
	<h1>OX 퀴즈 애플리케이션: 해답</h1>  
	<h2 th:text="${msg}" style="color:red">메세지 표시 영역</h2>  
	<a th:href="@{/quiz/play}">리플레이</a><br>  
	<a th:href="@{/quiz}">CRUD 화면에 돌아가기</a>  
</body>  
</html>
```
- `${msg}`에서 퀴즈 정답/오답을 표시함

### 3) 게임 기능 작성
- `QuizController` 클래스에 게임 기능을 수행하는 메서드를 추가함
```java
// QuizController 클래스

...

@Controller  
@RequestMapping("/quiz")  
public class QuizController {
	...
	
	// Quiz 데이터를 랜덤으로 한 건 가져와 화면에 표시  
	@GetMapping("/play")  
	public String showQuiz(QuizForm quizForm, Model model) {  
	    // Quiz 정보 취득(Optional으로 래핑)  
	    Optional<Quiz> quizOpt = service.selectOneRandomQuiz();  
	  
	    // 값이 있는지 확인  
	    if (quizOpt.isPresent()) {  
	        // QuizForm으로 채우기  
	        Optional<QuizForm> quizFormOpt = quizOpt.map(t -> makeQuizForm(t));  
	        quizForm = quizFormOpt.get();  
	    } else {  
	        model.addAttribute("msg", "등록된 문제가 없습니다.");  
	        return "play";  
	    }  
	  
	    // 표시용 모델에 저장  
	    model.addAttribute("quizForm", quizForm);  
	  
	    return "play";  
	}  
	  
	// 퀴즈의 정답/오답 판단  
	@PostMapping("/check")  
	public String checkQuiz(QuizForm quizForm, @RequestParam Boolean answer, Model model) {  
	    if (service.checkQuiz(quizForm.getId(), answer)) {  
	        model.addAttribute("msg", "정답입니다!");  
	    } else {  
	        model.addAttribute("msg", "오답입니다..");  
	    }  
	    return "answer";  
	}
}
```
- `showQuiz` 메서드에서 랜덤으로 퀴즈를 가져와 표시함
- 등록된 퀴즈가 없는 경우에는 '`등록된 문제가 없습니다`' 라는 메세지를 설정함
- `checkQuiz` 메서드에서 퀴즈의 정답/오답을 판정함
- 대부분의 처리는 앞에서 주입(인젝션)한 `QuizService`에 위임함

>'quiz/play' URL 확인
- CRUD 화면 하부 영역의 '플레이' 링크를 클릭하면 퀴즈 화면이 표시됨
<div style="display:flex; width:100%; box-sizing:border-box">
<img style="width:50%" src="https://user-images.githubusercontent.com/77138259/229728710-f0f57c5c-9f30-45cc-bd01-32f6af05e275.png" alt="초기 화면 플레이 버튼 위치" />
<img style="width:50%; height:auto" src="https://user-images.githubusercontent.com/77138259/229728843-d3defdf3-f8fb-4d71-b390-9d3376ff394c.png" alt="퀴즈 화면 표시" />
</div>

<div style="display:flex; width:100%; box-sizing:border-box">
<img style="width:50%" src="https://user-images.githubusercontent.com/77138259/229729487-3b454d99-19ea-49dd-9ada-fb46fcba3b54.png" alt="해답 화면 표시(정답)" />
<img style="width:50%; height:auto" src="https://user-images.githubusercontent.com/77138259/229729755-3164c7a9-2884-448b-ad90-84fb1528bf26.png" alt="해답 화면 표시(오답)" />
</div>

