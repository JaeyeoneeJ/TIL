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
- Terminal을 열어 다음 명령어를 실행함
```bash
sudo add-apt-repository ppa:jonathonf/ffmpeg-4
sudo apt-get update sudo apt-get install ffmpeg
```
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
