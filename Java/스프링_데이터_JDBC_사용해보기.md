- 현재 자바 프로그램 개발에서는 데이터베이스에 대한 액세스 처리를 O/R 매퍼라는 프레임워크를 사용해서 개발하는 경우가 많음
- 예제에서는 O/R 매퍼에 대해 간단히 설명하고 O/R 매퍼의 한 종류인 스프링 데이터 JDBC를 사용해 프로그램을 만들어보자

## O/R 매퍼란
- O/R 매퍼(Object-relational Mapper)는 애플리케이션에서 사용하는 O(Object): '객체'와 R(Relational): '관계형 데이터베이스'의 데이터를 매핑하는 것임
- O/R 매퍼는 미리 설정된 객체와 관계형 데이터베이스 간의 대응 관계 정보를 가지고 인터페이스의 데이터에 대응하는 테이블에 내보내거나 데이터베이스에서 값을 읽어 들여 인터페이스에 대입하는 작업을 자동으로 실행함

## 스프링 데이터 JDBC란
- 스프링 데이터 JDBC는 O/R 매퍼임
- 특징으로 스프링 데이터가 제공하는 CurdRepository를 상속해서 자동으로 CRUD를 지원하는 메서드를 사용할 수 있음


## 스프링 데이터 JDBC 프로그램 생성

### 1) 프로젝트 생성
- [Spring Initializr](./Spring_initializr_사용방법.md)에서 다음 설정 내용을 참조해서 다운로드 후 압축을 해제함

| **항목** | **값** |
| :--- | :--- |
| Project | Gradle - Groovy |
| Language | Java |
| Spring Boot | 3.0.5 |
| Artifact | SpringDataJDBCSample |
| Packaging | jar |
| Java | 17 |
| Dependencies | Spring Boot DevTools(개발 도구)<br>Lombok(개발 도구)<br>Spring Data JDBC(SQL)<br>PostgreSQL Driver(SQL) |

<img src="https://user-images.githubusercontent.com/77138259/228399745-95aa389a-fdfa-4a24-ba90-8d2e3ba26fca.png" alt="spring initializr 화면 구성" />

- IntelliJ IDEA에서 열기로 폴더를 선택함

### 2) application.properties 설정
- application.properties는 스프링 부트 프로젝트의 환경 설정용 파일임
- 여기서는 postgres 데이터베이스 접속에 관련된 설정을 함
- IntelliJ IDEA에서 src/main/resources => application.properties를 선택해 오픈함
- application.properties 파일에 다음 설정을 입력함
```
spring.datasource.driver-class-name=org.postgresql.Driver
spring.datasource.url=jdbc:postgresql://localhost:5432/postgres
spring.datasource.username=postgres
spring.datasource.password=0000 // PostgreSQL을 설치할 때 설정한 비밀번호 입력
```

- application.properties 설정 항목

| **항목** | **설명** |
| :--- | :--- |
| spring.datasource.driver-class-name | JDBC 드라이버의 클래스명을 지정함. 여기서는 PostgreSQL의 드라이버를 설정함 |
| spring.datasource.url | 데이버베이스의 '접속 URL'을 설정. 여기서는 PostgreSQL의 default Port인 5432와 기본 데이터베이스인 'postgres'를 사용 |
| spring.datasource.username | 데이터베이스에 접속하는 '유저명'을 설정 |
| spring.datasource.password | 데이터베이스에 접속하는 '패스워드'를 설정 |

### 3) 엔티티 생성
- member 테이블에 대응하는 Member 엔티티를 생성
>[엔티티와 리포지토리란](../Database/Entity와_Repository.md)
>[PostgreSQL 초기 설정 참고하기](../Database/PostgreSQL.md)

- 'src/main/java'에서 마우스 오른쪽 버튼을 클릭해 '새로 만들기 => 패키지'를 선택하여 com.example.demo.entity 패키지를 생성
- com.example.demo.entity 폴더에서 '새로 만들기 => Java 클래스' 선택 및 클래스명을 Member로 지정
- 클래스 내용을 다음과 같이 입력
```java
// Member 클래스

package com.example.demo.entity;  
  
import lombok.AllArgsConstructor;  
import lombok.Data;  
import lombok.NoArgsConstructor;  
  
// Member 테이블 엔티티  
@Data  
@NoArgsConstructor  
@AllArgsConstructor  
public class Member {  
    // Member 번호  
    private Integer id;  
    // Member 이름  
    private String name;  
}
```

