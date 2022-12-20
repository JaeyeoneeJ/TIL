# 😎 면접깡패 양성 프로그램, 떨면 뭐하니

  

&nbsp;

![KakaoTalk_20221212_152425796](https://user-images.githubusercontent.com/98001726/206976472-b9ef65a4-df2e-45ad-b346-95463e1032ec.png)

  

> 떨면 뭐하니는 IT개발 분야 모의 면접 서비스입니다.

> 프로젝트는 2022.11.04 ~ 2022.12.16 일간 진행되었습니다.

  

👉 [떨면뭐하니 바로 가기](https://itterview.com)

&nbsp;

  

<hr>

  

&nbsp;

  

## 📢 주요 기능

  

- 질문(텍스트)을 음성(보이스)으로 변환하여 AI 모의면접관이 직접 질문을 읽어주고,

각 질문의 소요 시간을 측정하여 실제 면접처럼 긴장되는 모의면접을 체험할 수 있어요.

- IT 개발자를 위한 면접 질문의 프리셋

(현재 React.js, Node.js, Spring 총 3가지 프리셋 적용)

- 내가 원하는 질문이 없어도 걱정하지 마세요!

내가 직접 입력한 질문으로 커스텀 모의 면접을 진행할 수 있어요.

  

&nbsp;

  

## 🌉 아키텍처


<img width="9719" alt="서비스 아키텍처" src="https://user-images.githubusercontent.com/98001726/206996021-1d6871a8-e616-4531-b233-e0c3c6f6c60e.png">

  

&nbsp;

  

## 🛠 사용한 기술

  

• 사용 언어 :

<img src="https://img.shields.io/badge/javascript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black">

  

• 서버 구축 :

  

<p>

<img src="https://img.shields.io/badge/node.js-339933?style=for-the-badge&logo=Node.js&logoColor=white">

<img src="https://img.shields.io/badge/Express-000000?style=for-the-badge&logo=Express&logoColor=white">

</p>

• 데이터 베이스 :

<p>

<img src="https://img.shields.io/badge/mongoDB-47A248?style=for-the-badge&logo=MongoDB&logoColor=white">

</p>

• 배포 :

<p>

<img src="https://img.shields.io/badge/AmazonEC2-FF9900?style=for-the-badge&logo=AmazonEC2&logoColor=white">

<img src="https://img.shields.io/badge/PM2-2B037A?style=for-the-badge&logo=PM2&logoColor=white">

<img src="https://img.shields.io/badge/nginx-009639?style=for-the-badge&logo=nginx&logoColor=white">

</p>

• 기타 :

<p>

<img src="https://img.shields.io/badge/Bcrypt-gray?style=for-the-badge&logoColor=white">

<img src="https://img.shields.io/badge/JWT-black?style=for-the-badge&logo=JSON Web Tokens&logoColor=white">

<img src="https://img.shields.io/badge/Nodemailer-FFE005?style=for-the-badge&logoColor=white">

<img src="https://img.shields.io/badge/NodeSchedule-40AEF0?style=for-the-badge&logoColor=white">

<img src="https://img.shields.io/badge/Jest-C21325?style=for-the-badge&logo=Jest&logoColor=white">

<img src="https://img.shields.io/badge/AmazonS3-569A31?style=for-the-badge&logo=AmazonS3&logoColor=white">

<img src="https://img.shields.io/badge/Swagger-85EA2D?style=for-the-badge&logo=Swagger&logoColor=black">

<img src="https://img.shields.io/badge/Sentry-362D59?style=for-the-badge&logo=Sentry&logoColor=white">

<img src="https://img.shields.io/badge/Github-181717?style=for-the-badge&logo=Github&logoColor=white">

<img src="https://img.shields.io/badge/GithubActions-2088FF?style=for-the-badge&logo=GithubActions&logoColor=white">

</p>

  

🌟 [더 자세한 내용 보러가기](https://fog-cyclone-297.notion.site/b101de37e067486399ae7bf539cd03f7)

  

&nbsp;

  

## ⚽ 트러블 슈팅

  

### 1) AccessToken, RefreshToken

  

| | |

| :---------: | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

| `문제 상황` | AccessToken만 사용했을 때 제 3자에게 토큰이 탈취되었을 경우<br> 보안에 취약하다는 피드백을 받음 |

| `선택지` | Access Token + Refresh Token |

| `의견 조율` | • Access Token의 유효 시간을 짧게 하여 공격 면적을 줄이고, <br> Refresh Token의 유효기간을 상대적으로 길게 설정해 사용자의 편의성도 <br>고려하면 좋을 것 같다고 의견이 나옴 <br>• Refresh Token 역시 탈취될 가능성이 있기 때문에 refresh 발급 시 DB에 저장 후 <br> 개인정보 탈취가 의심될 경우에 비밀번호 변경으로 유도한 뒤 변경 시 <br>DB에 저장된 Refresh를 삭제하게 하면 좋다는 의견 |

| `의견 결정` | 로그인 시 Access Token 과 Refresh Token 두 개를 발급, <br>Refresh Token 발급시 DB에 저장을 하고 개인정보 탈취 의심시 비밀번호 변경으로 유도하여 <br>DB에 있는 Refresh Token을 삭제함으로써 보안적으로 좀 더 강화하였다. |

  

&nbsp;

  

### 2) 로그 관리

  

| | |

| ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

| `문제 상황` | winston을 사용하여 로깅을 하고 있었으나 <br>일일이 찍어줘야 하는 불편함이 발생하여 다른 기술을 찾아보고자 함 |

| `선택지` | 1안) tracer + morgan <br> 2안) sentry + morgan |

| `의견 조율` | 1안의 경우 사용했을 때 winston과 별다른 차이점을 느끼지 못했고, <br>2안이 보다 시각적이고 간편하게 에러를 추적할 수 있었다. |

| `의견 결정` | 2안(sentry)을 사용했을 때 에러데이터를 쉽게 수집할 수 있고, <br>시각적으로 볼 수 있어 좋아보였다. <br>또 에러가 생겼을 때 슬랙으로 알람이 와서 간편하게 에러를 추적할 수 있고, <br>즉각적으로 대처가 가능하여 2안(sentry)을 채택하였다. |

  

&nbsp;

  

### 3) 테스트 코드 작성

  

| | |

| ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

| `문제 상황` | 테스트코드 없이 개발을 했을 때 API가 잘 작동하는지 <br>확인하려면 일일이 직접 입력값을 넣어야 했기 때문에 <br>리팩토링을 하거나 오류가 생겼을 때 수정을 하고 고치는 작업에 <br>시간이 많이 소요가 돼 팀의 생산성이 떨어지는 상황이 발생 |

| `선택지` | 1안) Mocha <br> 2안) Jest |

