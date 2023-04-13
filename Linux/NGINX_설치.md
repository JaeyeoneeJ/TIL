## NGINX란
- 서버 인스턴스에는 클라이언트의 요청을 처리해주는 서버 소프트웨어가 필요함
- 서버 소프트웨어는 크게 웹 서버와 웹 애플리케이션 서버(WAP; Web Application Server)로 구성됨
- 웹 서버는 클라이언트에서 HTTP 프로토콜로 요청을 받고 HTML, CSS, Javascript, 이미지와 같은 정적인 파일을 응답으로 전할 수 있음
- 반면, 웹 애플리케이션 서버는 클라이언트 요청에 의해 서버의 배포된 코드를 실행시키고 동적인 응답을 만들어 줌
- 웹 서버와 웹 애플리케이션 서버는 함께 사용되면서 웹 서버는 정적인 파일을 처리하거나 웹 애플리케이션 서버로 라우팅하는 역할을 하고, 웹 애플리케이션 서버는 동적인 처리가 필요한 요청이 온 것을 처리해서 응답해주는 것들을 할 수 있음
- 웹 서버 중 대표적인 product로는 Nginx와 apache가 있고, 이번 배포의 경우, 웹 서버로 nginx를 사용하고자 함
- Nginx 웹 서버를 맨 앞단에 가짜 서버로 두고, 뒷단에 웹 서버를 두는 reverse proxy 구조로 설계하여 보안성을 강화할 수 있음

## NGINX 설치
- EC2에 접속해서 다음 명령어로 서버의 패키지 목록을 업데이트함
```bash
sudo apt update
sudo apt upgrade
sudo apt autoremove
```

- 다음 명령어로 NGINX 설치
```bash
sudo apt install nginx
```

- 실행
```bash
sudo service nginx start
sudo service nginx status
```

- 정상적으로 실행되었으면 Active: active (running)이라는 메세지가 뜰 것임
<img src="https://user-images.githubusercontent.com/77138259/231050896-99b97efa-cbde-4bf5-81cf-bc9947ea932a.png" alt="nginx 실행" />

- Nginx Version 확인
```bash
sudo dpkg -l nginx
```

## 보안그룹 추가
- 먼저 nginx의 포트번호를 보안 그룹에 추가하겠음
- nginx의 포트번호는 기본적으로 80임. 해당 포트 번호가 보안 그룹에 없으니 `EC2 → 보안 그룹 → EC2 보안 그룹 선택 → 인바운드 편집` 으로 차례로 이동해서 변경함
<img src="https://user-images.githubusercontent.com/77138259/231051505-df2407ba-5ac1-4b2f-9510-35b2d207f191.png" alt="인바운드 규칙 편집" />

## NGINX와 Spring Boot 연동
- nginx가 현재 실행 중인 스프링 부트 프로젝트를 바라볼 수 있도록 프록시 설정을 하겠음

- nginx가 설치된 경로 찾기
```bash
sudo find / -name nginx.conf
# /etc/nginx/nginx.conf
```

- 먼저 nginx 설정 파일을 열어보자
```bash
sudo vim /etc/nginx/nginx.conf
```

## nginx 구동 테스트
```bash
netstat -lntp
```
- 80번 포트가 리스닝되고 있으면 실행된 상태임
<img src="https://user-images.githubusercontent.com/77138259/231052986-ca896ed8-2354-4068-829a-2acc75d4261d.png" alt="nginx 구동 상태" />

- 만약 netstat이 없다면 아래 명령어로 설치
```bash
sudo apt install net-tools
```

- 퍼블릭 IP로 접속해보면 웰컴 페이지가 반겨줌
<img src="https://user-images.githubusercontent.com/77138259/231054465-6440dbd8-17a5-40dd-be32-f5b9ff8bc3fd.png" alt="nginx 구동 확인" />
- 이제 Nginx를 이용해서 HTTP 80번 포트로 접속했을 때 Tomcat 8080번 포트로 리다이렉트할 수 있게 해보겠음

## Nginx server block 설정

- Nginx 웹 서버를 사용할 때, (Apache의 가상 호스트와 유사한) 서버 블록을 사용해 구성 세부 정보를 캡슐화하고 단일 서버에서 둘 이상의 도메인을 호스팅할 수 있음
- nginx는 기본적으로 활성화된 하나의 서버 블록(`/var/www/html`)이 있으며, 단일 사이트에서는 잘 작동하지만 여러 사이트를 호스팅할 경우 작동하지 않을 수 있음
- 여러 사이트를 호스팅할 경우에는 (`/var/www/{domain}/html`)과 같은 방식으로 디렉토리 구조를 수정해 적용할 수 있음

