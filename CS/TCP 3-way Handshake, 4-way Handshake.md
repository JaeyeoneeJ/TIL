## TCP 3-way Handshake
간단요약: 연결하고자 하는 두 장치 간의 논리적 접속을 성립하기 위해 사용하는 연결 확인 방식으로, 3번의 확인 과정을 거친다고 해서 3 way handshake라고 부른다.
  
TCP 3 way handshake를 간단히 표현하면 다음과 같다.
- A -> B : 내 말 들려?
- B -> A : 잘 들려. 내 말은 들려?
- A -> B : 잘 들려!

### 개념
- TCP 통신을 이용하여 데이터를 전송하기 위해 네트워크 연결을 설정(Connection Establish)하는 과정
- 양쪽 모두 데이터를 전송할 준비가 되었다는 것을 보장하고, 실제로 데이터 전달이 시작하기 전에 한 쪽이 다른 쪽이 준비되었다는 것을 알 수 있도록 한다.
- 즉, TCP/IP 프로토콜을 이용해서 통신을 하는 응용 프로그램이 데이터를 전송하기 전에 먼저 정확한 전송을 보장하기 위해 상대방 컴퓨터와 사전에 세션을 수립하는 과정을 의미한다.
- TCP 통신은 PAR(Positive Acknowledgement with Re-transmission)을 통해 신뢰적인 통신을 제공한다.

### 작동 방식
- Client는 Server와 연결하기 위해 3-way handshake를 통해 연결 요청을 하게 된다.
- 우리가 일반적으로 생각하는 Client와 Server는 모두 서로 연결 요청을 먼저 할 수 있기 때문에, 연결 요청을 먼저 시도한 요청자를 Client로, 연결 요청을 받은 수신자를 Server 쪽으로 생각하고 보면 될 것 같다.

- SYN(Synchronization sequence Numbers): 연결 확인을 위해 보내는 무작위의 숫자 값(내 말 잘 들려?)
- ACK(Acknowledgements): Client 또는 Server로부터 받은 SYN에 1을 더해 SYN을 잘 받았다는 ACK(잘 들려)
- ISN(Initial Sequence Numbers): Client와 Server가 각각 처음으로 생성한 SYN

<img width="100%" src="https://goodgid.github.io/assets/img/network/tcp_ip_3way_4way_1.png" alt="TCP 3-way handshake 기본 매커니즘" >

### SYN의 값에 무작위 수를 사용하는 이유?
- Connection을 맺을 때 사용하는 포트는 유한 범위 내에서 사용하고 시간이 지남에 따라 재사용된다.
- 따라서 두 통신 호스트가 과거에 사용된 포트 번호 쌍을 사용할 가능성이 존재한다.
- 서버측에서 패킷의 SYN을 보고 패킷을 구분하므로 난수가 아닌 순차적인 숫자가 전송된다면 이전의 connection으로부터 오는 패킷으로 인식할 수 있어 이러한 문제 발생 가능성을 줄이기 위해 ISN을 무작위 난수로 사용한다.

| **상태** | **설명** |
| :---: | :--- |
| Closed | 연결 수립을 시작하기 전의 기본 상태(연결 없음) |
| Listen | 포트가 열린 상태로 연결 요청을 대기 중 |
| SYN-sent | SYN 요청을 한 상태 |
| SYN-reseived | SYN 요청을 받고 상대방의 응답을 기다리는 중 |
| Established | 연결의 수립이 완료된 상태, 서로 데이터를 교환할 수 있다. |

- 예시
<img src="https://t1.daumcdn.net/cfile/tistory/99AE67425B32FEE81B" alt="TCP 3-way handshake 기본 매커니즘" >
	- Client가 연결하고자 하는 Server에게 seq port (2915269997) 전송
	- Server가 해당 seq port를 받고 해당 숫자에 1을 더한 ack port (2915269998) + Client의 연결을 확인하고자 하는 seq port (1458477026) 전송
	- Client가 Server의 seq port를 받고 해당 숫자에 1을 더한 ack port (1458477027)을 Server에 전송

## TCP 4-way Handshake
- 3-way handshake와 반대로 가상 회선 연결을 해제할 때 주고 받는 확인작업이다.
- 이 역시 4번의 확인 과정을 거친다고 하여 4-way handshake라고 부른다.

TCP 4-way handshake를 간단히 표현하면 다음과 같다.
- A -> B : 나는 다 보냈어. 이제 끊자!
- B -> A: 알겠어! 잠시만~
- B -> A: 나도 끊을게!
- A -> B: 알겠어!

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FtQR1l%2FbtqyJRYdm3E%2F143elB5WCHDlofiAsax2J1%2Fimg.png" alt="TCP 4-way handshake 기본 매커니즘" >
- FIN(Finish): TCP 연결을 종료하겠다는 메세지. 더 이상 보낼 데이터가 없음을 의미함

| **상태** | **설명** |
| :---: | :--- |
| Closed | 연결 수립을 시작하기 전의 기본 상태(연결 없음) |
| Established | 연결의 수립이 완료된 상태, 서로 데이터를 교환할 수 있다. |
| Close-wait | 상대방의 FIN(종료 요청)을 받은 상태. 상대방 FIN에 대한 ACK를 보내고 애플리케이션에 종료를 알린다. |
| Last-ACK | Close-wait 상태를 처리 후, 자신의 FIN 요청을 보낸 후, FIN에 대한 ACK를 기다리는 상태 |
| FIN-wait-1 | 자신이 보낸 FIN에 대한 ACK를 기다리거나 상대방의 FIN을 기다린다. |
| FIN-wait-2 | 자신이 보낸 FIN에 대한 ACK를 받았고 상대방의 FIN을 기다린다. |
| Closing | 상대방의 FIN에 ACK를 보냈지만 자신의 FIN에 대한 ACK를 못 받은 상태 |
| Time-wait | 모든 FIN에 대한 ACK를 받고 연결 종료가 된 상태. 새 연결과 겹치지 않도록 일정 시간 동안 기다린 후, Closed로 전이한다. |

### Time-Wait이란?
- 먼저 연결을 끊는(active closer) 쪽에 생성되는 소켓으로, 혹시 모를 패킷 전송 실패에 대비하기 위하여 존재하는 소켓이다.
- 만일 Time-Wait이 없다면 패킷의 손실이 발생하거나 통신자 간 연결 해제가 제대로 이루어지지 않을 수 있다.
	- 만약 Server에서  FIN 플래그를 전송하기 전에 전송한 패킷이 Routing 지연이나 패킷 유실로 인한 재전송 등으로 인해 FIN 패킷보다 늦게 도착하는 상황이 발생할 경우,
	- 이러한 현상을 대비하여 Client는 Server로부터 FIN 플래그를 수신하더라도 일정시간(Default: 240sec) 동안 세션을 남겨 놓고 잉여 패킷을 기다리는 과정을 거친다.(Time-wait 과정)

### TCP의 연결 설정 과정(3단계)와 연결 종료 과정(4단계)이 단계가 차이나는 이유는?
- Client가 데이터 전송을 마쳤다고 하더라도 Server는 아직 보낼 데이터가 남아있을 수 있기 때문에 일단 FIN에 대한 ACK만 보내고, 데이터를 모두 전송한 후에 자신도 FIN 메세지를 보내기 때문에 종료 과정에서 단계가 1개 추가된다.

<hr>
## ref.
https://seongonion.tistory.com/74
https://velog.io/@averycode/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-TCPUDP%EC%99%80-3-Way-Handshake4-Way-Handshake