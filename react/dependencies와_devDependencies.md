- react로 프로젝트를 구성할 때 package.json 파일이 자동으로 생성되어 기본 dependencies 값을 할당한다.
- 일반적으로 `create-react-app(CRA)`를 사용하여 리액트 페이지를 생성할 때, 기본적으로 `devDependencies` 목록을 보여주지 않는다. 이는 CRA가 개발 환경을 설정하는 데 필요한 모든 패키지를 자동으로 관리하기 때문이다.
- 따라서 일반적으로 사용자가 직접 개발 환경 패키지를 `devDependencies`에 추가할 필요가 없다.

- CRA는 프로젝트의 초기 설정과 개발 환경을 관리하기 위해 다양한 패키지와 스크립트를 사용합니다. 이러한 패키지들은 CRA의 내부 동작을 지원하며, 이들은 CRA 자체의 의존성으로 설치되므로 `devDependencies`에 직접 추가할 필요가 없다.

- 만약 CRA를 사용하여 프로젝트를 생성한 후에 추가적인 개발 환경 패키지를 설치하려는 경우, 이 패키지들은 자동으로 `dependencies` 또는 `devDependencies` 중 적절한 위치에 추가될 것이다.
- 단, 이러한 패키지들이 개발 과정에서만 필요하다면 CRA는 이를 자동으로 `devDependencies`에 추가할 것이다.

- 요약하자면, CRA 프로젝트에서는 CRA 자체가 개발 환경을 관리하는 데 필요한 모든 패키지를 이미 관리하고 있기 때문에, 일반적으로 사용자가 직접 `devDependencies`에 패키지를 추가할 필요는 없으므로 CRA의 기본 동작을 따르는 것이 좋다.

