- 자 이제 도메인에 ssl/https를 적용해보자

## Certbot 설치하기
- Certbot은 Let's Encrypt 인증서를 자동으로 발급 및 갱신을 해주는 봇 프로그램임

### 1) snap를 이용하여 certbot 설치
- 아래 명령으로도 certbot을 설치할 수 있으나, Ubuntu 최신 버전(v20)에서는 동작하지 않았음
```bash
sudo wget https://dl.eff.org/certbot-auto
```

- 따라서 snap를 이용해 설치해줘야 함
```bash
# snap을 이용하여 core 설치 -> snap을 최신 버전으로 유지하기 위해 설치
sudo snap install core

# core를 refresh 해준다.
sudo snap refresh core

# 기존에 잘못된 certbot이 설치되어있을 수도 있으니 삭제 해준다.
sudo apt remove certbot

# certbot 설치
sudo snap install --classic certbot

# certbot 명령을 로컬에서 실행할 수 있도록 snap의 certbot 파일을 로컬의 cerbot과 링크(연결) 시켜준다. -s 옵션은 심볼릭링크를 하겠다는 것.
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

- ssl 발급을 위해 아래 명령어를 입력함. 여기서 example.com 은 실제 도메인으로 변경되어야 함
```bash
sudo certbot certonly --standalone -d example.com -d www.example.com
```
- 처음에 긴급갱신 및 보안 공지 시에 사용하기 위해 이메일을 입력하라고 뜨므로 본인 이메일 입력
- 약관 동의 Y
- 광고성 메일 수신 N
- SSL 발급 받고자 하는 도메인 입력
- 아래 메세지가 뜨면 정상적으로 발급된 것임
<img src="https://user-images.githubusercontent.com/77138259/231111451-92c73519-f258-415a-875a-831dd40be8f1.png" alt="ssl 발급" />

- 위 두줄이 각각 공개키와 비밀키 경로이므로 기억해두자
- 아래 네모 칸이 https가 설정된 도메인임

```bash
sudo vi /etc/nginx/sites-available/default
```

- 여기까지 설정을 마쳤다면 certbot이 알아서 Nginx 설정까지 해줬기 때문에 https 설정도 되어있을 것임
- 나의 경우 아래와 같이 설정되어 있었음
<img src="https://user-images.githubusercontent.com/77138259/231115025-0ffdac56-fce5-4945-803e-5b0d66c0e62e.png" alt="sites-available/default" />

### 갱신
- certbot을 이용하여 ssl인증서를 발급할 경우 3개월 마다 갱신을 해줘야 함
- 무료이기 때문에 안전하게 서비스를 이용하려면 갱신해야 함
- 아래 명령을 통해 갱신해줄 수 있음
```bash
certbot renew
```
- 인증서의 유효기간이 끝나가면 본인이 certbot을 통해 ssl인증서를 받아올 때 입력해 준 `이메일`로 알림이 오게 됨
- 해당 메일을 받으면 위 명령을 통해 갱신해주면 됨
- 그마저도 귀찮으면 아래 링크 참조
> [Let's Encrypt SSL 인증서 자동 갱신 설정 방법](https://devlog.jwgo.kr/2019/04/16/how-to-lets-encrypt-ssl-renew/)


<hr>
### ref
- https://gist.github.com/woorim960/dda0bc85599f61a025bb8ac471dfaf7a
