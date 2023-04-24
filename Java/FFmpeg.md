## FFmpeg란
- FFmpeg는 비디오와 오디오를 다루는 라이브러리로, 비디오 및 오디오를 편집, 변환, 인코딩 및 디코딩하는데 사용됨
- FFmpeg는 커맨드라인 인터페이스를 제공하며, 다양한 형식의 비디오 및 오디오 파일을 처리할 수 있음
- Java Spring Boot 프레임워크에서는 FFmpeg 라이브러리를 사용하여 비디오 및 오디오 파일을 처리할 수 있음
- 예를 들어, Spring Boot를 사용하여 웹 애플리케이션을 개발하면, FFmpeg를 사용하여 비디오 및 오디오 파일을 업로드, 변환 및 스트리밍할 수 있음

- 또한, Spring Boot와 FFmpeg를 함께 사용하여 실시간 비디오 스트리밍 서비스를 구축할 수 있음
- 이를 위해서는 FFmpeg를 사용하여 비디오를 캡처하고, Spring Boot를 사용하여 해당 비디오를 클라이언트에게 전송하는 기능을 구현할 수 있음 이를 통해, 실시간 비디오 스트리밍 서비스를 제공할 수 있음

## FFmpeg 설치 방법
- FFmpeg 라이브러리는 시스템에 직접 설치해야 함
- 라이브러리를 설치한 후에는 Java에서 해당 라이브러리를 사용하기 위한 바인딩을 다운로드하고 프로젝트에 추가해야 함. 
- Windows, macOS, Linux 등 각 운영체제별로 설치 방법이 조금씩 다를 수 있음

