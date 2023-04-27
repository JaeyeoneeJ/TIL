## 개요
- 나는 내가 만든 Java Spring Boot 프로젝트를 로컬에서, 더 나아가 EC2 환경에서 자동으로 배포하고 빌드 에러 시 문제점을 한 번에 알 필요성을 느꼈음
- Jenkins를 통해 Java Spring Boot를 배포하기 위해서는 몇 가지 도구가 필요함
	- JDK 설치: Java 개발 환경이 설치되어 있어야 함
	- Gradle 설치: Java 프로젝트를 빌드하기 위한 도구
	- Jenkins 설치: Jenkins는 지속적인 통합 도구로, 코드 변경 사항이 발생할 때마다 빌드 및 배포를 자동화할 수 있음

## Jenkins란
- Jenkins는 지속적인 통합(Continuous Integration, CI) 및 지속적인 배포(Continuous Deployment, CD) 프로세스를 구현하는 데 사용되는 오픈 소스 도구임
- 이를 통해 소프트웨어 개발자는 코드 변경사항이 자동으로 빌드, 테스트 및 배포되도록 자동화할 수 있음
- Jenkins를 사용하면 다음과 같은 이점을 얻을 수 있음
	1. 빠른 피드백
		- Jenkins를 사용하면 코드 변경사항이 발생할 때마다 자동으로 빌드 및 테스트를 수행하므로 빠른 피드백을 받을 수 있음
		- 이는 문제가 발생했을 때, 문제를 빠르게 발견하고 수정할 수 있도록 도와줌
	2. 자동화
		- Jenkins를 사용하면 빌드, 테스트 및 배포 프로세스를 자동화할 수 있음
		- 이를 통해 개발자는 반복적이고 시간 소모적인 작업을 수행하지 않고도 더 많은 시간을 코드 개발 및 품질 향상에 할애할 수 있음
	3. 안정성
		- Jenkins를 사용하면 개발자가 수동으로 수행할 수 있는 작업을 자동화하므로 빌드 및 배포 프로세스의 안정성이 향상됨
		- 이는 소프트웨어의 품질을 향상시키고 오류가 발생할 가능성을 줄여줌
	4. 확장성
		- Jenkins는 다양한 플러그인과 함께 제공되며 사용자가 필요한 플러그인을 설치하여 사용할 수 있음
		- 이를 통해 Jenkins를 사용자의 요구에 맞게 확장ㅎ할 수 있음
- 따라서 Jenkins를 사용하면 코드 변경사항이 자동으로 빌드, 테스트 및 배포되도록 자동화하여 소프트웨어 개발 프로세스를 개선할 수 있음

## Jenkins 설치
- 참고로 나는 EC2 환경에서 Linux Ubuntu 18.04 버전을 사용하고 있고, 개발 환경을 Java 8(1.8)로 통합해 놓은 상황임
- 이로 인해 문제가 생겼음. Jenkins 2.357 및 LTS 버전(LTS 2.361.1)은 Java 11 또는 17을 필요로 함
- 즉, Jenkins LTS의 공식 시스템 요구사항에서도 Java 11이 권장되고 있는 것임

