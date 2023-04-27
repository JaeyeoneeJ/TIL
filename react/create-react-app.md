## create-react-app이란
- create-react-app은 Facebook에서 만든 React 애플리케이션을 쉽게 시작할 수 있는 도구임
- React는 사용하기 쉽고 유연한 프론트엔드 JavaScript 라이브러리임
- create-react-app은 React 개발을 시작할 때 프로젝트 구조, 설정 및 빌드 프로세스를 관리하기 쉽도록 해줌

- create-react-app을 사용하면 명령줄 인터페이스를 통해 새로운 React 프로젝트를 쉽게 만들 수 있음
- 이 도구를 사용하면 Webpack, Babel 및 기타 필요한 라이브러리를 포함하여 React 애플리케이션을 구축하기 위해 필요한 모든 도구를 빠르게 설정할 수 있음
- 또한, 개발 중에 빠른 리로딩을 제공하여 빠른 피드백 루프를 통해 React 애플리케이션을 빌드하고 디버그할 수 있음

## create-react-app 실행 방법
- 먼저 Node.js를 설치해야 함. Node.js는 JavaScript 런타임으로, create-react-app을 실행하는데 필요함
- 명령 프롬프트 또는 터미널을 열고 다음 명령어를 실행하여 create-react-app을 전역으로 설치함
```bash
npm install -g create-react-app
```

- create-react-app을 사용하여 새 React 프로젝트를 만듦
```bash
npx create-react-app my-app
# 또는
npx create-react-app .
```

1. `npx create-react-app my-app`
- `npx create-react-app my-app`은 현재 폴더 경로에 `my-app`이라는 폴더를 만들어서 그 안에 react-app을 설치하고 프로젝트의 이름을 설치 경로이 `my-app`으로 정함
- 이 경우,  `cd my-app`으로 프로젝트가 설치된 경로로 이동하여 `npm start`를 하여야 함

2. `npx create-react-app .`
- `npx create-react-app .`은 현재 폴더에 react-app을 설치하고 프로젝트의 이름을 현재 폴더 이름으로 정함
- 이 경우, 바로 `npm start` 명령을 실행할 수 있음
- 나의 경우, git clone과 함께 사용하는 경우가 많고, git 프로젝트의 이름이 react 프로젝트의 이름인 경우가 많기 때문에 `npx create-react-app .`를 더 자주 사용함

## npm 말고 npx를 쓰는 이유
- `npm install -g create-react-app`을 이용하여 전역으로 설치하는 방법도 있음
- `npm -g` 옵션을 통해 다운을 받으면 매 프로젝트마다 모듈을 설치해주지 않아도 되고 사용하는 pc 내부에 모듈을 설치해서 프로젝트를 할 때마다 같은 모듈을 가지고 오기 때문에 사용성이 좋을 수 있음
- 하지만 모듈을 재사용하다보면 여러 프로젝트의 환경변수를 맞춰줄 때 문제가 생길 수 있음

- 근본적으로 `create-react-app`은 React 앱을 생성하는 도구이며, 이 도구를 실행하기 위해 로컬 컴퓨터에 설치해야 함. 이 때, `npm` 패키지 매니저를 이용해서 전역 설치할 수도 있고, `npx` 명령어를 이용해서 일회성으로 실행할 수도 있음
- `npm` 패키지 매니저를 이용해서 전역으로 설치할 경우, 해당 도구가 로컬 컴퓨터에 영구적으로 설치되므로, 이후에 `create-react-app`을 실행할 때마다 해당 도구를 호출할 필요 없이 직접 실행할 수 있음
- 하지만 이 경우, 설치한 도구를 삭제하거나 업데이트할 때 등의 관리가 필요하며, 설치한 도구의 버전과 일치하지 않는 프로젝트에서는 문제가 발생할 수 있음

- 반면에 `npx` 명령어를 이용해서 일회성으로 실행할 경우, 해당 도구를 로컬에 설치할 필요가 없음
- `npx` 명령어는 해당 패키지가 로컬에 설치되어 있지 않더라도, 패키지를 일시적으로 다운로드하여 실행할 수 있음
- `npm install -g create-react-app` 명령어를 실행하지 않아도 `npx create-react-app my-app` 명령어를 실행하여 새로운 React 앱을 만들 수 있음
- 따라서, 프로젝트마다 필요한 버전의 `create-react-app`을 사용할 수 있으며, 도구의 버전 업그레이드나 삭제에 대한 관리도 필요하지 않음

- 결론적으로, `npx` 명령어를 이용하는 것이 더욱 효율적이며, 프로젝트마다 필요한 버전의 도구를 자유롭게 사용할 수 있다는 장점이 있음