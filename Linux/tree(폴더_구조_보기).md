## 0. 개요
- `tree` 명령어는 파일 및 디렉터리 구조를 트리 형식으로 표시하는 명령어이다.
- 이 명령어를 사용하려면 시스템에 이미 설치되어 있거나, 필요하다면 설치해야 한다.


## 1. 설치 방법
### 1) Linux
- 대부분의 Linux 배포판에서는 기본적으로 설치되어 있을 수 있다.
- 만약 설치되어 있지 않다면 다음 명령어를 사용하여 설치할 수 있다.
```bash
sudo apt-get install tree   # Debian/Ubuntu
sudo yum install tree       # CentOS/RHEL
```
### 2) macOS
- macOS에서는 Homebrew를 사용하여 `tree`를 설치할 수 있다.
- Homebrew가 설치되어 있지 않다면 먼저 설치해야 한다.

```bash
brew install tree
```
### 3) Windows
- Windows에서는 기본적으로 `tree` 명령어가 제공되지 않는다.
- 대신 Git Bash, Cygwin 등의 환경에서 사용할 수 있다.
- Git Bash를 사용하는 경우 Git을 설치하면 함께 제공된다.

- Git Bash를 통해 `tree` 명령어를 사용할 수 있다.


## 2. 사용 방법
- `tree` 명령어는 다양한 옵션을 제공하여 디렉터리 및 파일 구조에 대한 출력을 조절할 수 있다.
- 일반적인 `tree` 명령어 사용법은 다음과 같다.
```bash
tree [옵션] [디렉터리 또는 경로]
```

- 여러 플래그와 옵션을 사용하여 출력을 조절할 수 있다. 몇 가지 주요 옵션은 다음과 같다.
	- `-a` 또는 `--all`: 숨겨진 파일 및 디렉터리를 포함하여 모든 파일을 표시
	- `-d` 또는 `--dirs-only`: 디렉터리만 표시하고 파일은 표시하지 않음
	- `-L level` 또는 `--max-depth level`: 트리를 표시하는 최대 깊이를 설정
	- `-I pattern` 또는 `--exclude pattern`: 특정 패턴을 가진 파일이나 디렉터리를 제외
	- `-f` 또는 `--full-path`: 전체 경로를 표시

### 예시
- 현재 경로에서 node_modules 폴더를 제외하고 숨김 파일을 표시
```bash
tree -I node_modules -a .
```