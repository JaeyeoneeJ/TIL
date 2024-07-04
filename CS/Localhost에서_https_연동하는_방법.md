- 개발서버를 https로 연동해야 하는 이유는 여러가지가 있지만,
- 나의 경우, 다른 기기에서 카메라 등 외부 기기 사용을 테스트해야 하는데, 일반 브라우저에서는 보안 상 문제로 https에서만 외부 기기 접근을 허용하므로 개발 중인 서버를 https로 구현해야 하는 문제가 발생함
- mkcert로 하는 방법은 브라우저에서 mkcert 인증서를 유효하게 받아들이지 않아 사용 불가함

## ngrok 활용

### 1) 설치
1. Mac
```bash
brew install ngrok
```

2. Window
- 아래 링크 접속, 다운로드 후 설치
- https://dashboard.ngrok.com/get-started/setup/windows

> 번외: choco로 설치하는 방법도 있음
```bash
choco install ngrok
```

### 2) ngrok 계정 생성 및 연결
1. 계정생성
- https://dashboard.ngrok.com/signup

2. 인증 토큰 생성
- https://dashboard.ngrok.com/get-started/your-authtoken

3. 계정 연결
```bash
ngrok config add-authtoken $YOUR_AUTHTOKEN
# or
ngrok authtoken $YOUR_AUTHTOKEN
```

### 3) 로컬 서버 실행
- 띄울 localhost 서버를 먼저 실행한다.(ex: localhost:3000)
- ngrok으로 https 서버를 호출한다.
```bash
ngrok http 3000
```

``` bash
# Forwarding
# https://xxxx-xxx-xxx-xxx-xx.ngrok-free.app -> http://localhost:3000
```

- 여기서 내가 띄운 `http://localhost:3000` 이 `https://83ed-211-221-208-16.ngrok-free.app`로 포워딩되어 띄워진 것을 알 수 있다.
- 위에서 알려준 url을 복사하여 실행하면 끝
