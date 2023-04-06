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

