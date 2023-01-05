## TCP(Transmission Control Protocol)
인터넷상에서 데이터를 메세지의 형태로 보내기 위해 IP와 함께 사용하는 프로토콜
TCP는 신뢰성 있는 데이터 전송을 지원하는 연결 지향형 프로토콜이다.
일반적으로 TCP와 IP가 함께 사용되는데, IP가 데이터의 전송을 처리한다면 TCP는 패킷 추적 및 관리를 하게 된다.
연결 지향형인 TCP는 3-way handshaking이라는 과정을 통해 연결 후 통신을 시작하는데, 흐름 제어와 혼잡 제어를 지원하며 데이터의 순서를 보장한다.
- 흐름 제어: 보내는 측과 받는 측의 데이터 처리속도 차이를 조절해주는 것
- 혼잡 제어: 네트워크 내 패킷 수가 넘치게 증가하지 않도록 방지하는 것
예시) 파일 전송

### TCP 특징
- 연결형 서비스로 가상 회선 방식을 제공
- 데이터의 전송 순서 보장
- 서버와 클라이언트는 1:1로 연결
- 데이터의 경계를 구분하지 않음
- 신뢰성 있는 데이터 전송
- UDP보다 전송속도가 느림
- 연결을 설정(3-way handshaking)과 해제(4-way handshaking)


## UDP(User Datagram Protocol)
데이터를 데이터그램 단위로 처리하는 프로토콜
UDP는 비연결형 프로토콜로써, 인터넷상에서 서로 정보를 주고받을 때 정보를 보내거나 받는다는 신호 절차를 거치지 않고 보내는 쪽에서 일방적으로 데이터를 전달하는 통신 프로토콜이다.
TCP와는 다르게 연결 설정이 없으며, 혼잡 제어를 하지 않기 때문에 TCP보다 전송 속도가 빠르다.
하지만 데이터 전송에 대한 보장을 하지 않기 때문에 패킷 손실이 발생할 수 있다.
예시) 신뢰성보다는 연속성이 중요한 서비스인 스트리밍 등에 자주 사용됨

### UDP 특징
- 비연결형 서비스로 데이터그램 방식을 제공
- 비신뢰성
- 데이터의 경계를 구분
- 패킷 오버헤드가 적어 네트워크 부하 감소
- 혼잡 제어를 하지 않기 때문에 TCP보다 빠름
- TCP의 handshaking과 같은 연결 설정이 없음

⭐️ TCP vs UDP

| **TCP(Transfer Control Protocol)** | **UDP(User Datagram Protocol)** |
| :---: | :---: |
| 연결형 프로토콜 | 비연결형 프로토콜 |
| 데이터의 경계를 구분하지 않음 | 데이터의 경계를 구분함 |
| 신뢰성있는 데이터 전송 (데이터 재전송 존재O) | 비신뢰성 데이터 전송 (데이터 재전송 존재 X) |
| 1:1(Unicast) 통신 | 1:1, 1:N(Broadcast), N:N(Multicast) 통신 |

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbUl39w%2FbtrlDXobQDK%2FiTeMkl6e2YYEYrNguUijT1%2Fimg.webp" alt="TCP, UDP 차이" />

## 요약
TCP는 연속성보다 신뢰성 있는 전송이 중요할 때에 사용되는 프로토콜로 주로 파일 전송 등에 사용된다.
UDP는 TCP보다 빠르고 네트워크 부하가 적다는 장점이 있지만 신뢰성 있는 데이터 전송을 보장하지는 않는다.
그렇기 때문에 신뢰성보다는 연속성이 중요한 실시간 스트리밍과 같은 서비스에 자주 사용된다.


<hr>
## ref.
https://mangkyu.tistory.com/15
https://cocoon1787.tistory.com/757

