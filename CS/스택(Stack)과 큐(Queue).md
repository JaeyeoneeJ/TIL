### 스택(Stack)
- 스택(Stack)은 "쌓다"라는 의미로, <strong>데이터를 차곡차곡 쌓아 올린 형태의 자료구조</strong>로 <strong>가장 마지막에 삽입된 자료가 가장 먼저 삭제</strong>되는 구조를 가지고 있다.
- 스택은 <strong>같은 구조와 크기의 자료를 정해진 방향으로만 쌓을 수 있으며, top으로 정한 곳을 통해서만 접근</strong>할 수 있다.
- 스택에서 top을 통해 삽입하는 연산을 "push", 삭제하는 연산을 "pop"이라고 한다.
- 이러한 스택의 구조를 <strong>후입선출(LIFO, Last-In-First-Out)구조</strong>라고 한다.
- 예시)
	- 웹 브라우저 방문 기록(뒤로 가기)
	- 실행 취소(undo)
	- 역순 문자열 만들기
	- 후위 표기법 계산

<img style="background:white" src="https://user-images.githubusercontent.com/59376200/127250787-bc69ec8e-573e-4f9c-91ee-39409598da00.png" alt="stack" />

### 큐(Queue)
- 큐(Queue)는 스택(Stack)과 다르게 먼저 들어온 것이 먼저 나가는 "선입선출"로, FIFO(First In First Out)의 구조를 가지고 있다.
- 일반적으로 카페에서 먼저 와서 주문한 손님이 음료를 먼저 받고 나가는 개념이라고 보면 편하다.
- 삭제 연산(디큐(Dequeue))이 수행되는 곳을 프론트(front), 삽입 연산(인큐(EnQueue))이 이루어지는 곳은 리어(rear)로, FIFO 구조를 위해 스택과 다르게 큐의 한쪽 끝에는 삽입 작업이, 다른 한쪽에서는 삭제 작업이 나뉘어서 이루어지고 있다.
- 예시)
	- 은행 업무
	- 콜 센터 고객 대기시간
	- 프로세스 관리
	- 캐시(Cache) 구현

<img style="background:white" src="https://user-images.githubusercontent.com/59376200/127253000-528edd13-59d3-4cd5-a7c9-8529cc9dae34.png" alt="stack" />

<hr>
## ref.
https://brightwon.tistory.com/8
https://devuna.tistory.com/22