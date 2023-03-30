>[MVC 모델에 대해 알아보기](./MVC_모델.md)

## 스프링 MVC란
- 스프링 MVC를 간단히 설명하면 '웹 애플리케이션을 간단하게 만들 수 있는 기능을 제공하는 프레임워크'임
- '프런트 컨트롤러 패턴(Front Controller Pattern)'이란 디자인 패턴(Design Pattern)이라고 하는 '이런 경우에는 이런 식으로 작성하면 좋다'라는 선인들의 지혜가 담긴 설계 패턴의 하나로, 모든 요청을 프런트 컨트롤러가 받아, 그 후 담당하는 컨트롤러에 적절하게 할당하는 설계임
- 스프링 MVC의 주요 기능으로 화면 전환이나 사용자가 사용하는 브라우저와 서버 간 입출력 데이터 전달을 단순화하는 등의 기능이 있음

- 스프링 MVC의 주요 구성 요소

| **객체** | **기능 개요** |
| :--- | :--- |
| DispatcherServlet | 모든 요청을 수신하는 프런트 컨트롤러 |
| Model | 컨트롤러에서 뷰에 넘겨주는 표시용 데이터 등을 저장하는 객체<br>(HttpServletRequest나 HttpSession과 같은 기능을 제공) |
| 컨트롤러 | 요청에 대응해서 처리할 내용이 있는 곳 |
| 서비스 처리 | 데이터베이스에 접속해서 데이터를 취득하거나 데이터를 가공하는 등 여러 가지 작업을 실행. 개발자가 설계하고 구현함(스프링 MVC와 관계 없음) |
| 뷰 | 화면 표시 처리를 함(구체적으로는 JSP 등의 처리) |


## 요청을 받고 응답을 보낼 때까지의 흐름

- 요청을 받고 응답을 보낼 때까지의 흐름
<img src="https://user-images.githubusercontent.com/77138259/228435499-dde664f1-9b49-45c2-a0fe-6b1cb8795b63.png" alt="스프링 MVC에서 요청에서 응답까지의 흐름" />
<p style="margin:0" align="right"><a href="https://wikibook.co.kr/spring/">출처: 스프링 프레임워크 첫걸음(위키북스)</a></p>

1. 모든 요청을 수신하는 프런트 컨트롤러인 DispatcherServlet이 클라이언트로부터 요청을 수신함
2. DispatcherServlet이 컨트롤러의 요청 핸들러 메서드를 호출함
3. 컨트롤러는 비즈니스 로직 처리를 호출하고, 처리 결과를 받음
4. 처리 결과를 모델로 설정하고 뷰 이름을 반환함
5. 반환된 뷰 이름을 받아 DispatcherServlet이 뷰 이름에 대응하는 뷰에 대해 화면 표시 처리를 의뢰함
6. 마지막으로 클라이언트가 응답을 받고 브라우저에 화면이 표시됨

- 그림을 보면 번거로운 처리는 스프링 MVC가 담당하기 때문에 실제로 우리가 작성하는 부분은 컨트롤러, 비즈니스 로직 처리, 뷰의 세가지 뿐인 것을 알 수 있음


## 스프링 MVC 사용해보기

### 1) 프로젝트 생성
- [Spring Initializr](./Spring_initializr_사용방법.md)에서 다음 설정 내용을 참조해서 다운로드 후 압축을 해제함

| **항목** | **값** |
| :--- | :--- |
| Project | Gradle - Groovy |
| Language | Java |
| Spring Boot | 3.0.5 |
| Artifact | SpringMVCViewSample |
| Packaging | jar |
| Java | 17 |
| Package name | com.example.demo |
| Dependencies | Spring Boot DevTools(개발 도구)<br>Thymeleaf(템플릿 엔진)<br>Spring Web(웹) |

- IntelliJ IDEA에서 열기로 폴더를 선택함

>**타임리프란?**
>- 타임리프(Thymeleaf)는 데이터와 미리 정의한 템플릿을  바인딩(묶어주는)해서 뷰에 표시할 때 도움을 주는 '템플릿 엔진(Template Engine)'의 한 종류로, 스프링 부트에서 사용하기를 추천하고 있음

### 2) 컨트롤러 생성
- 'src/main/java => com.example.demo' 폴더에서 마우스 오른쪽 버튼을 클릭해 '새로 만들기 => 패키지'를 선택하여 com.example.demo.controller 패키지를 생성
- 다시 controller 폴더를 선택하고 마우스 오른쪽 버튼을 클릭해 '새로 만들기 => Java 클래스'를 선택한 후 HelloViewController 클래스를 생성
- 아래 내용을 작성

```java
// HelloViewController 클래스

package com.example.demo.controller;  
  
import org.springframework.stereotype.Controller;  
import org.springframework.web.bind.annotation.GetMapping;  
import org.springframework.web.bind.annotation.RequestMapping;  
  
@Controller // 인스턴스 생성 어노테이션
@RequestMapping("hello")  
public class HelloViewController {  
    @GetMapping("view")  
    public String helloView() {  
        // 반환 값으로 뷰의 이름을 돌려줌  
        return "hello";  
    }  
}
```

컨트롤러는 POJO 클래스로 작성함
>POJO(Plain Old Java Object)는 보통의 자바 객체, 즉 어떤 클래스를 상속하는 등의 특별한 처리를 하지 않는 클래스로 생각하면 됨

