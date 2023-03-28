## URL
https://start.spring.io/

## Spring Initializr의 이점
- 스프링 부트에서 프로젝트를 시작할 때, 통합 개발 환경(IDE)에 의존하지 않는 프로젝트를 만드는 방법을 원한다면 Spring Initializr에서 프로젝트를 만들고 사용 중인 통합 개발 환경에서 해당 프로젝트를 가져와서 사용

## 사용법

<img src="https://user-images.githubusercontent.com/77138259/228220191-522b6eda-3c24-4cff-9ef2-ae519ec4cb53.png" alt="spring initializr 화면 구성" />

- 항목 설명
| **입력/선택 항목** | **개요** |
| :--- | :--- |
| Project | 빌드 도구 선택. 예제에서는 Gradle-Groovy 선택 |
| Language | 사용할 프로그래밍 언어 선택. Java, Kotlin, Groovy 중 선택 가능 |
| Spring Boot | 사용할 스프링 부트 버전 선택 |
| Project Metadata(Group) | 패키지명 입력 |
| Project Metadata(Artifact) | 프로젝트(응용 프로그램) 이름 입력 |
| Project Metadata(Name) | Artifact와 동일한 이름 사용 추천 |
| Project Metadata(Description) | 프로젝트 설명 입력 |
| Project Metadata(Package Name) | 프로젝트의 패키지 이름. 일반적으로 Group에 지정한 패키지와 Artifact에 지정된 이름으로 구성 |
| Dependencies | 사용할 프레임워크나 라이브러리를 선택함. 키워드를 입력하여 원하는 프레임워크나 라이브러리를 선택하고 추가할 수 있음 |

- Generate를 클릭하면 프로젝트가 zip 형태로 압축된 파일로 로컬 PC에 다운로드 됨
- 다운로드한 zip 파일의 압축을 푼 다음 IntelliJ IDEA에서 '파일 -> 열기'로 불러오면 프로젝트가 임포트 됨
- 통합 개발 환경(IDE)에 의존하지 않는 스프링 부트를 사용하여 프로젝트를 만드는 방법을 원한다면 Spring Initializr에서 프로젝트를 만드는 방법을 추천함