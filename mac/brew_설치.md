## brew란
- Homebrew는 macOS와 Linux용의 패키지 관리자이다. 이 도구를 사용하면 터미널에서 명령어를 사용하여 손쉽게 다양한 소프트웨어를 설치, 업데이트, 제거할 수 있다.
- 일반적으로 Homebrew를 사용하여 설치하는 소프트웨어는 커맨드 라인 도구, 개발 도구, 라이브러리, 어플리케이션 등이며, macOS 시스템에서 손쉽게 관리할 수 있다.
- 또한 Homebrew는 패키지들을 온라인 저장소에서 관리하고, 패키지간의 종속성을 해결하고, 패키지를 업데이트하는 등의 기능을 제공하여 시스템 관리를 훨씬 간편하게 해준다.
- 예를 들어, Homebrew를 사용하여 Python, Node.js, Git 등과 같은 소프트웨어를 설치할 수 있다. 또한 macOS 시스템의 패키지들을 최신 버전으로 업데이트하거나 관리하는 데도 사용할 수 있다.


## brew 설치
- 우선 [brew 공식 사이트](https://brew.sh/ko/)에 접속한다.
- 설치 스크립트를 복사(사이트 내 최신 스크립트를 복사할 것)하고 터미널에 붙여넣는다.
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

- 잘 설치되었는지 `brew -v`로  확인한다.
