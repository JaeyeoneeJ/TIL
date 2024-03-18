> 아래 테스트 내용은 저의 github에 수록되어 있습니다. 테스트 앱의 확인이 필요한 경우, [enact-test](https://github.com/JaeyeoneeJ/enact-test)를 통해 프로젝트를 확인할 수 있습니다.

 > 이 프로젝트를 실행하기 위해서는 `enact-cli`가 반드시 필요합니다.
 > 또한 TV 또는 emulator에 설치하기 위해서는 webOS_CLI가 반드시 필요합니다.
 > 
 > 설치 방법은 [webOS_CLI 설정](./webOS_CLI_설정.md)을 참고하시기 바랍니다.


## 1. 프로젝트 실행 방법

### 1) `npm run serve`
- 위 명령어를 통해 개발 모드의 앱을 실행할 수 있습니다.
- 기본 포트 주소는 8080이며, 빌드 스크립트를 변경하여 포트를 변경할 수 있습니다.

```bash
# package.json
"scripts": {
  # 기존
  "serve": "enact serve",

  # 3000번 포트로 실행
  "serve": "enact serve -p=3000",
  ...
}
```

### 2) `npm run pack` || `npm run pack-p`
- 위 명령어를 통해 개발 모드의 앱을 빌드할 수 있습니다.
- 빌드 폴더의 경로는 `/dist` 입니다. 이를 변경하기 위해서는 webpack 설정을 변경해야만 합니다.(eject 필요)
- `enact pack`은 일반 빌드입니다.
- `enact pack -p`는 production 빌드입니다.
- 만일 당신의 글로벌 환경에 `http-server` 등이 설치되어 있는 경우, 빌드 후 `npx http-server ./dist`로 빌드된 앱을 확인할 수 있습니다. (또는 `npx http-server ./dist -p 9090`으로 포트 번호 변경 가능)

### 3) `enact eject`
- 위 명령어를 사용하면 webpack 설정 등 enact에서 기본적으로 실행하는 명령 옵션이 모두 꺼내지므로 사용에 주의하시기 바랍니다.
- 만일 당신이 webpack 설정 등으로 빌드 파일의 경로를 변경하거나 webpack plugin 추가를 원한다면 eject하여 webpack 설정을 변경하여야만 합니다.
  (현재 enact 외부에서 webpack 설정을 변경하는 방법은 없습니다.)

> `enact eject`에 대한 내용은 [Enact에서 service-worker 사용](./Enact에서_service-worker_사용.md) 문서를 확인하시기 바랍니다.


## 2. TV(또는 에뮬레이터) 설치

> ares 명령어가 실행 가능해야 함

### 1) 빌드 앱
- 아래 순서를 통해 TV에 앱을 설치할 수 있습니다.
```bash
# 1. App build
npm run pack
# 또는
npm run pack-p

# 2. App packaging
ares-package ./dist

# 3. TV install
ares-install [생성된 파일].ipk # default에 설치
# 또는
ares-install --device [장치 이름] [생성된 파일].ipk
```

2. hostedApp 사용
- TV에서 설치된 앱 실행
- 설치된 앱의 development tool > console에서 다음 명령어를 통해 이동

```js
// 당신의 작업 내용을 실시간으로 확인하고 싶을 때,
location.href = "http://192.000.0.00:8080"; // ip:포트번호
```
