## 1. MacOS
- java 설치 확인
```bash
java -version
# java version "1.8.0_361"
# Java(TM) SE Runtime Environment (build 1.8.0_361-b09)
# Java HotSpot(TM) 64-Bit Server VM (build 25.361-b09, mixed mode)

javac -version
# javac 1.8.0_361
```

MacOS에서 JDK 기본 설치 경로는 `/Library/Java/JavaVirtualMachines`임
```bash
ls /Library/Java/JavaVirtualMachines
# adoptopenjdk-8.jdk jdk1.8.0_361.jdk zulu-17.jdk
```
- 나의 경우에는 설치된 자바 버전이 여러 개임. 여기서 `adoptopenjdk-8.jdk`을 JAVA_HOME에 설정하겠음

- 사용자 Home으로 이동 후 환경설정을 위해 vi 에디터로 `.bash_profile`을 만듦
```bash
cd ~
vi .bash_profile

# 또는
vi ~/.bash_profile
```

- 아래 내용을 작성. JAVA_HOME 경로는 각자 JAVA Version에 맞게 설정
```vi
JAVA_HOME=/Library/Java/JavaVirtualMachines/adoptopenjdk-8.jdk/Contents/Home
PATH=$PATH:$JAVA_HOME/bin
export JAVA_HOME
export PATH
```

- vi 에디터 저장 후, source 명령어를 이용해 환경설정을 적용함
```bash
cd ~
source .bash_profile

# 또는
source ~/.bash_profile
```

- 환경 설정이 잘 적용되었는지 확인하기 위해 `JAVA_HOME`을 출력해 봄
```bash
echo $JAVA_HOME
```

- 환경 설정에 따른 java가 잘 적용되었는지 확인
```bash
java -version
# openjdk version "1.8.0_292"
# OpenJDK Runtime Environment (AdoptOpenJDK)(build 1.8.0_292-b10)
# OpenJDK 64-Bit Server VM (AdoptOpenJDK)(build 25.292-b10, mixed mode)

javac -version
# javac 1.8.0_292
```
- java 버전이 바뀌어 잘 적용된 것을 확인할 수 있음(원래 1.8.0_361 => 1.8.0_292)


## 2. linux ubuntu 설정
- java 설치 확인
```bash
java -version
# openjdk version "1.8.0_362"
javac -version
# javac 1.8.0_362
```

- java 설치 위치 확인
```bash
which java
# /usr/bin/java
readlink -f /usr/bin/java
# /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java

which javac
# /usr/bin/javac
readlink -f /usr/bin/javac
# /usr/lib/jvm/java-8-openjdk-amd64/bin/javac
```

- 관리자 권한으로 profile 열기
```bash
sudo vi /etc/profile
```

- profile 내용 맨 하단에 JAVA_HOME 설정
```vi
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

- 환경설정 소스 적용
```bash
source /etc/profile
```

- 적용 확인
```bash
echo $JAVA_HOME
# /usr/lib/jvm/java-8-openjdk-amd64
```