### Nginx 파일 및 디렉토리
1. 콘텐츠
- `/var/www/html`: 기본적으로 기본 Nginx 페이지가 있음. Nginx 구성 파일을 변경할 수 있음

2. 서버 구성
- `/etc/nginx/nginx.conf`: 기본 Nginx 구성 파일임. Nginx 전역 구성을 변경하도록 수정할 수 있음
- `/etc/nginx/sites-available/`: 사이트 별로 서버 블록을 저장할 수 있는 디렉토리임. Nginx는 `sites-enabled` 디렉토리에 연결되지 않은 경우 이 디렉토리의 구성 파일을 사용하지 않음. 일반적으로 `sites-available` 디렉토리에서 서버 블록이 구성된 후에 다른 디렉토리에 연결되어 사용할 수 있게 됨
- `/etc/nginx/sites-enabled`: 활성화된 사이트별 서버 믈록이 저장되는 디렉토리임. 일반적으로 `sites-available` 디렉토리에 있는 구성파일에 연결해 생성함

3. 서버 로그
- `/var/log/nginx/access.log`: 별도로 구성되지 않는 한 웹 서버에 대한 모든 요청이 기록됨
- `/var/log/nginx/error.log`: 모든 Nginx 오류가 기록됨

4. nginx.conf
- 우선 `sudo cat /etc/nginx/nginx.conf`를 실행해 기본 Nginx 구성 파일을 살펴보겠음
<img src="https://user-images.githubusercontent.com/77138259/231055873-752f2808-5ca1-4716-a129-2dac182b574a.png" alt="nginx.conf" />
- Nginx가 `/etc/nginx/conf.d/*.conf` 파일들과 `/etc/nginx/sites-enabled` 디렉토리의 모든 파일들을 include 해온다는 것을 알 수 있음
- 만약 이 두 줄이 없으면 http { ... } 안에 vi 에디터로 붙여넣으면 됨


## 연동하기
### 1) proxy 설정
```bash
sudo mkdir /var/log/nginx/proxy   # log, error 파일이 들어갈 디렉토리 생성
sudo vi /etc/nginx/proxy_params
```
- 포트번호가 80인 http에서 요청이 오면, Spring Boot 프로젝트에서 8080번 포트를 바라볼 수 있도록 proxy 설정을 해주겠음
- `proxy_params`에 아래의 코드를 붙여넣어 줌
```vi
# 넘겨 받을 때 프록시 헤더 정보 지정
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header Host $http_host;
proxy_set_header X-Forwarded-Proto $scheme;
proxy_set_header X-NginX-Proxy true;

client_max_body_size 256M;
client_body_buffer_size 1m;

proxy_buffering on;
proxy_buffers 256 16k;
proxy_buffer_size 128k;
proxy_busy_buffers_size 256k;

proxy_temp_file_write_size 256k;
proxy_max_temp_file_size 1024m;

proxy_connect_timeout 300;
proxy_send_timeout 300;
proxy_read_timeout 300;
proxy_intercept_errors on;
```

### 2) 서버 블록 생성
- 현재 가지고 있는 도메인과 jar 파일을 이용하기 위해서 기본 구성 파일을 수정해 서버 블록을 생성해주겠음
- 명령어 `sudo vi /etc/nginx/sites-available/{domain}` 입력 후, 아래 코드를 복사해 붙여넣어 줌
	- `{domain}` : '.com', '.co.kr' 등이 포함되어 있어야 함
	- http:// 빼주자. 이것때문에 vi 에디터가 can't open file for writing이 계속 떴다.
```vi
server { # server 블록
	listen 80;

    server_name {domain} www.{domain};
    
    access_log /var/log/nginx/proxy/access.log;
    error_log /var/log/nginx/proxy/error.log;

    location / { # location 블록
        include /etc/nginx/proxy_params;
        proxy_pass http://{퍼블릭IP주소}:8080;	# reverse proxy의 기능
    }
}
```
- location 블록의 `proxy_pass`를 통해 8080번 포트를 통해 접속해야 볼 수 있는 화면(Spring Boot 프로젝트 화면)을 80번(HTTP) 포트에 접속했을 때 확인할 수 있도록 설정함
- 즉, `Reverse proxy`의 기능을 하게 할 수 있음

