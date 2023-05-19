## @Scheduled 어노테이션이란
- Spring Boot의 @Scheduled 어노테이션은 지정된 시간에 주기적으로 메서드를 실행하도록 스케줄링 하는데에 사용됨
- 이 어노테이션을 사용하여 스케줄링 작업을 간편하게 설정할 수 있음
- @Scheduled 어노테이션은 다음과 같은 세 가지 속성을 가짐
	- fixedDelay: 이전 실행이 완료된 후, 일정한 시간이 경과한 후 메셔드를 실행함
	- fixedRate: 메서드를 일정한 시간 간격으로 실행함
	- cron: cron 표현식을 사용하여 메서드를 실행함

## 예제
- 아래는 fixedDelay와 fixedRate 속성을 사용하여 메서드를 스케줄링하는 예제임
```java
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

@Component
public class MyScheduler {
    @Scheduled(fixedDelay = 5000)
    public void myTask() {
        // 5초마다 실행됨
    }

    @Scheduled(fixedRate = 10000)
    public void anotherTask() {
        // 10초마다 실행됨
    }
}
```

- 아래는 cron 속성을 사용하여 메서드를 스케줄링하는 예제임
```java
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

@Component
public class MyScheduler {
    @Scheduled(cron = "0 0/5 * * * *")
    public void myTask() {
        // 매 5분마다 실행됩니다.
    }
}
```
- 위 에제에서는 cron 표현식을 사용하여 매 5분마다 메서드가 실행됨
- cron 표현식은 "초 분 시간 일 월 요일"의 순서로 구성됨
- 각 필드는 와일드카드(`*`), 값의 범위(1~5), 특정 값(3), 범위와 특정 값(1-5, 7), 특정 간격(`*/5`) 등으로 지정할 수 있음

- Spring Boot의 @Scheduled 어노테이션을 사용하면 주기적인 작업을 쉽게 스케줄링할 수 있으며 다양한 옵션을 사용하여 다양한 스케줄링 작업을 설정할 수 있음

## 두 예제의 차이점
- fixedDelay와 fixedRate 속성을 사용하는 방법과 cron 속성을 사용하는 방법은 각각 장단점이 있음

### 1) fixedDelay와 fixedRate 속성
- fixedDelay와 fixedRate 속성은 실행 주기를 지정하는 데에 간단하고 직관적인 방법임
- 이 속성을 사용하면 고정된 간격으로 작업을 수행할 수 있음. 예를 들어, fixedDelay를 1분으로 설정하면 이전 실행이 끝나고 1분 후에 다음 실행이 시작됨. fixedRate를 1분으로 설정하면 작업이 1분마다 실행됨

- 하지만 fixedDelay와 fixedRate는 실행 시간에 따라서 실행 간격이 달라질 수 있음
- 예를 들어, 만약 이전 작업이 매우 오래 걸리면 다음 작업이 실행되기까지 기다리는 시간이 증가함
- 이런 이유로 fixedDelay와 fixedRate는 간격이 정확하고 일정하게 유지되지 않을 수 있음

- 따라서 fixedDelay와 fixedRate는 간단하고 직관적인 실행 간격을 지정하는 데에 유용함
- 나의 경우, 10분 마다 로그를 쌓아서 별도의 파일로 저장하는 용도로 사용을 하고 있음

### 2) cron 속성
- 반면에, cron 속성은 실행 간격을 정확하게 제어할 수 있음. cron 표현식을 사용하여 원하는 실행 간격을 정확하게 지정할 수 있음
- 또한 cron 표현식은 복잡한 실행 스케줄을 설정할 수 있음. 예를 들어, 특정 날짜와 시간에 실행되는 작업, 매일 정해진 시간에 실행되는 작업 등을 설정할 수 있음
- 하지만 cron 속성은 복잡하고 이해하기 어려울 수 있음. 또한 cron 표현식이 변경되었을 때, 애플리케이션을 재시작해야만 함

- cron 속성은 정확하고 복잡한 실행 스케줄을 지정하는데 유용함
- 나의 경우, 1주일마다 s3 버킷과 SQL DB를 참조해서, s3 버킷을 서칭하고 DB에 없는 파일이 버킷에 있으면 DB를 업데이트하는 등의 작업을 진행하고 있음

- 즉, 어떤 방법을 선택할지는 실행하는 작업의 성격과 실행 간격의 정확도가 중요한 요소임