### 1) Windows
- [FFmpeg 공식 사이트](https://ffmpeg.org/download.html#build-windows)에서 `Windows Builds`를 클릭함
- 내려받은 파일을 압축해제 함
- FFmpeg 실행 파일(`ffmpeg.exe`, `ffprobe.exe`)을 이용할 프로젝트 폴더 등에 복사함

### 2) macOS
- [FFmpeg 공식 사이트](https://ffmpeg.org/download.html#build-mac)에서 macOS용 FFmpeg을 내려받음
- 다운로드 받은 파일을 압축해제 함
- Terminal을 열어 다음 명령어를 실행함
```bash
sudo mv [FFmpeg 설치 경로]/ffmpeg /usr/local/bin/
sudo mv [FFmpeg 설치 경로]/ffprobe /usr/local/bin/
```

- 또는 brew를 사용하여 다운로드 할 수 있음
```bash
brew install ffmpeg
```
- 설치가 완료되면, `ffmpeg` 명령어를 사용하여 FFmpeg가 제대로 설치되었는지 확인할 수 있음
```bash
ffmpeg -version
```

### 3) Linux (Ubuntu)
- Terminal을 열어 다음 명령어를 실행하여 시스템 패키지 목록을 업데이트 함
```bash
sudo apt-get update
```

- 아래 명령어로 `ffmpeg` 패키지를 설치함
```bash
sudo apt-get install ffmpeg
```

- 설치가 완료되면 아래 명령어로 `ffmpeg`가 제대로 설치되었는지 확인
```bash
ffmpeg -version
# ffmpeg version 3.4.11-0ubuntu0.1 Copyright (c) 2000-2022 the FFmpeg developers
# built with gcc 7 (Ubuntu 7.5.0-3ubuntu1~18.04)
# ...
```
- 위와 같이 버전 정보 등이 출력되면 정상적으로 설치된 것임

- 만약 Ubuntu가 아닌 다른 Linux 배포판을 사용하고 있다면, 해당 배포판의 패키지 매니저를 이용해 FFmpeg을 설치할 수 있음
- 예를 들어, CentOS의 경우 `yum install ffmpeg` 명령어를 사용할 수 있음

- 위와 같은 방법으로 FFmpeg을 시스템에 설치한 후, Spring Boot 프로젝트에서 해당 라이브러리를 사용할 수 있음


## FFmpeg 설치 경로 추가
- FFmpeg 설치 경로를 입력하는 것은 Java 코드에서 FFmpeg 실행 파일을 찾을 수 있도록 경로를 설정하는 것임. 따라서 이 경로는 Java 코드에서 설정해줘야 함
- 일반적으로는 Java 코드에서 FFmpeg 실행 파일의 경로를 하드코딩하는 것보다, 환경변수를 사용하여 설정하는 것이 더욱 유연하게 관리할 수 있음
- 이를 위해서는 운영 체제에서 FFmpeg를 설치할 때, 환경변수를 설정해주어야 함

### 1) MacOS
- `ffmpeg` 명령어로 설치된 경로를 찾음
```bash
which ffmpeg
# /opt/homebrew/bin/ffmpeg
```
- 설치된 경로가 출력되면 이 경로를 메모장 등 텍스트 에디터에 복사해 둠

- `~/.bash_profile` 파일을 열어 `PATH` 환경 변수에 FFmpeg 설치 경로를 추가함
```bash
vi ~/.bash_profile   # 파일 열기
```

- 아래와 같이 수정하였음
```bash
# .bash_profile 기존

JAVA_HOME=/Library/Java/JavaVirtualMachines/adoptopenjdk-8.jdk/Contents/Home
PATH=$PATH:$JAVA_HOME/bin
export JAVA_HOME
export PATH
```

```bash
# .bash_profile 변경

JAVA_HOME=/Library/Java/JavaVirtualMachines/adoptopenjdk-8.jdk/Contents/Home FFMPEG_HOME=/opt/homebrew/bin # <- 추가
PATH=$PATH:$JAVA_HOME/bin:$FFMPEG_HOME # <- 수정
export JAVA_HOME
export PATH
```

- 수정 후, 변경사항을 적용해 주고 잘 적용되었는지 확인
```bash
source ~/.bash_profile # 변경사항 적용
echo $PATH # 적용 여부 확인
```

### 2) Linux (Ubuntu)
- MacOS와 마찬가지로 `ffmpeg` 명령어로 설치된 경로를 찾음
- 아마 초기 경로는 /usr/bin/ffmpeg 일 것임
```bash
which ffmpeg
# /usr/bin/ffmpeg
```
- 설치된 경로가 출력되면 이 경로를 메모장 등 텍스트 에디터에 복사해 둠

- 우리는 초기 경로를 그대로 쓰지 않고 로컬 경로로 옮기는 작업을 할 것임.

> **로컬 경로로 바꿔쓰는 이유**
> 
> `/usr/bin/ffmpeg`는 Ubuntu에서 기본적으로 제공하는 `ffmpeg` 패키지의 설치 경로임. 이 경로는 시스템 전역에서 사용할 수 있도록 설정되어 있음
> 
> 하지만 `/usr/local/bin/ffmpeg`와 같은 경로는 사용자가 직접 컴파일한 또는 컴파일된 `ffmpeg`의 실행 파일을 설치하는 경로 중에 하나임. 일반적으로 이 경로를 사용하면 사용자가 설치한 패키지와 시스템 기본 패키지간에 충돌을 방지할 수 있음. 또한 사용자가 컴파일한 `ffmpeg`를 이 경로에 설치하면 사용자가 더 많은 제어권을 가질 수 있음
> 
> 따라서, `/usr/local/bin/ffmpeg`은 `/usr/bin/ffmpeg`보다 사용자 정의가 더 용이하고 시스템 전역에서 패키지 충돌을 방지할 수 있는 장점이 있음
> 
> 단, 경로를 변경하면 기존에 `/usr/bin/ffmpeg` 경로에 설치된 패키지와의 호환성 문제가 발생할 수 있으므로 주의해야 함. 기존에 설치된 패키지가 있을 경우 새로운 경로에 설치하기 전에 해당 패키지를 삭제하거나 기존 경로를 사용하는 것이 좋음

- cp 명령어를 사용하여 로컬 경로로 파일을 복사함
```bash
sudo cp /usr/bin/ffmpeg /usr/local/bin/
```
- 위의 경우, 기존의 `ffmpeg` 패키지와 충돌이 발생할 수 있으므로 복사 후, `which ffmpeg`를 사용하여 복사한 파일에 문제가 없다는 것을 확인하고 원본 설치경로를 지우는게 좋음

- `/etc/profile` 파일을 수정하여 `ffmpeg` 실행 파일 경로를 환경 변수로 추가하면 모든 사용자에게 적용할 수 있음
- 아래 명령어를 입력하여 `/etc/profile` 파일을 염
```bash
sudo vi /etc/profile
```

- 파일의 맨 아래에 다음과 같이 로컬로 옮긴 경로를 추가해 줌
```vi
...
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export PATH=$PATH:/usr/local/bin
```
- 위 코드에서 자바 경로를 추가했다면 첫 번째 줄은 무시해도 됨
- 변경 사항이 모든 사용자에게 적용되도록 다음 명령어를 입력함

```bash
source /etc/profile
```