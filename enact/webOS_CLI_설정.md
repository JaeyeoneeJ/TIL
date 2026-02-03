## 1. node
- node: v16.17.0
  (위 환경에서 설치했습니다.)

## 2. Enact Client
- enact 명령어를 사용하기 위해 아래 설치 필수
```bash
npm install -g @enact/cli@6.0.4
# node v16.17.0 이상 권장
```

## 3. ares setting
### 1) CLI 파일 다운로드
- [https://webostv.developer.lge.com/develop/tools/cli-installation](https://webostv.developer.lge.com/develop/tools/cli-installation) 접속 후, 본인 OS에 맞는 CLI 파일 다운로드

### 2) 환경변수 설정
```bash
# macOS || linux
echo $LG_WEBOS_TV_SDK_HOME

# window
echo %LG_WEBOS_TV_SDK_HOME%
```
- 환경변수 `LG_WEBOS_TV_SDK_HOME`가 이미 존재하는 경우, 해당 경로가 출력됨
- 환경변수가 구성되어 있지 않은 경우, 아래 지침을 이어서 진행
1. `webOS_TV_SDK` 폴더를 원하는 경로에 만든다.
2. `webOS_TV_SDK` 폴더 안에 위에서 받은 CLI 파일의 압축을 푼다.
3. linux or macOS의 경우
- `/etc/profile`에 다음을 추가한다. `YOUR_PATH`를 `webOS_TV_SDK` 경로로 바꾼다.

```bash
# Setting the LG_WEBOS_TV_SDK_HOME variable to the parent directory of CLI
export LG_WEBOS_TV_SDK_HOME="/YOUR_PATH/webOS_TV_SDK"

if [ -d "$LG_WEBOS_TV_SDK_HOME/CLI/bin" ]; then
  # Setting the WEBOS_CLI_TV variable to the bin directory of CLI
  export WEBOS_CLI_TV="$LG_WEBOS_TV_SDK_HOME/CLI/bin"
  # Adding the bin directory of CLI to the PATH variable
  export PATH="$PATH:$WEBOS_CLI_TV"
fi
```
- 아래를 실행하여 변경 사항을 저장한다.
```bash
source /etc/profile
```

4. winOS의 경우
- 다음과 같이 환경변수를 구성한다. `YOUR_PATH`를 `webOS_TV_SDK` 경로로 바꾼다.
```bash
// Setting the LG_WEBOS_TV_SDK_HOME variable to the parent directory of CLI
> setx /m LG_WEBOS_TV_SDK_HOME "C:\YOUR_PATH\webOS_TV_SDK"

// Setting the WEBOS_CLI_TV variable to the bin directory of CLI
> setx /m WEBOS_CLI_TV "%LG_WEBOS_TV_SDK_HOME%\CLI\bin"

// Adding the bin directory of CLI to the PATH variable
  // on CMD
> setx /m PATH "%PATH%;%WEBOS_CLI_TV%"
  // or on PowerShell
> setx /m PATH "$Env:PATH;$Env:WEBOS_CLI_TV”
```
- 변경 사항을 적용하려면 명령 창이나 PC를 다시 시작한다.

### 3) 설치 확인
- 성공적으로 설치되면 webOS TV CLI 버전이 표시된다.
```bash
ares -V
# webOS TV CLI Version: 1.12.4-j27
```


## 4. TV 연결
### 0) 초기 설정
1. [LG 개발자 사이트](https://us.lgaccount.com/login) 계정 생성
2. 개발자 모드 앱 설치(TV)
- 생성한 계정으로 로그인
- LG Content Store에서 "Developer Mode" 검색 후 설치

### 1) TV 개발자 모드 활성화
- Developer Mode App 실행
- Dev Mode Status => On 전환 후 TV 재부팅

### 2) TV 등록
> ares 명령어가 실행 가능해야 함

1. TV 추가
```bash
ares-setup-device -a [장치 이름] -i "{'host':'[ip]', 'port':'9922', 'username':'developer'}"
# [장치 이름]: 임의로 만드는 사람이 생성
# [ip]: Developer Mode App 에서 확인했던 ip
```

> 장치 이름 IP 변경: `ares-setup-device -m [장치 이름] -i "{'host':'[ip]', 'port':'9922', 'username':'developer'}"`
> TV가 아닌 emulator인 경우, `'port': '6622'`

2. 추가한 장치 목록 확인
```bash
ares-setup-device –list
```

3. 키 파일 가져오기
> Developer Mode App 에서 Key Server => On(필수)
```bash
ares-novacom --device [장치 이름] --getkey
# SSH Private Key: /Users/username/.ssh/[장치 이름]_webos
# input passphrase [default: webos]: Developer Mode app에서 확인한 Passphrase 입력
```

4. TV 연결 확인
```bash
ares-device-info --device [장치 이름]
# modelName : OLED65####
# sdkVersion : #.#.#
# firmwareVersion : ##.##.##
# boardType : ######
# otaId : #######
```

5. TV default setting
```bash
ares-setup-device
```
- ares-setup-device 후 set default > 사용하려는 장치로 변경, 저장
- 이 방법으로 진행하면 장치 이름 계속 입력할 필요 없음