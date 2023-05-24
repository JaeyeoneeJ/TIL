## nvm 이란
- node.js는 javascript를 서버 측 애플리케이션 개발에 사용되는 런타임 환경이며, 지속적으로 버전이 업데이트되고 개선됨
- nvm은 node.js 버전 매니저로 시스템에 여러 개의 node.js를 설치하고 사용할 버전을 쉽게 전환할 수 있도록 도와주는 shell script임
- nvm을 사용하면 원하는 node.js 버전을 설치하고, 프로젝트별로 해당 버전을 사용할 수 있음. 이는 다양한 프로젝트가 서로 다른 node.js 버전을 요구할 때 특히 유용함
- nvm은 주로 MacOS와 Linux 시스템에서 사용되지만 Windows에서도 WSL(Windows Subsystem for Linux)을 통해 사용할 수 있음

> WSL은 windows 10부터 지원되는 기능임. 기존 windows 사용자들은 WSL 외에 Git Bash 등을 사용하여 nvm을 관리하는 편임

- nvm은 명령줄 인터페이스를 제공하여 node.js 버전을 설치, 관리 및 전환하는 작업을 수행함

## NVM 설치
### 1) MacOS
- homebrew를 통해 nvm을 설치함
```bash
brew install nvm
```

- nvm을 사용할 수 있도록 터미널 프로필 파일에 설정을 추가함
```bash
vi ~/.bash_profile
vi ~/.zshrc
vi ~/.profile
vi ~/.bashrc
```

- 위 명령어 중 하나를 선택하여 프로필 파일을 염
- 프로필 파일에 다음 줄을 추가함
```bash
source $(brew --prefix nvm)/nvm.sh
```

- 터미널을 닫았다가 다시 열면 셋팅이 완료됨

## nvm 사용
- 터미널에 nvm을 입력하면 다양한 옵션을 확인할 수 있음

### 1) node.js 버전 설치 및 사용
```bash
nvm install 버전
```

- 만약 14.16.1 버전의 node를 설치하려면 아래 명령어를 입력
```bash
nvm install 14.16.1
```

- 설치된 버전(예를 들어 14.16.1)의 node.js를 사용하려면 아래 명령어를 입력
```bash
nvm use 14.16.1
```

- 현재 내 로컬에 설치되어 있는 node.js 버전 목록을 확인하려면 아래 명령어를 입력
```bash
nvm list
```

- nvm을 통해 use로 버전을 바꿔주면 npm도 자동으로 변경되기 때문에 접근성이 뛰어남