> [jenkins java 요구사항](https://www.jenkins.io/doc/administration/requirements/java/)

- Jenkins의 이전 LTS 버전인 Jenkins LTS 2.222.4 버전은 Java 8을 지원하므로 해당 버전을 사용할 수도 있겠지만 이미 릴리즈된 이전 버전이므로 보안상의 이유 등으로 최신 버전을 사용하는 것이 좋음
- 때문에 나는 최신 버전을 설치하고 사용 자바를 8 버전으로 바꾸는 작업을 해보겠음(삽질 시작)

### 1) MacOS
- 먼저 Homebrew가 설치되어 있어야 함
- Homebrew를 사용하여 Jenkins를 설치하기 위해 아래 명령어를 실행함
```bash
brew install jenkins-lts
# ...
# Note: When using launchctl the port will be 8080.
# To restart jenkins-lts after an upgrade:
#   brew services restart jenkins-lts
# Or, if you don't want/need a background service you can just run:
#   /opt/homebrew/opt/openjdk@17/bin/java -Dmail.smtp.starttls.enable=true -jar /opt/homebrew/opt/jenkins-lts/libexec/jenkins.war --httpListenAddress=127.0.0.1 --httpPort=8080
```

- 설치를 마치면 위와 같은 문구가 출력됨
- 하나씩 뜯어보자
	1. Note: When using launchctl the port will be 8080.
		- Jenkins가 기본적으로 8080 포트를 사용한다는 것을 알려줌
		- 이는 Jenkins 서비스를 브라우저에서 접근할 때 사용하는 포트임
		- 물론 포트 번호는 웹 인터페이스에서 변경할 수 있음
	2. To restart jenkins-lts after an upgrade:   brew services restart jenkins-lts
		- Jenkins-lts를 업그레이드 한 후에 Jenkins를 다시 시작하는 방법을 알려줌
		- 이는 Jenkins-lts를 설치할 때 자동으로 설정된 Jenkins 서비스를 다시 시작하여 변경 사항을 적용하는 방법임
	3. Or, if you don't want/need a background service you can just run:  /opt/homebrew/opt/openjdk@17/bin/java -Dmail.smtp.starttls.enable=true -jar /opt/homebrew/opt/jenkins-lts/libexec/jenkins.war --httpListenAddress=127.0.0.1 --httpPort=8080
		- Jenkins를 실행하는 명령어임
		- 이 명령어를 실행하면 localhost의 8080 포트에서 실행됨
		- 이 명령어를 통해 Jenkins를 실행한 후 브라우저에서 http://localhost:8080 에 접속하여 Jenkins에 로그인하고 사용할 수 있음

- 설치가 정상적으로 되었으니 Jenkins 서버를 구동해서 웹 인터페이스에서 설정을 만져보자
```bash
/opt/homebrew/opt/openjdk@17/bin/java -Dmail.smtp.starttls.enable=true -jar /opt/homebrew/opt/jenkins-lts/libexec/jenkins.war --httpListenAddress=127.0.0.1 --httpPort=8080
# Running from: /opt/homebrew/Cellar/jenkins-lts/2.387.2/libexec/jenkins.war
# webroot: /Users/{user_name}/.jenkins/war
# ...
# *************************************************************
# *************************************************************
# *************************************************************
# 
# Jenkins initial setup is required. An admin user has been created and a password generated.
# Please use the following password to proceed to installation:
# 
# asdfasdfasdfasdfasdfasdf <- Unlock Jenkins를 위한 비밀번호
# 
# This may also be found at: /Users/{user_name}/.jenkins/secrets/initialAdminPassword
```

- 실행 시 알 수 있는 것
	- Jenkins는 Java 17 버전의 JDK를 사용하고 있다는 것임(openjdk@17)
	- 구동되는 서버는 http://localhost:8080 이라는 것
	- Jenkins-lts의 버전은 2.387.2 라는 것
	- Jenkins에 접속하기 위한 초기 비밀번호는 /Users/{user_name}/.jenkins/secrets/initialAdminPassword 에서 찾을 수 있다는 것

- Jenkins 서버를 실행했다면 이제 웹 브라우저의 url에 http://localhost:8080 을 입력해보자
<img src="https://user-images.githubusercontent.com/77138259/234767889-8131a981-baad-4a28-87e9-bb90cac59da9.png" alt="Jenkins 초기 로그인" />
- Jenkins 서버를 처음 설치하고 로그인하면 'Unlock Jenkins' 페이지가 표시됨
- 아까 확인한 초기 비밀번호(여기서는 asdfasdfasdf...)를 입력하고 Continue 버튼을 누름
- 만약 까먹었다면 위 그림과 같이 빨간색으로 표기된 경로에 접속해서 초기 비밀번호를 찾아서 입력함
```bash
cat /Users/{user_name}/.jenkins/secrets/initialAdminPassword
# asdfasdfasdfasdfasdfasdf
```

- Jenkins의 "Customize Jenkins" 페이지가 표시됨
- 이 페이지에서 "Install suggested plugins"를 선택하면 Jenkins에서 권장하는 플러그인을 자동으로 설치할 수 있음(나는 자동 설치)
- 또는 "Select plugins to install" 옵션을 선택하여 수동으로 플러그인을 선택할 수도 있음

<img src="https://user-images.githubusercontent.com/77138259/234768760-a4d581ae-4ae3-434f-a42b-67df1fce0bf6.png" alt="Jenkins 플러그인 설치 중" />
- 참고로 터미널에서 서버를 실행 중이라면 열심히 로그가 쌓이고 있는 것을 알 수 있음
- 설치가 끝나면 아래와 같이 뜸
```bash
# ...
# b#run: Completed installation of 82 plugins in 3 min 11 sec
```
- 뭔가 상당히 많이 설치되는 것 같음. 다음에 필요 없는 플러그인을 제거해야겠다는 생각이 듬

- 플러그인 설치가 완료되면 Jenkins 관리자 계정을 만들어야 함
- 이메일 주소와 암호를 입력하고 "Save and Continue" 버튼을 클릭함
<img src="https://user-images.githubusercontent.com/77138259/234769424-dad7ff23-57ef-4156-bc7c-d4946d199d67.png" alt="Jenkins 계정 설정" />
- 다음으로 Jenkins Web UI에 접근하기 위한 Jenkins URL을 입력하고 Save and Finish를 선택함

## 접속 포트 변경
- Jenkins의 기본 포트는 8080이지만 대부분 8080 포트는 다른 프로그램에서도 기본 포트로 사용하기 때문에 충돌이 일어날 확률이 매우 높음(Tomcat이나 Upsource 등 대부분의 소프트웨어는 8080을 기본 포트로 사용하고 있음)
- 따라서 나는 9090으로 변경하겠음
- 우리는 jenkins를 실행시킬 때 이미 실행 경로를 파악해놨음
```bash
# 앞에서 jenkins 서버 실행 했을 때 코드
/opt/homebrew/opt/openjdk@17/bin/java -Dmail.smtp.starttls.enable=true -jar /opt/homebrew/opt/jenkins-lts/libexec/jenkins.war --httpListenAddress=127.0.0.1 --httpPort=8080
# Running from: /opt/homebrew/Cellar/jenkins-lts/2.387.2/libexec/jenkins.war <- 이부분을 보면 /opt/homebrew/Cellar/jenkins-lts/2.387.2/ 에서부터 jenkins가 시작되는 것을 알 수 있음
# webroot: /Users/{user_name}/.jenkins/war
# ...

# 따라서 아래 명령어를 통해 설정파일을 변경함
vi /opt/homebrew/Cellar/jenkins-lts/2.387.2/homebrew.mxcl.jenkins-lts.plist
```

```plist
...
	<string>--httpPort=9090</string>
```
- `<string>--httpPort=8080</string>`을 9090으로 변경해 줌
- 참고로 `<string>--httpListenAddress=127.0.0.1</string>`의 127.0.0.1 이 부분을 0.0.0.0으로 변경하면 공인 IP 또는 사설 IP로도 접근이 가능함. 물론 특정 IP만 접근할 수 있도록 설정할 수도 있음
- 추가적으로 `<key>RunAtLoad</key><true/>` 는 부팅 시 자동 실행이며, `<false/>`를 해주면 자동 실행이 되지 않음

- 다시 Jenkins를 실행해보자
```bash
/opt/homebrew/opt/openjdk@17/bin/java -Dmail.smtp.starttls.enable=true -jar /opt/homebrew/opt/jenkins-lts/libexec/jenkins.war --httpListenAddress=127.0.0.1 --httpPort=9090
```

- 전에는 8080 포트로 실행했고, 이번에는 9090 포트로 실행했음
- 기본적인 설정 상 jenkins 경로에서 `java -jav jenkins.war`을 실행하면 되었겠지만, 나의 경우 jdk 설정이 java 8로 되어있었기 때문에 임시적으로 저렇게 실행을 함

- 브라우저에서 http://localhost:9090 에 접속하면 정상적으로 Jenkins에 로그인 되는 것을 확인할 수 있음

## JDK 설치
- 앞에서 언급한 것 처럼 java 8로 개발 환경을 통합하였지만 현재 설치한 Jenkins는 java 11을 최소 버전으로 지원함
- 우선 Jenkins가 구동되고 있는 서버에 Java가 설치되어 있어야 함
- 보통 기본적으로 환경 변수로 `$JAVA_HOME`을 설정했을 것임. 어떻게 설정되어 있나 확인해보겠음
```bash
echo $JAVA_HOME
```
- 만일 위 환경 변수를 불러왔지만 아무 것도 뜨지 않아 당황스럽다면 `source ~/.bash_profile` 명령을 실행하면 됨. 환경 변수가 초기화 된 이유는 여러가지가 있지만, 아마도 Jenkins를 실행하면서 강제로 Java version을 17로 구동했기 때문에 JAVA_HOME의 내용을 제거하지 않았을까 라고 추측하고 있음
- 아니면 직접 확인하는 방법도 있음
```bash
ls /Library/Java/JavaVirtualMachines/
# adoptopenjdk-8.jdk jdk1.8.0_361.jdk zulu-17.jdk
```

- java가 설치되어 있는 기본 경로로 이동하면 현재 내 PC에 설치되어 있는 jdk들을 보여줌
- 나는 여기서 adoptopenjdk-8.jdk를 사용하도록 하겠음(EC2의 Linux Ubuntu 18.04는 apt 패키지로 다운받았기 때문에 adoptopenjdk 로 설치되어 있어 개발 환경을 일치시키고자 함)

- 브라우저에서 http://localhost:9090 에 접속하고 Dashborad(왼쪽 사이드바 메뉴)에서 Jenkins 관리를 클릭
- System Configuration의 Global Tool Configuration 클릭
- JDK 항목에 아래와 같이 추가
<img src="https://user-images.githubusercontent.com/77138259/234781870-be4e98b3-dc6e-441c-aacc-4987c5541408.png" alt="Jenkins JDK 설정" />

- 아래 `Save` 버튼을 클릭하고 JDK가 잘 설정되었는지 간단한 확인을 해보자
- 바로 build 시  execute shell을 하나 추가해서 java -version을 찍는 것임
- Dashboard > 새로운 Item 클릭 > java-jdk-8-build-test 입력(마음대로 작성해도 됨) > Freestyle project 클릭
- Configuration에서 Build Steps에서 Add build step > Execute Shell 클릭
- 새 쉘 스크립트 창에 아래 내용 입력
```bash
#!/bin/bash
echo $JAVA_HOME
java -version
```
- `save` 버튼을 클릭하여 변경 사항 저장
- Dashboard > java-jdk-8-build-test 의 사이드바에서 지금 빌드(Build Now) 버튼을 클릭하여 새 빌드를 시작

> 주의: 클릭한 수 만큼 빌드되니 반드시 한 번만 클릭하자

- 빌드가 완료되면 고정링크 또는 Build History에 `#1` 빌드가 생성되었을 것임
- 클릭 후, Console Output을 클릭하여 내용을 확인
```console
Started by user [홍길동](http://localhost:9090/user/{user_name})
Running as SYSTEM
Building in workspace /Users/{user_name}/.jenkins/workspace/java-jdk-8-build-test
[java-jdk-8-build-test] $ /bin/bash /var/folders/3n/q6dqjv7s43v_nwbjrvvk5c2w0000gn/T/jenkins12289147565810915352.sh
/Library/Java/JavaVirtualMachines/adoptopenjdk-8.jdk/Contents/Home
openjdk version "1.8.0_292".
OpenJDK Runtime Environment (AdoptOpenJDK)(build 1.8.0_292-b10)
OpenJDK 64-Bit Server VM (AdoptOpenJDK)(build 25.292-b10, mixed mode)
Finished: SUCCESS
```

- 위와 같이 openjdk version이 1.8로 정상적으로 설정되었으며 success로 잘 완료된 것을 확인할 수 있음