- Lombok에서 제공하는 어노테이션을 사용

| **항목** | **설명** |
| :--- | :--- |
| @Data | 클래스에 부여하는 것으로, 전 필드에 대해 getter/setter로 액세스할 수 있음. hashCode(), equals(), toString()도 자동 생성됨 |
| @NoArgsConstructor | 클래스에 부여하는 것으로, 기본 생성자가 자동 생성됨 |
| @AllArgsConstructor | 클래스에 부여하는 것으로, 전 필드에 대해 초기화 값을 인수로 가지는 생성자가 자동 생성됨 |

- 엔티티 생성 후, 테이블의 기본키(Primary Key)에 해당하는 id 필드에 대해 @Id 어노테이션을 부여
- 부여 시 'org.springframework.data.annotation.Id'가 임포트 됨
```java
// Member 클래스

...

public class Member {  
	// Member 번호  
	@Id  // 여기에 @Id 어노테이션 부여
	private Integer id;
	
	...
}
```

### 4) 리포지토리 생성
- member 테이블에 대해서 데이터를 조작하는 리포지토리를 생성해보자
- 'src/main/java => com.example.demo' 폴더에서 마우스 오른쪽 버튼을 클릭해 '새로 만들기 => 패키지'를 선택하여 com.example.demo.repository 패키지를 생성
- com.example.demo.repository 폴더에서 '새로 만들기 => Java 클래스' 선택 및 클래스명을 MemberCrudRepository라는 이름으로 인터페이스를 생성
- MemberCrudRepository 인터페이스에서 CrudRepository를 상속해서 사용하기 위해 클래스명 뒤에 extends를 추가함
- 인터페이스 내용에 다음와 같이 작성
```java
// MemberCrudRepository 인터페이스

package com.example.demo.repository;  
  
import com.example.demo.entity.Member;  
import org.springframework.data.repository.CrudRepository;  
  
// Member 테이블: 리포지토리  
public interface MemberCrudRepository extends CrudRepository<Member, Integer> {  
  
}
```

- MemberCrudRepository 리포지토리는 CrudRepository를 상속하도록 작성함
- CrudRepository 인수의 타입인 Member와 @Id 어노테이션을 부여한 필드의 타입인 Integer의 순서로 지정함
>저장 대상 객체의 타입과 저장 대상 객체의 기본키 타입을 지정한 것임
>이것으로 MemberCrudRepository 인터페이스 안에서 메서드를 작성하지 않아도 사용 가능하게 됨
>메서드에서 사용되는 반환 값이나 인수의 엔티티나 ID는 <Member, Integer>로 지정한 타입이 됨

- CurdRepository의 CRUD 메서드

| **반환형** | **메서드** | **개요** |
| :--- | :--- | :--- |
| long | count() | 취득한 엔티티의 수를 돌려줌 |
| void | delete(Member entity) | 지정한 엔티티를 삭제 |
| void | deleteAll() | 리포지토리에서 관리하는 모든 엔티티를 삭제 |
| void | deleteAll(Iterable entities) | 지정한 복수의 엔티티를 삭제 |
| void | deleteById(Integer id) | 지정한 ID에 해당하는 엔티티를 삭제 |
| boolean | existsById(Integer id) | 지정한 ID에 해당하는 엔티티의 존재 여부를 확인 |
| Iterable | findAll() | 모든 엔티티를 반환 |
| iterable | findAllById(Iterable ids) | 지정한 복수의 ID에 해당하는 엔티티를 반환 |
| Optional | findById(Integer id) | 지정한 ID에 해당하는 엔티티를 반환 |
| Member | save(Member entity) | 지정한 엔티티를 저장 |
| Iterable | saveAll(Iterable entities) | 지정한 모든 엔티티를 저장 |

- 중요한 점은 save 메서드는 @Id 어노테이션이 부여된 필드의 값이 null인 경우, INSERT가 실행되고, 그 외에는 UPDATE가 실행된다는 점임

