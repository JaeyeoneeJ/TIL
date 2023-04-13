- 해당 자료는 [Nginx에 SSL(HTTPS) 적용하기](../Linux/Nginx에_SSL(HTTPS)_적용하기.md) 이후의 내용을 다루고 있음

### 1) 인증키 옮기기
```bash
sudo cp -r /etc/letsencrypt/live/{domain_name} /home/secure
```
- 위 링크에서 받은 인증키를 스프링 부트에 적용하기 위해 `home/secure`로 옮겨줌


### 2) 파일 변환
```bash

```