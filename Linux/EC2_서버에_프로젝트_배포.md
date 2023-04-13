- 본 내용은 Database의 [RDS_인스턴스_생성하기](../Database/RDS_인스턴스_생성하기.md) 이후의 내용을 다루고 있음

## EC2에 프로젝트 Clone 받기
- 먼저 깃허브에서 코드를 받아올 수 있게 EC2에 깃을 설치하겠음
- 나의 EC2는 Ubuntu 20.04 LTS 버전을 쓰고 있어서 git이 기본적으로 깔려있음
- 깃 설치 여부 확인
```bash
git --version
```

- 만일 깃이 깔려있지 않다면 아래 명령어를 통해 설치
```bash
sudo apt-get install git
```
- 정상적으로 설치되었는지 버전 체크를 통해 확인하자

- 깃이 성공적으로 설치되면 git clone으로 프로젝트를 저장할 디렉토리를 생성하겠음
```bash
mkdir ~/app && mkdir ~/app/step1
```

- 생성된 디렉토리로 이동
```bash
cd ~/app/step1
```

- 본인 깃허브 웹페이지에서 https 주소를 복사함
- 복사한 https 주소를 통해 git clone을 진행함
```bash
git clone {복사한 주소}
```

- git clone이 끝났으면 클론도딘 프로젝트로 이동해서 파일들이 잘 복사되었는지 확인함
```bash
cd {프로젝트 명}
ll
```

## 배포 스크립트 만들기
- 여기서의 배포는 다음의 과정을 모두 포괄하는 것임
	- git clone 혹은 git pull을 통해 새 버전의 프로젝트를 받음
	- Gradle이나 Maven을 통해 프로젝트 테스트와 빌드
	- EC2 서버에서 해당 프로젝트 실행 및 재실행
- 앞선 과정을 배포할 대마다 개발자가 하나하나 명령어를 실행하는 것은 불편함이 많음
- 그래서 이를 쉘 스크립트로 작성해 스크립트만 실행하면 앞의 과정이 차례대로 진행되도록 하겠음

>쉘 스크립트는 .sh라는 파일 확장자를 가진 파일로 NodeJS가 .js 라는 파일을 통해 서버에서 작동하는 것처럼 쉘 스크립트 역시 리눅스에서 기본적으로 사용할 수 있는 스크립트 파일의 한 종류임

- ~/app/step1/에 deploy.sh 파일을 하나 생성함
```bash
vim ~/app/step1/deploy.sh
```

- 아래 내용을 작성
```vim
#!/bin/bash

REPOSITORY=/home/ubuntu/app/step1
PROJECT_NAME=test-web

cd $REPOSITORY/$PROJECT_NAME/

echo "> Git Pull"
git pull

echo "> 프로젝트 Build 시작"
./gradlew build

echo "> step1 디렉토리로 이동"
cd $REPOSITORY

echo "> Build 파일 복사"
cp $REPOSITORY/$PROJECT_NAME/build/libs/*.jar $REPOSITORY

echo "> 현재 구동중인 애플리케이션 pid 확인"
# CURRENT_PID=$(pgrep -f ${PROJECT_NAME}.*.jar)
CURRENT_PID=$(ps -ef | grep ${PROJECT_NAME} | grep -v grep | awk '{print $2}')

echo "> 현재 구동중인 애플리케이션 pid: $CURRENT_PID"
if [ -z "$CURRENT_PID" ]; then
	echo "> 현재 구동 중인 애플리케이션이 없으므로 종료하지 않습니다."
else
	echo "> kill -15 $CURRENT_PID"
	kill -15 $CURRENT_PID
	sleep 5
fi

echo "> 새 애플리케이션 배포"
JAR_NAME=$(ls -tr $REPOSITORY/ | grep jar | tail -n 1)

echo "> JAR Name: $JAR_NAME"
nohup java -jar $REPOSITORY/$JAR_NAME > log.out 2>&1 &
```

- 이렇게 생성한 스크립트에 실행 권한을 추가
```bash
chmod +x ./deploy.sh
```

- 이제 이 스크립트를 다음 명령어로 실행함
```bash
./deploy.sh
```