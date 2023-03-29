## Model 인터페이스란
- 처리한 데이터를 뷰에 표시하고 싶을 경우 데이터를 전달하는 역할을 함
- 스프링 MVC에 의해 관리되며, 수동 또는 자동으로 객체를 저장하고 관리하는 기능을 제공함
- Model을 이용하고 싶은 경우 요청 핸들러 메서드의 인수에 Model 타입을 전달함. 그러면 스프링 MVC가 자동으로 Model 타입 인스턴스를 설정함

### 기억해야 할 중요한 메서드
1. addAttribute
- 특정 이름에 대해 값을 설정
- 저장하고 싶은 값에 별명을 붙인다고 생각하면 됨
- 뷰에서는 별명에 사용한 이름을 이용함
```java
Model addAttribute(String name, Object value)
// name: 이름(별명), value: 값(저장하고 싶은 객체)
```

## Model을 사용하는 프로그램 만들기

### 1) 프로젝트 생성
- [Spring Initializr](./Spring_initializr_사용방법.md)에서 다음 설정 내용을 참조해서 다운로드 후 압축을 해제함
| **항목** | **값** |
| :--- | :--- |
| Project | Gradle - Groovy |
| Language | Java |
| Spring Boot | 3.0.5 |
| Artifact | SpringMVCModelSample |
| Packaging | jar |
| Java | 17 |
| Package name | com.example.demo |
| Dependencies | Spring Boot DevTools(개발 도구)<br>Thymeleaf(템플릿 엔진)<br>Spring Web(웹) |

- IntelliJ IDEA에서 열기로 폴더를 선택함

### 2) 컨트롤러 생성
- 'src/main/java => com.example.demo' 폴더에서 마우스 오른쪽 버튼을 클릭해 '새로 만들기 => 패키지'를 선택하여 com.example.demo.controller 패키지를 생성
- 다시 controller 폴더를 선택하고 마우스 오른쪽 버튼을 클릭해 '새로 만들기 => Java 클래스'를 선택한 후 HelloModelController 클래스를 생성
- 아래 내용을 작성
```java
// HelloModelController 클래스

package com.example.demo.controller;  
  
import org.springframework.stereotype.Controller;  
import org.springframework.ui.Model;  
import org.springframework.web.bind.annotation.GetMapping;  
import org.springframework.web.bind.annotation.RequestMapping;  
  
@Controller  
@RequestMapping("hello") // 클래스에 어노테이션 부여
public class HelloModelController {  
    @GetMapping("model") // 요청 핸들러 메서드에 어노테이션 부여
    public String helloView(Model model) {
        // Model 데이터를 저장  
        model.addAttribute("msg", "타임리프!!!");  
        // 반환 값으로 뷰 이름을 돌려줌  
        return "helloThymeleaf";  
    }  
}
```

- 타임리프를 사용하는 경우 컨트롤러에서 뷰에서 표시할 데이터를 준비해야 함. 그 때 사용하는 것이 Model 인터페이스임
- Model 인터페이스를 사용하려면 요청 핸들러 메서드의 인수에 Model 타입을 전달함
- 전달되면 스프링 MVC가 자동으로 Model 타입의 인스턴스를 설정하므로 Model addAttribute 메서드를 사용할 수 있음
- addAttribute 메서드는 인수로 '이름: msg'에 '값: 타임리프!!!'를 저장함
- 클라이언트로부터 URL(http://localhost:8080/hello/model)이 GET 메서드로 송신되면 HelloModelController 클래스의 helloView 메서드가 호출되어 반환값으로 뷰 이름을 돌려주는 것으로 그에 대응되는 뷰가 표시됨

### 3) 뷰 생성
- 'src/main/resources/templates' 폴더에서 마우스 오른쪽 버튼을 클릭해 '새로 만들기 => HTML 파일'를 선택하여 'helloThymeleaf.html' 파일 생성
- 아래와 같이 입력
```html
<!DOCTYPE html>  
<!--타임리프 사용 선언-->  
<html xmlns:th="http:www.thymeleaf.org">  
<head>  
    <meta charset="UTF-8">  
    <title>Hello Thymeleaf</title>  
</head>  
<body>  
	<!--추가-->  
	<h1 th:text="${msg}">표시되는 부분</h1>  
</body>  
</html>
```
- 타임리프의 기능은 'th:xxx속성명' 형식으로 입력함

- th:texst의 설명
| **속성** | **기능 개요** |
| :--- | :--- |
| th:text | 속성 값에 지정된 값을 새니타이즈(Sanitize)하여 출력 |
| th:utext | 속성 값에 지정된 값을 출력(새니타이즈 안 함) |

> **새니타이즈란?**
> - 새니타이즈(Sanitize)란 위험한 코드나 데이터를 변환 또는 제거하여 무력화하는 것임
> - 간단히 말하면 '특별한 의미를 가진 문자의 특별함을 무효화하고 의도하지 않은 움직임을 봉쇄'하는 것임
> - 새니타이즈는 웹사이트의 입력 폼을 통해 악의적인 코드가 입력되었을 때 등에 유용하게 사용됨

### 4) 실행과 확인
- 'src/main/resources/templates' 폴더의 helloThymeleaf.html 을 선택하고 마우스 오른쪽 버튼을 클릭해서 '다음에서 열기 => 브라우저 => Chrome'을 선택함
- h1 태그로 둘러싸인 '표시되는 부분'이라는 문자가 브라우저에 표시됨
- 이번에는 SpringMvcModelSampleApplication 자바 파일에서 마우스 오른쪽 버튼을 클릭해서 실행
- 브라우저에서 'http://localhost:8080/hello/model' 를 입력하고 뷰가 표시되는 것을 확인

<img src="https://user-images.githubusercontent.com/77138259/228467010-247fbdf4-5acc-41e9-aa2a-69fadfbfe785.png" alt="브라우저 확인" />

- 요청 핸들러 메서드에서 뷰로 표시하고 싶은 데이터를 Model의 'addAttribute(이름, 값)' 메서드를 사용해서 저장하고, 타임리프에서 데이터를 표시할 위치를 '${이름}' 형식으로 설정함
- 주의할 점은 뷰에서 사용할 수 있는 것은 'addAttribute(이름, 값)'의 '이름' 부분이라는 것임