1. @Controller
- 인스턴스 생성 어노테이션
- @Controller는 클라이언트와 데이터 입출력을 제어하는 애플리케이션 레이어의 컨트롤러를 부여함
- 요청 핸들러 메서드의 반환 값을 뷰 이름으로 하여 템플릿 엔진의 뷰가 응답 HTML을 생성함

2. @RequestMapping
- 클래스나 메서드에 부여하는 어노테이션
- 컨트롤러의 요청 핸들러 메서드와 URL을 매핑함
- @RequestMapping의 속성

| **속성** | **기능 개요** |
| :--- | :--- |
| value | 매핑할 URL 경로(path)를 지정함<br>value는 처음의 /를 생략할 수 있음<br>URL 경로만 지정하는 경우에는 속성에서 value를 생략할 수 있음<br>URL 경로를 여러 개 지정할 수 있음 |
		| method | GET과 POST 등의 HTTP 메서드를 지정함<br>GET을 지정하는 경우에는 RequestMethod.GET을 사용함<br>POST를 사용하는 경우에는 RequestMethod.POST를 사용함<br>HTTP 메서드를 여러 개 지정할 수 있음<br>클래스에 @RequestMapping을 부여하는 경우에는 지정할 수 없음 |

- value 속성의 예
```java
// value 속성에 처리 대상의 URL 경로를 매핑
@RequestMapping(value = "hello")

// value 속성만 지정하는 경우 생략 가능
@RequestMapping("hello")

// URL 경로를 여러 개 지정 가능
@RequestMapping(value = { "hello", "hellospring" })

// method에서 HTTP 메서드 'GET'을 지정함
@RequestMapping(value = "hello", method = RequestMethod.GET)

// 메서드를 여러 개 지정할 수 있음
@RequestMapping(value = "hello", method = { RequestMethod.GET, RequestMethod.POST })
```

3. @GetMapping
- @GetMapping은 @RequestMapping의 GET 요청용 어노테이션임
- 이 어노테이션을 사용하는 것으로 작성 내용이 줄어들고 가독성이 높아짐
- 속성으로는 @RequestMapping의 value 속성은 같지만 method 속성은 없음

- @GetMapping의 예
```java
// value 속성만이라면 생략 가능함
@GetMapping("hello")

// URL을 여러 개 지정할 수 있음
@GetMapping(value = { "hello", "hellospring" })
```

4. PostMapping
- @RequestMapping의 POST 요청용 어노테이션으로 이 어노테이션을 사용하면 작성 내용이 줄어들고 가독성이 높아짐
- 속성으로는 @RequestMapping의 value 속성은 같지만 method 속성은 없음

- @PostMapping의 예
```java
// value 속성만이라면 생략 가능함
@PostMapping("hello")

// URL을 여러 개 지정할 수 있음
@PostMapping(value = { "hello", "hellospring" })
```

### 3) URL 매핑
- HelloViewController 클래스는 클래스에 @RequestMapping("hello") 어노테이션을 부여하고 요청 핸들러 메서드에 @GetMapping("view") 어노테이션을 부여했음
- 이것으로 클라이언트가 URL(http://localhost:8080/hello/view)로 GET 메서드를 보내면 HelloViewController 클래스의 helloView 메서드가 호출됨

>**스프링 부터에서의 URL 표기**
>- 브라우저의 주소 표시줄에 입력한 'http://localhost:8080/hello/view' 에 대한 설명 
>- localhost는 자기 자신을 의미하는 서버 이름임
>- 8080은 스프링 부트에 포함된 톰캣(Tomcat) 서버의 포트 번호임
>- 포트란 네트워크 상에서 데이터를 통신하기 위한 문 같은 것으로, 포트 번호가 그 문의 번호임
>- 일반적으로 'http://<서버 이름>(:포트 번호)/<컨텍스트 패스>/<매핑 URL>' 형식으로 애플리케이션에 액세스하지만 스프링부트에서는 기본적으로 <컨텍스트 패스>(애플리케이션 이름)가 생략됨

### 4) 뷰 생성
- 스프링 부트의 프로젝트에서는 템플릿 엔진을 사용하는 경우 '뷰'를 두는 곳이 지정되어 다음과 같은 규칙을 지켜야 함
	- resources/templates 폴더 밑에 뷰를 생성
	- 뷰가 많이 필요한 경우에는 기능별로 폴더를 만들어 보관
	- 폴더를 나눈 경우는 templates 폴더 이하의 폴더명을 요청 핸들러 메서드의 반환값에 지정해야 함
	- CSS나 자바스크립트 등은 resources/static 폴더에 배치함

- hello.html을 생성
	- 'src/main/resources/templates' 폴더에서 마우스 오른쪽 버튼을 클릭해 '새로 만들기 => HTML 파일'를 선택하여 'hello.html' 파일 생성
	- 아래와 같이 입력
```html
<!DOCTYPE html>  
<html lang="en">  
<head>  
    <meta charset="UTF-8">  
    <title>View Sample</title>  
</head>  
<body>  
    <!--추가-->  
    <h1>Hello View?!</h1>  
</body>  
</html>
```

- 실행과 확인
	- com.example.demo 폴더에 있는 SpringMvcViewSampleApplication을 선택하고 실행
	- 정상적으로 실행되면 스프링 부트에서 톰캣 서버를 정상적으로 가동했다는 메세지를 확인할 수 있음
	- 브라우저에서 'http://localhost:8080/hello/view' 를 입력하고 뷰가 표시되는 것을 확인
<img src="https://user-images.githubusercontent.com/77138259/228454496-642f3f74-41ac-44be-9c20-a6888efc0779.png" alt="웹 서버 기동 확인" />
