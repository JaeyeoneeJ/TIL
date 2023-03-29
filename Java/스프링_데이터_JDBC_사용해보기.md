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
