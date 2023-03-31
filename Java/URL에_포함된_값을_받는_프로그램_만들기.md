- 링크를 클릭했을 때 등 URL의 일부로 사용되는 값을 취득하는 방법과 하나의 뷰에 여러 개의 버튼이 있을 때 처리 내용을 구별하는 방법을 알아보자

### 1) 프로젝트 생성
- [Spring Initializr](./Spring_initializr_사용방법.md)에서 다음 설정 내용을 참조해서 다운로드 후 압축을 해제함

| **항목** | **값** |
| :--- | :--- |
| Project | Gradle - Groovy |
| Language | Java |
| Spring Boot | 3.0.5 |
| Artifact | PathVariableSample |
| Packaging | jar |
| Package name | com.example.demo |
| Java | 17 |
| Dependencies | Spring Boot DevTools(개발 툴)<br>Thymeleaf(템플릿 엔진)<br>Spring Web(웹) |

<img src="https://user-images.githubusercontent.com/77138259/228768733-41d46f83-34dd-422d-bf8d-a6a85fe5e9fd.png" alt="spring initializr 화면 구성" />

- IntelliJ IDEA에서 열기로 폴더를 선택함

### 2) 컨트롤러 생성
- '`src/main/java => com.example.demo`' 폴더에서 마우스 오른쪽 버튼을 클릭해 '새로 만들기 => 패키지'를 선택하여 `com.example.demo.controller` 패키지를 생성
- 다시 `controller` 폴더를 선택하고 마우스 오른쪽 버튼을 클릭해 '`새로 만들기 => Java 클래스`'를 선택한 후 `PathVariableController` 클래스를 생성
- 아래 내용을 작성
```java
// PathVariableController 클래스

package com.example.demo.controller;  
  
import org.springframework.stereotype.Controller;  
import org.springframework.web.bind.annotation.GetMapping;  
  
@Controller  
public class PathVariableController {  
    // 화면 표시  
    @GetMapping("show")  
    public String showView() {  
        // 반환 값으로 뷰 이름을 돌려줌  
        return "show";  
    }  
}
```
- `PathVariableController` 클래스는 요청 핸들러 메서드에 `@GetMapping("show")` 어노테이션을 부여함
- 클라이언트에서 GET 메서드로 URL(http://localhost:8080/show)에 접속하면 `PathVariableController` 클래스의 `showView` 메서드가 호출되고 반환값으로 뷰 이름인 '`show`'를 돌려줌

### 3) 뷰 생성(입력 화면)
- `showView` 메서드인 반환값인 '`뷰 이름: show`'에 대응하는 `show.html`을 생성해서 `resources/templates` 폴더 아래에 두겠음
- `src/main/resources/templates` 폴더에서 마우스 오른쪽 버튼을 클릭해 '`새로 만들기 => HTML 파일`'를 선택하여 '`show.html`' 파일 생성
- 아래와 같이 입력
```html
<!-- show.html -->

<!DOCTYPE html>  
<html lang="en" xmlns:th="http://www.thymeleaf.org">  
<head>  
    <meta charset="UTF-8">  
    <title>URL 경로에 포함된 값과 클릭됟ㄴ 버튼 식별</title>  
</head>  
<body>  
    <!--URL에 값 넣기-->  
    <h3><a th:href="@{/function/1}">기능-1</a></h3>  
    <h3><a th:href="@{/function/2}">기능-2</a></h3>  
    <h3><a th:href="@{/function/3}">기능-3</a></h3>  
  
    <!--같은 form 태그 안에 포함된 여러 개의 버튼-->  
    <form th:action="@{/send}" method="post">  
        <input type="submit" value="버튼A" name="a" />  
        <input type="submit" value="버튼B" name="b" />  
        <input type="submit" value="버튼C" name="c" />  
    </form></body>  
</html>
```

- `th:href="@{/function/1}"`은 `URL(/function/1)`의 링크를 생성함
- URL의 제일 끝에 적은 숫자가 'URL에 포함된 값'이 됨
- 위에서는 하나의 `<form>` 태그 안에 3개의 버튼을 만들었음
- 어느 버튼을 클릭하더라도 `URL(/send)`로 POST 송신됨
	- 어느 버튼을 클랬는지 확인할 때 중요한 것은 `name` 속성임
	- 이 `name` 속성을 이용해 컨트롤러의 요청 핸들러 메서드에서 처리를 나눔

### 4) 컨트롤러에 추가(링크 처리)
- 컨트롤러인 `PathVariableController`에 요청 핸들러 메서드에 처리 내용을 추가함
```java
// PathVariableController 클래스

...
  
@Controller  
public class PathVariableController {  
    ...
    
    // 링크 처리  
	@GetMapping("/function/{no}")  
	public String selectFunction(@PathVariable Integer no) {  
	    // 뷰 이름을 초기화  
	    String view = null;  
	    switch (no) {  
	        case 1:  
	            view = "pathvariable/function1";  
	            break;        case 2:  
	            view = "pathvariable/function2";  
	            break;        case 3:  
	            view = "pathvariable/function3";  
	            break;    }  
	  
	    // 반환값으로 뷰 이름을 돌려줌  
	    return view;  
	}
}
```
- `@GetMapping("/function/{no}")`가 GET으로 온 `URL(/function/{no})`에 대응함
- '`{no}`'는 자리 표시자(placeholder)로 URL에 포함된 값을 저장함
- `@PathVariable`에 자리 표시자와 같은 변수명으로 지정하면 자리 표시자에 저장된 값이 `@PathVariable`의 변수로 저장됨
- `no` 변수 값을 토대로 `switch`문을 이용해 뷰 이름을 확정함

### 5) 뷰 생성(기능 화면)
- `selectFunction` 메서드의 반환값(뷰 이름)에 대응하는 파일을 만들어서 `resources/templates` 폴더 아래에 두겠음
- '`src/main/resources/templates`' 폴더에서 마우스 오른쪽 버튼을 클릭해 '`새로 만들기 => 경로`'를 선택하여 '`pathvariable`' 폴더 생성
- '`pathvariable`' 폴더에서 마우스 오른쪽 버튼을 클릭해 '`새로 만들기 => HTML 파일`'를 선택하여 '`function1.html`' 파일 생성
- 아래 내용 입력
```html
<!-- function1.html -->

<!DOCTYPE html>  
<html lang="en">  
<head>  
    <meta charset="UTF-8">  
    <title>기능 1</title>  
</head>  
<body>  
  <h1>기능 1의 화면</h1>  
</body>  
</html>
```
- 같은 방법으로 `function2.html`과 `function3.html`를 생성함(숫자도 변경!)

### 6) 확인(링크)
- `PathVariableSampleApplication` 자바 파일에서 마우스 오른쪽 버튼을 클릭해서 ==실행==을 선택
- 'http://localhost:8080/show' 에서 각 링크를 클릭하여 대응되는 뷰가 표시되는 것을 확인
<div style="display:flex; width:100%; box-sizing:border-box">
<img style="width:50%" src="https://user-images.githubusercontent.com/77138259/228776705-0eff5829-e8c6-42b8-8b25-5bd0a75f9f05.png" alt="웹 브라우저 링크 클릭 전" />
<img style="width:50%" src="https://user-images.githubusercontent.com/77138259/228776671-6fc0e6aa-9f00-498a-a576-29b50c677c0d.png" />
</div>

### 7) 컨트롤러에 추가(버튼 판별 처리)
- 컨트롤러인 `PathVariableController`에 요청 핸들러 메서드를 추가함
```java
// PathVariableController 클래스

...
  
@Controller  
public class PathVariableController {  
    ...
    
	// 버튼A 클릭 처리  
	@PostMapping(value = "send", params = "a")  
	public String showAView() {  
	    // 반환값으로 뷰 이름을 돌려줌  
	    return "submit/a";  
	}  
	// 버튼B 클릭 처리  
	@PostMapping(value = "send", params = "b")  
	public String showBView() {  
	    // 반환값으로 뷰 이름을 돌려줌  
	    return "submit/b";  
	}  
	// 버튼C 클릭 처리  
	@PostMapping(value = "send", params = "c")  
	public String showCView() {  
	    // 반환값으로 뷰 이름을 돌려줌  
	    return "submit/c";  
	}
}
```
- 요청 매핑 어노테이션의 `params` 속성에 뷰의 버튼에 대응하는 `name` 속성을 설정하면 같은 `URL(/send)`에 POST로 전달된 요청에서 어느 버튼이 클릭되었는지 판별함
- 다른 속성도 설정하기 위해 `value` 속성을 명시적으로 작성함

### 8) 뷰 생성(버튼 클릭 화면 확인)
- 메서드의 반환값(뷰 이름)에 대응하는 파일을 만들어서 `resources/templates` 폴더 아래에 두겠음
- '`src/main/resources/templates`' 폴더에서 마우스 오른쪽 버튼을 클릭해 '`새로 만들기 => 경로`'를 선택하여 '`submit`' 폴더 생성
- '`submit`' 폴더에서 마우스 오른쪽 버튼을 클릭해 '`새로 만들기 => HTML 파일`'를 선택하여 '`a.html`' 파일 생성
- 아래 내용 입력
```html
<!-- a.html -->

<!DOCTYPE html>  
<html lang="en">  
<head>  
    <meta charset="UTF-8">  
    <title>a</title>  
</head>  
<body>  
  <h1>버튼A 클릭 화면</h1>  
</body>  
</html>
```
- 같은 방법으로 `b.html`과 `c.html`를 생성함(h1 태그의 알파벳 변경!)

### 9) 확인(버튼 판별)
- `PathVariableSampleApplication` 자바 파일에서 마우스 오른쪽 버튼을 클릭해서 ==실행==을 선택
- 'http://localhost:8080/show' 에서 각 버튼을 클릭하여 대응되는 뷰가 표시되는 것을 확인
<img src="https://user-images.githubusercontent.com/77138259/228779879-080ae7af-0368-43ac-9f8d-28461ede9d7f.png" alt="웹 브라우저 결과 화면" />
- 각 버튼을 클릭하면 `/send`에서 값만 달라지는 것을 확인할 수 있음