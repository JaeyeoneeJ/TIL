인터페이스란 자바에서 클래스에 포함하는 메서드의 구체적인 내용을 작성하지 않고 상수와 메서드 타입만 정의한 것을 말함

## 인터페이스 선언하기
- 인터페이스를 선언할 때는 interface 키워드를 사용함
```java
public interface Greet {
	// interface 선언
	// 인사하기
	void greeting();
}
```
- 인터페이스는 다른 클래스에서 구현하는 것을 전제로 만들어짐
- 때문에 인터페이스를 선언한 메서드는 암묵적으로 public abstract 접근 제어자(access modifier)가 붙은 추상 클래스라고 불림

```java
public interface Greet {
	// 암묵적 접근 제어자, 위의 코드와 같은 의미를 가짐
	// 인사하기
	public abstract void greeting();
}
```
- 또한 인터페이스에 변수를 선언한 경우, 암묵적으로 public static final 한정자가 붙어 상수가 됨

## 인터페이스 구현하기
- 인터페이스를 구현할 때는 implements 키워드를 사용함
- 인터페이스의 추상 메서드는 암묵적으로 public abstract 한정자가 붙기 때문에 구현할 때는 public을 선언해 둘 필요가 있음

```java
public class MorningGreet implements Greet {
	@Override
	public void greeting() {
		System.out.println("좋은 아침입니다.");
	}
}
```

- @Override 어노테이션은 슈퍼 클래스나 인터페이스의 메서드를 상속 혹은 구현하는 클래스에서 재정의하는 것을 말함
- 즉, @Override를 메서드에 부여하는 것으로 "이것은 재정의(Override)된 메서드입니다. 만약 재정의되어 있지 않으면 에러가 발생합니다." 라는 것을 알려줌