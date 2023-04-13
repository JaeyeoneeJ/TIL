- 이미 실행 중인 애플리케이션 때문에 골치 아픔을 겪어본 사람들은 이미 많이 사용해보았을 것임
- 하지만, kill -9 과 kill -15 등 많은 옵션이 있었기에 어떤 경우에 쓰는지 궁금해서 자세히 알아보기로 했음

- 리눅스에서 kill 명령어를 통해 프로세스를 지정하고 신호를 보내서 제어하는 것을 알아보자
- kill 명령어는 주로 프로세스를 종료하는 용도로 많이 사용됨
- 나는 실제로 `lsof -i:[포트번호]`로 검색해서 `kill -9 [PID]`로 강제 종료하는 방법을 많이 사용함
```bash
# 3000번 포트를 사용하고 있지 않으면 아무 것도 뜨지 않음
lsof -i:3000

# 만일 사용하는 포트가 있다면 아래와 같이 뜸
lsof -i:3000
# COMMAND   PID USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
# node    12345 jjy   31u  IPv4 0xc5fe1234e3d9r99e      0t0  TCP *:hbci (LISTEN)
```

- 만약 위와 같이 3000번 포트를 사용하고 있다고 가정해보자
- 현재 node로 PID 12345를 jjy라는 사용자가 이용중인 것을 확인할 수 있음

- kill 명령의 가장 간단한 사용방법은 kill 뒤에 -9 옵션으로 프로세스 아이디(PID)를 지정하고 종료 신호를 입력하는 것이 가장 일반적임
- 여기서 kill 명령어로 해당 포트를 끄려면 아래와 같이 입력
```bash
kill -9 12345
```

- 잘 꺼졌는지 확인
```bash
lsof -i:3000
# 아무것도 뜨지 않으면 잘 꺼진 것
```

- kill 명령어 뒤에 -l 을 사용하면 kill의 신호로 사용할 수 있는 신호 이름을 보여줌
```bash
kill -l
# 1) SIGHUP       2) SIGINT        3) SIGQUIT      4) SIGILL       5) SIGTRAP
# 6) SIGABRT      7) SIGBUS        8) SIGFPE       9) SIGKILL     10) SIGUSR1
# 11) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
# 16) SIGSTKFLT   17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
# 21) SIGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU     25) SIGXFSZ
# 26) SIGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGIO       30) SIGPWR
# 31) SIGSYS      34) SIGRTMIN    35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3
# 38) SIGRTMIN+4  39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8
# 43) SIGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12 47) SIGRTMIN+13
# 48) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12
# 53) SIGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7 
# 58) SIGRTMAX-6  59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2
# 63) SIGRTMAX-1  64) SIGRTMAX
```

- kill 명령어에서 -l 옵션을 사용하면 사용 가능한 모든 신호(Signal)를 확인할 수 있음
|**번호**|**신호(Signal) 이름**|**신호(Signal)**|**의미**|
|---|---|---|---|
|1|SIGHUP|HUP|hangup, 로그아웃등의 접속이 끊을 때 발생하는 신호(Signal)로 특정 실행 중인 프로그램이 사용하는 설정 파일을 변경시키고 변화된 내용을 적용할때 사용됨|
|2|SIGINT|INT|현재 작동중인 프로그램의 동작을 멈출때 사용되며, 일반적인 값은 `<CTRL>+<c>`임|
|9|SIGKILL|KILL|프로그램을 무조건 종료할 경우 사용|
|11|SIGSEGV|SEGV|잘못된 메모리 관리시 생기는 신호(Signal)|
|15|SIGTERM|TERM|실행중인 프로그램을 정상적인 종료방법으로 프로그램을 종료하는 신호(Signal)로 kill 명령에서 신호(Signal)를 지정하지 않으면 이 신호(Signal)를 사용하여 프로그램을 종료|
|18|SIGCONT|CONT|중지 되어 있는 프로그램을 재실행 하는데 사용되는 신호(Signal)|
|19|SIGSTOP|STOP|프로그램을 중지 하는데 사용되는 신호(Signal)|
|20|SIGTSTP|TSTP|터미널에서 중지되어 있는 신호(Signal)|

- 신호(Signal)은 번호, 신호(Signal)이름으로 신호(Signal)로 보낼 수 있음
- 또한, 위 표에 옵션은 자주 사용되는 신호(Signal)로 강제로 종료시킬때 사용되는 -9 -SIGKILL 등 옵션을 주어 사용하면 됨


<hr>
## ref
- https://server-talk.tistory.com/432