### 5) 클래스 생성
- 실제 작업을 처리하는 클래스로 SpringDataJdbcSampleApplication 클래스를 생성함
- 스프링부트 애플리케이션의 기동 클래스인 SpringDataJdbcSampleApplication에서는 MemberCrudRepository를 @Autowired로 주입하고 '등록' 메서드와 '전체 취득' 메서드를 만듦
- 메서드 내에서는 MemberCrudRepository에 처리를 위임함
- execute 메서드로 등록 메서드와 전체 취득 메서드를 호출하도록 작성하고 main 메서드에서 execute 메서드를 호출함

```java
// SpringDataJdbcSampleApplication 클래스
package com.example.demo;  // com.example.SpringDataJDBCSample 패키지가 아님 주의
  
import com.example.demo.entity.Member;  
import com.example.demo.repository.MemberCrudRepository;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.boot.SpringApplication;  
import org.springframework.boot.autoconfigure.SpringBootApplication;  
  
// 스프링 부트 기동 클래스  
@SpringBootApplication  
public class SpringDataJdbcSampleApplication {  
  
    public static void main(String[] args) {  
        SpringApplication.run(SpringDataJdbcSampleApplication.class, args)  
                .getBean(SpringDataJdbcSampleApplication.class).execute();  
    }  
  
    @Autowired  
    MemberCrudRepository repository;  
  
    // 등록과 전체 취득 처리  
    private void execute() {  
        // 등록  
        executeInsert();  
        // 전체 취득  
        executeSelect();  
    }  
  
    // 등록  
    private void executeInsert() {  
        // 엔티티 생성(id는 자동 부여되기 때문에 null 설정  
        Member member = new Member(null, "이순신");  
        // 리포지토리를 이용해 등록을 수행하고 결과를 취득  
        member = repository.save(member);  
        // 결과를 표시  
        System.out.println("등록 데이터: " + member);  
    }  
  
    // 전체 취득  
    private void executeSelect() {  
        System.out.println("--- 전체 데이터를 취득합니다 ---");  
        // 리포지토리를 이용해 전체 데이터를 취득  
        Iterable<Member> members = repository.findAll();  
        for (Member member: members) {  
            System.out.println(member);  
        }  
    }  
}
```
- 위 코드에서 'com.example.demo.repository.MemberCrudRepository'를 참조할 수 없다는 에러가 계속 떠서 찾아보니 실행 단위가 하나의 패키지로 묶여 있어야 실행이 가능하다는 것을 알게 되었다.
- 심지어 책의 예제코드(github 버전)도 그렇게 작성되어 있었다.(출판이 잘못되었나보다.)

>책을 맹신하지 말자. 스스로 찾아보는 습관을 기르자.

<img src="https://user-images.githubusercontent.com/77138259/228427135-5a1c5934-07fd-4bb4-a256-e1186de2b357.png" alt="실행 결과 값" />
- 실행을 해보면 CrudRepository를 상속한 MemberCrudRepository 클래스의 등록과 전체 취득 메서드가 호출된 것을 알 수 있음

>등록 데이터에 Member id가 7인 이유는, 오류가 났을 때 이것저것 등록 테스트를 했기 때문이다.
>
>id는 primary key로 지정하여 데이터 생성과 함께 자동으로 매겨지는 것이며, 삭제 후 재등록 시 한 번 사용했던 id에 재등록할 수 없다.

<img src="https://user-images.githubusercontent.com/77138259/228427880-544f4d60-09c1-4432-aa48-871690218d62.png" alt="postgreSQL view data" />
- pgAdmin 4에서 확인해보면 같은 결과가 표시되는 것을 확인할 수 있음

- 스프링 데이터가 제공하는 CrudRepository를 상속한 MemberCrudRepository 인터페이스를 사용하여 INSERT와 SELECT를 실행하는 프로그램을 만들었다.
- CrudRepository를 상속한 것만으로 CRUD를 실행할 수 있는 메서드를 사용할 수 있는 것은 개발을 상당히 편리하게 해 줌
- 한 가지 주의할 점으로는 CrudRepository를 상속한 MemberCrudRepository 인터페이스에 @Repository를 부여하지 않는다는 것이다.
- @Repository는 인스턴스 생성을 위한 어노테이션이며, 인터페이스는 인스턴스화할 수 없다.
- CrudRepository를 상속하는 것은 인터페이스이며, 해당 인터페이스의 구현 클래스는 스프링 데이터 JDBC가 자동 생성하므로 직접 구현 클래스를 생성해 @Repository를 구현 클래스에 부여할 필요가 없다.