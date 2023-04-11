- [CORS(Cross-Origin Resource Sharing)](../CS/CORS.md)란 다른 출처의 자원을 공유할 수 있도록 설정하는 권한 체계를 말함
- 따라서 CORS를 설정해주지 않거나 제대로 설정하지 않은 경우, 원하는대로 리소스를 공유하지 못함
- 이를 Spring Boot에서 해결하는 방법에 대해 알아보자

## @Configuration으로 해결하기
- 이 방법은 Global하게 적용하는 방법임
- 우선 config 패키지를 만들어 줌(경로: `/src/main/java/{프로젝트 명}/config`)
- 만들어진 config 패키지 안에 WebConfig 클래스를 만들어 주고 아래와 같이 입력
```java
// WebConfig 클래스

package com.example.testweb.config;  
  
import org.springframework.context.annotation.Configuration;  
import org.springframework.web.servlet.config.annotation.CorsRegistry;  
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;  
  
@Configuration  
public class WebConfig implements WebMvcConfigurer {  
    @Override  
    public void addCorsMappings(CorsRegistry registry) {  
        registry.addMapping("/**")  
                .allowedOrigins("*")  
                .allowedMethods("GET", "POST", "PUT", "DELETE")  
                .maxAge(3000);  
    }  
}
```

1. addMapping
- 클래스 상단에 @Configuration 어노테이션을 통해 설정파일이라는 것을 알려줌
- 그리고 WebMvcConfigurer를 implements함
- addCorsMappings 메소드를 오버라이드해서 설정을 바꿈
- registry.addMapping을 이용해서 CORS에 적용할 URL 패턴을 정의할 수 있음
	- 위처럼 "`/**`" 와일드 카드를 사용할 수도 있음
	- 또한 Ant-style도 지원하며 "`/somePath/**`" 이렇게 적용할 수도 있음
	- Default 값은 아래와 같음
		- Allow all origins
		- Allow "simple" methods GET, HEAD and POST
		- Allow all headers
		- Set max age to 1800 seconds(30minutes)

2. allowedOrigins
- allowedOrigins 메소드를 이용해서 자원 공유를 허락할 Origin을 지정할 수 있음
- 위 처럼 "`*`"로 모든 Origin을 허락할 수 있음
```java
@Configuration  
public class WebConfig implements WebMvcConfigurer {  
    @Override  
    public void addCorsMappings(CorsRegistry registry) {  
        registry.addMapping("/**")  
                .allowedOrigins("http://localhost:8080", "http://localhost:8081");  
    }  
}
```
- 위처럼 한 번에 여러 Origin을 설정할 수도 있음

3. allowedMethods
- allowedMethods를 이용해서 허용할 HTTP method를 지정할 수 있음
- 위 처럼 여러개를 지정할 수 있고 마찬가지로  "`*`"를 이용하여 모든 method를 허용할 수 있음

4. maxAge
- maxAge 메소드를 이용해서 원하는 시간만큼 pre-flight 리퀘스트를 캐싱해둘 수 있음
- 안의 숫자는 초(second)라고 생각하면 됨


## Annotation 이용하기
- 두번 째 방법은 Controller 또는 method 단에서 annotation을 통해 적용하는 방법임
```java
// SomeController 클래스

@RequestMapping("/somePath")
@CrossOrigin(origins = "*", allowedHeaders = "*")
public class SomeController {

}
```
- 위처럼 CrossOrigin이라는 어노테이션을 사용하면 global하게 설정하던 것과 같이 허용할 origins이나 methods를 지정할 수 있음
- @Configuration에서 적용한 것과 같이 origins, methods, maxAge, allowedHeaders를 사용하면 됨

```java
// SomeController 클래스

@RestController
@RequestMapping("/somePath")
public class SomeController {
	@CrossOrigin(origins="*")
	@RequestMapping(value = "/{something}",method = RequestMethod.DELETE)
	public ResponseEntity<String> delete(@PathVariable Long reservationNo) throws Exception{
	}
}
```
- 위와 같이 단일 메소드에만 국한하여 적용할 수도 있음