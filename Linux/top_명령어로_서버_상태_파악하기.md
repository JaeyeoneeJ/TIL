- `top` 명령어는 리눅스 시스템에서 현재 실행 중인 프로세스의 실시간 정보를 제공하는 유틸리티이다.
- `top` 명령어를 실행하면, 시스템의 CPU 사용량, 메모리 사용량, 프로세스 목록 및 각 프로세스의 세부 정보를 볼 수 있다.

## 1. 기본 사용법
```bash
top
```
- 위 명령어만 입력하면 기본적인 프로세스 목록과 시스템 리소스 사용 현황이 실시간으로 업데이트 된다.

<img src="https://github.com/JaeyeoneeJ/TIL/assets/77138259/36ffd100-b6b1-4eab-bb7c-7bbafdc6bc2c" alt="top" />

### 1) 요약 영역
- 요약 영역은 전체 프로세스가 OS에 대해서 리소스를 어느정도 차지하고 있는지를 알려준다. 요약 영역에 나타나는 대표적인 값은 시간, 유저, 로드 에버리지(Load Average), 테스크(Tasks), 메모리(memory) 이다.

### 2) 로드 애버리지(Load Average)
- Load Average는 시스템의 부하를 나타내는 지표로, 주로 UNIX 및 UNIX 계열 운영체제에서 사용된다.
- 이 숫자는 일정 시간 동안의 CPU 부하를 나타내며, 일반적으로 1분, 5분, 15분의 평균값을 표시하며 각각의 숫자는 해당 시간 동안의 부하를 나타낸다.
- 일반적으로 CPU 코어당 1.0 정도가 정상이지만, 시스템이 여유로울 때에도 높은 값을 가질 수 있다. 하지만 Load Average가 지속적으로 높다면, 시스템 리소스 부족이나 다른 문제가 있을 수 있음을 나타낼 수 있다.
- 현재 위의 프로세스는 코어 수가 4개로, 로드 에버리지가 14.16, 14.19, 14.43 으로 코어 4개를 나눴을 때, 3.565 정도가 도출되므로 약 357%의 사용을 하고 있어 CPU에서 처리하지 못하고 대기 중인 프로세스가 있다는 것을 알 수 있다.

### 3) Tasks
- 현재 프로세스들의 상태를 나타내주는 영역이다.
- Total은 전체 프로세스, running은 running 상태인 프로세스, sleeping은 대기상태인 process, stopped는 종료된 프로세스, zombies는 좀비상태인 프로세스의 수를 나타낸다.

### 4) CPU 사용량
- %Cpu(s)는 CPU가 어떻게 사용되고 있는지 그 사용율을 보여주는 영역이다. 모든 값의 총 합은 100%이며, 이를 %로 나누어 보여준다. 각 요소는 아래와 같다.
	- us(User): 프로세스의 유저 영역에서의 CPU 사용률(사용자 영역)
	- sy(System): 프로세스의 커널 영역에서의 CPU 사용률(시스템 영역)
	- ni(Nice): 프로세스의 우선순위(priority) 설정에 사용하는 CPU 사용률
	- id(Idle): 사용하고 있지 않는 비율. 높은 값은 CPU가 여유로움을 나타냄
	- wa(I/O Wait): 입출력(I/O) 작업이 완료될 때까지 기다리고 있는 CPU 비율
	- hi(Hardware Interrupts): 하드웨어 인터럽트에 사용되는 CPU 사용률
	- si(Software Interrupts): 소프트웨어 인터럽트에 사용되는 CPU 사용률
	- st(Steal Time): CPU를 VM에서 사용하여 대기하는 CPU 비율. 일반적으로 가상화 환경에서 사용됨

### 5) 메모리 사용량
- 시스템의 메모리(RAM) 및 스왑(Swap) 사용량에 관한 정보를 나타내주는 영역이다.
- MiB Mem(메모리)은 RAM의 메모리 영역이며 MiB Swap(스왑)은 디스크를 메모리 처럼 이용하는 Swap 메모리 영역이다.
- 일반적으로 Mem의 사용량이 거의 가득 찼을때 Swap 메모리 영역을 사용하며 이 영역은 디스크이기 때문에 RAM 메모리보다 속도가 많이 느린 단점을 가진다.
	- total: 총 메모리 양
	- free: 사용 가능한 메모리 양
	- used: 사용 중인 메모리 양
	- buff/cache: 버퍼(커널 버퍼)와 캐시(디스크의 페이지 캐시)로 사용되고 있는 메모리 양. 즉, I/O와 관련되어 사용되는 버퍼에 사용되는 메모리
	- avail Mem: swap 메모리를 사용하지 않고 사용할 수 있는 메모리의 크기

### 6) 디테일 영역
- 디테일 영역에는 각 프로세스에 대한 상세한 내용이 나온다.
- PID
    - 프로세스 ID로 프로세스를 구분하기 위한 겹치지 않는 고유한 값
-  USER
    - 해당 프로세스를 실행한 USER 이름 또는 효과를 받는 USER의 이름
- PR & NI
    - PR : 커널에 의해서 스케줄링되는 우선순위
    - NI : PR에 영향을 주는 nice라는 값
- VIRT, RES, SHR, %MEM
    - 해당 필드들은 프로세스의 메모리와 관련
    - VIRT : 프로세스가 소비하고 있는 총 메모리. 프로그램이 실행중인 코드, heap, stack과 같은 메모리, IO buffer 메모리를 포함
    - RES : RAM에서 사용중인 메모리의 크기
    - SHR : 다른 프로세스와의 공유메모리(Shared Memory)
    - %MEM : RAM에서 RES가 차지하는 비율
- S : 프로세스의 현재 상태(Process State)
	- S(Sleeping): 프로세스가 메모리에 로드되어 있지만 아무런 활동이 없는 상태
	- R(Running): 프로세스가 현재 실행 중인 상태로 CPU 사용 중
	- D(Disk sleep): 프로세스가 디스크에서 데이터를 읽거나 기다리는 상태
	- Z(Zombie): 프로세스가 종료되었지만, 부모 프로세스가 해당 종료 상태를 아직 확인하지 않은 상태
	- T(Stopped): 프로세스가 일시 중지된 상태
- TIME+ : 프로세스가 사용한 토탈 CPU 시간
- COMMAND : 해당 프로세스를 실행한 커맨드

## 2. 프로세스 정렬
### 1) Sorting
- 디테일 영역에 대해 원하는 값 기준으로 정렬
	- ‘M’ to sort by memory usage
	- ‘P’ to sort by CPU usage
	- ‘N’ to sort by process ID
	- ‘T’ to sort by the running time
	- ‘R’ to sort by 오름차순과 내림차순을 토글 변경

### 2) Filtering
- 프로세스가 너무 많다면 필터링 기능을 사용
- top이 실행 중인 상태에서 o 또는 O 입력

- 예를 들어 아래와 같이 입력 시,
<img src="https://github.com/JaeyeoneeJ/TIL/assets/77138259/96679f46-8ef2-4991-865a-f790c689e25e" alt="top-filtering" />

- 아래와 같은 결과가 도출됨
<img src="https://github.com/JaeyeoneeJ/TIL/assets/77138259/8e1833cb-e734-4c70-b87c-b3ff0729d40d" alt="top-filtering-end" />
- 즉, COMMAND에 web을 포함한 결과만 출력 가능


<hr>
### ref
- https://sabarada.tistory.com/146