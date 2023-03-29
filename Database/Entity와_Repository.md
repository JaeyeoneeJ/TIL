## Entity란
- 엔티티를 한마디로 표현하면 '데이터를 담아두는 객체'임
- 엔티티는 데이터베이스 테이블의 한 행(레코드)에 대응하는 객체임

```java
// Member 엔티티 예

public class Member {
	// id 칼럼 대응
	private Integer id;
	
	// name 칼럼 대응
	private String name;
	
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
}
```

- 엔티티는 실제 데이터를 담아 두는 개체로 사용할 때, 다음 세 가지를 기억해두면 좋음
	- 클래스명: 대응하는 데이터베이스의 테이블 명으로 하는 경우가 많음
	- 데이터베이스에 값 넘겨주기: 데이터베이스 값을 등록/갱신하는 경우, 엔티티에 값을 넣어서 넘겨줌
	- 데이터베이스에서 값 가져오기: 데이터베이스에서 값을 가져오는 경우에는 값을 엔티티에 넣어서 가져옴

## Repository란
- 리포지토리를 간단히 말하면 데이터베이스를 조작하는 클래스임
- 리포지토리를 생성하는 경우에는 반드시 인터페이스를 정의하고 구현해야 함
- 리포지토리 인터페이스의 필드에 리포지토리 구현 클래스를 [DI](../Java/DI_Container.md)하여 특정 구현에 의존하는 것을 피할 수 있기 때문임
- 자바에서는 인터페이스를 구현한 클래스의 접미사에 'Impl(implements의 약자)'를 붙이는 경우가 많음