> Server 블록
> 
> Nginx는 이제 listen 지시문에 의해 포트번호 80으로 들어오는 요청들에 대해 server_name 값과 정확하게 일치하는 서버 블록을 찾으려고 시도할 것임
> 
> 참고: server_name을 추가할 때, 해시 버킷 메모리 문제가 발생할 수 있음. 이를 방지하기 위해 `/etc/nginx/nginx.conf` 파일에서 옵션을 조정해주겠음
```bash
sudo vi /etc/nginx/nginx.conf
```
<img src="https://user-images.githubusercontent.com/77138259/231065817-4cd59411-1a0f-4fb2-a5c9-73254a64d61f.png" alt="nginx.conf" />

### 3) 새로 생성한 파일 활성화
- 이제 `sites-available` 디렉토리로부터 `site-enabled` 디렉토리에 대한 링크를 생성해 파일을 활성화해보겠음
```bash
sudo ln -s /etc/nginx/sites-available/{domain} /etc/nginx/sites-enabled/
```

### 4) 기본 구성 파일 삭제
- `sites-available` 디렉토리로부터 `site-enabled` 디렉토리에서 명령어 `ls -al`을 실행해보면 원래 nginx 서버를 연결하던 default 파일이 새로 생성한 파일과 함께 보임
```bash
ls -al /etc/nginx/sites-available
ls -al /etc/nginx/sites-enabled
```

<img src="https://user-images.githubusercontent.com/77138259/231066540-5bae2535-898b-41e4-aece-e2bc8142a091.png" alt="nginx.conf" />
- 이대로 연결할 경우 에러가 발생하기 때문에 default 파일을 삭제해주겠음
```bash
sudo rm /etc/nginx/sites-available/default
sudo rm /etc/nginx/sites-enabled/default
```

### 5) Nginx 재시작
- Nginx에 대해 구문 오류가 없는지 테스트하고, Spring Boot 프로젝트와 함께 재시작해보자
```bash
sudo nginx -t
# nginx: [warn] could not build optimal proxy_headers_hash, you should increase either proxy_headers_hash_max_size: 512 or proxy_headers_hash_bucket_size: 64; ignoring proxy_headers_hash_bucket_size
# nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
# nginx: configuration file /etc/nginx/nginx.conf test is successful
```
- 나의 경우 위와 같은 경고문이 출력되었음
- 현재 nginx가 참고하고 있는 nginx.conf는 `/etc/nginx/nginx.conf`이므로 해당 경로의 파일을 수정해주자
```bash
sudo vi /etc/nginx/nginx.conf
```
```vi
...
http {
	...
	
	##
	# proxy 설정 추가
	##

	proxy_headers_hash_max_size 51200;
	proxy_headers_hash_bucket_size 6400;
}
```
- 위의 숫자는 원하는 숫자를 넣어도 상관 없음(나의 경우, 기존 숫자보다 100배씩 해줬음)
- 다시 테스트를 해보면 정상적으로 뜨는 것을 확인할 수 있음
```bash
sudo nginx -t
# nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
# nginx: configuration file /etc/nginx/nginx.conf test is successful
```

- nginx를 재부팅 함
```bash
sudo service nginx reload
```

## Nginx default 설정
- 이것저것 귀찮으면 아래 대로 진행하면 됨
```bash
sudo vi /etc/nginx/sites-available/default
```

```vi
...

server {
	...
	location / {
                proxy_pass http://localhost:8080;    # <- 위에 4줄 추가
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header Host $http_host;
  
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                # try_files $uri $uri/ =404;   # <- 주석처리
        }
	...
}
```

- 참고로 파일 수정 전에 반드시 백업을 해두는 것이 좋음
```bash
cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default.backup
```
- 이렇게 백업을 미리 해두면 설정 파일을 수정한 후에 오류가 발생할 경우, 백업 파일을 복원하여 이전 상태로 되돌릴 수 있음

## Nginx 연결 오류
- 실행중인 오류 목록을 보려면 항상 Nginx 오류 로그를 참조할 수 있음
```bash
sudo cat /var/log/nginx/error.log
```

### 방화벽 설정
- 루트 또는 sudo 권한이 있는 사용자만 시스템 방화벽을 관리할 수 있음
- Ubuntu 20.04에는 기본적으로 UFW가 시스템 내부에 설치되어 있음. 만약 설치되지 않은 경우 패키지를 설치해야 함
```bash
sudo apt update
sudo apt install ufw
```

- UFW는 기본적으로 비활성화되어 있음. 다음 명령어로 UFW 서비스 상태를 확인할 수 있음
```bash
sudo ufw status verbose
# Status: inactive <- 비활성화 되어있는 상태
```



<hr>
### ref
- https://libertegrace.tistory.com/entry/NGINX-1
- https://velog.io/@u-nij/Spring-Boot-Nginx-%EC%97%B0%EB%8F%99%ED%95%B4%EC%84%9C-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0
