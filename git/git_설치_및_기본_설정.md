## git 설치
### 1) MacOS
- homebrew는 MacOS용 패키지 관리자로, Git을 비롯한 다양한 유용한 도구와 라이브러리를 쉽게 설치하고 관리할 수 있음
- homebrew가 설치되어 있다면 아래 명령어를 입력하여 git 설치
```bash
brew install git
```

- 잘 설치되었는지 git의 현재 버전을 출력
```bash
git --version
# git version 2.40.0
```

### 2) Linux (Ubuntu)
- 터미널을 열고 다음 명령어를 실행하여 apt 패키지 목록을 업데이트 함
```bash
sudo apt-get update
```

- 다음 명령어를 실행하여 git을 설치함
```bash
sudo apt-get install git
```

잘 설치되었는지 git의 현재 버전을 출력
```bash
git --version
# git version 2.17.1
```


## git 설정
- 아래 명령어를 입력하면 git config 명령어의 속성과 사용 방법에 대해 알려줌
```bash
git config
```

- 우리는 git에 현재 설정되어 있는 정보를 확인하기 위해 `--list` 속성을 사용하여 설정 값을 확인하겠음
```bash
git config --list
# core.autocrlf=input
# core.repositoryformatversion=0
# core.filemode=true
# core.bare=false
# core.logallrefupdates=true
# core.ignorecase=true
# core.precomposeunicode=true
```

- 별도로 설정한 값이 없기 때문에 기본 값이 들어가 있거나 아무 결과가 출력되지 않을 수 있음
- 처음 git을 설치할 때 설정을 하지 않았기 때문임

### 1) 전역(global) 설정
- git의 전역 설정은 git을 사용하는 모든 프로젝트에서 공통으로 적용되는 설정임
- git의 전역 설정은 사용자 이름, 이메일 주소, 에디터 등과 같은 정보를 포함함
- 이 설정은 사용자의 환경에 따라 달라질 수 있음

- git의 전역 설정을 변경하기 위해 아래 명령어를 입력함
```bash
git config --global {option} {value}
```
- 여기서 {option}은 변경하려는 설정의 잉름이고, {value}는 변경하려는 설정의 값임
- 일반적으로 사용자 이름과 이메일 주소를 전역으로 적용하기 위해 설정하는 편임

- 예를 들어 아래와 같이 작성할 수 있음
```bash
git config --global user.name global-user
git config --global user.email global-user@example.com
```
- 이 명령어는 사용자 이름을 "global-user"로, 이메일 주소를 "global-user@example.com"으로 설정함

- 잘 적용되었는지 확인을 위해 아래 명령어로 확인
```bash
git config --global --list
# user.name=global-user
# user.email=global-user@example.com
```
- 전역 설정이 정상적으로 적용된 것을 확인할 수 있음

### 2) 지역(local) 설정
- git의 로컬 설정은 사용자가 작업하는 특정 git 리포지토리(repository)에 대한 설정을 의미함
- 이 설정은 리포지토리에 대한 로컬 커밋 메세지, 작업자 정보 등을 저장하며, 이러한 정보는 다른 사용자와 협업하는 과정에서 유용함

- 로컬 설정은 git 디렉토리 내부의 `.git/config` 파일에 저장됨. 이 파일은 사용자가 명시적으로 수정할 수 있음
- 로컬 설정을 변경하는 방법은 아래와 같음
1. `cd` 명령어를 사용하여 git 리포지토리로 이동함
2. `git config` 명령어를 사용하여 로컬 설정을 변경함
```bash
git config {option} {value}
# 또는
git config --local {option} {value}
```

- 나는 로컬 설정을 하고 있다는 것을 명시적으로 표현하고 싶어서 2번째인 `--local` 속성을 자주 사용하는 편임
- 예시로 이렇게 작성할 수 있음
```bash
git config --local user.name local-user
git config --local user.email local-user@example.com
```
- 이 명령어는 현재 경로의 git의 리포지토리에 대한 설정으로 사용자 이름을 "local-user"로, 이메일 주소를 "local-user@example.com"으로 설정한다는 것을 의미함

- 잘 적용되었는지 확인을 위해 아래 명령어를 입력
```bash
git config --local --list
# core.repositoryformatversion=0
# core.filemode=true
# core.bare=false
# core.logallrefupdates=true
# core.ignorecase=true
# core.precomposeunicode=true
# remote.origin.url=https://github.com/{user-name}/{repo-name}.git
# remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
# branch.main.remote=origin
# branch.main.merge=refs/heads/main
# user.name=local-user
# user.email=local-user@example.com
```

- 위 설정 값을 보면 아래 부분에 user.name과 user.email이 잘 적용된 것을 알 수 있음

- 전역 설정으로 user.name과 user.email을 설정했었기 때문에 현재 설정 값이 덮어쓰기 된 것인지 우려할 수도 있음
- 아래 명령어로 현재 git 리포지토리에 설정된 값을 확인해보자
```bash
git config --list
# user.name=global-user                 # global
# user.email=global-user@example.com    # global
# core.repositoryformatversion=0
# core.filemode=true
# core.bare=false
# core.logallrefupdates=true
# core.ignorecase=true
# core.precomposeunicode=true
# remote.origin.url=https://github.com/{user-name}/{repo-name}.git
# remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
# branch.main.remote=origin
# branch.main.merge=refs/heads/main
# user.name=local-user                  # local
# user.email=local-user@example.com     # local
```

- 맨 위의 설정은 global 이며, 아래 설정은 local임을 알 수 있음