| `의견 조율` | 1안의 경우 다른 라이브러리의 설치 및 작업이 필요했고 <br>2안의 경우 1안보다 기능이 적지만 문서화가 잘되어 있고 가볍고 간단하다는 장점이 있다. |

| `의견 결정` | 시작하기 쉽고 빠르게 적용이 가능한 2안(jest)을 채택하였다. |

  

&nbsp;

  

### 4) CI/CD 적용

  

| | |

| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

| `문제 상황` | 협업 시 코드 배포를 해야하는 상황이 빈번히 발생하여 <br>배포 하는 데 많은 시간이 소요 되고 불필요하고 반복적인 과정을 줄이고 싶은 <br>필요를 느꼈다. |

| `선택지` | 1안) Jenkins <br> 2안) Github Actions (CI) + AWS CodeDeploy(CD) <br> 3안) Github Actions (CI/CD) |

| `의견 조율` | 1안: 사용자가 많지만 젠킨스만을 위한 인스턴스가 필요하고 세팅이 복잡하다 <br> 2안: aws 하나로 관리가 가능하지만 요금이 발생하는 단점이 있다. <br> 3안: 툴 설치 없이 바로 github repository에서 관리할 수 있고 설정이 쉽다 |

| `의견 결정` | 프로젝트 막바지에 적용을 하려다 보니 효율을 고려하지 않을 수 없었다. <br>그렇기 때문에 우선순위를 시간과 접근성에 두고 찾다보니 <br>3안(github action)이 찾던 조건에 가장 부합하여 채택하였다. |

  

&nbsp;

  

## 🚩 맡은 역할

  

| 이름 | 역할 |

| ------ | ------------------------------------------------------------------------- |

| 백지영 | 모의 면접, s3 multer, morgan |

| 김민섭 | 로그인, 회원정보 수정, 회원 탈퇴, 휴면 회원 관리, 테스트 코드 작성, ci/cd |

| 장민영 | 회원 가입, 스웨거, 부하테스트 작성 |

| 최예닮 | sentry, 테스트 코드 작성, 부하 테스트 작성 |