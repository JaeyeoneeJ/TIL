# 환경
- Mac M3 Air
- RAM 16GB
- GPU 없음 (NVIDIA X)
- 로컬 
- Enact + FE 코드 분석

# 설치 방법
## 1. gpt4all App 설치
### 1) 공식 사이트 접속
[https://gpt4all.io](https://gpt4all.io)

### 2) Mac용 다운로드
- macOS (Apple Silicon) 선택
- `.dmg` 파일 다운로드

### 3) 설치
- DMG 열기
- **GPT4All.app → Applications**로 드래그 또는 installer 인 경우 실행
- 최초 실행 시 보안 경고 나오면:
    - 시스템 설정 → 개인정보 보호 및 보안 → “열기”

## 2. Llama 3 8B 모델 설치
### 1) GPT4All 실행
- Application 에서 앱 실행

### 2) model 설치
- 좌측 메뉴에서 Models 클릭
- add Model 선택
- Explore Models 에서 `Llama 3 8B Instruct` 다운로드

### 3) settings
- 좌측 메뉴에서 Settings 클릭
- Application 에서 `Default Model: Llama 3 8B Instruct` 설정
- Advanced 에서 `CPU Threads: 8` 설정
- Model 에서 Llama 3 8B Instruct 선택
- System Message 설정
	- AI의 고정 성격/규칙/역할로 모든 프롬프트보다 우선 순위가 높음
	- 목적에 맞게 사용하기 위해 설정하는 것이 좋음
	- 예시:
```
You are a senior frontend engineer.
Analyze the given project or codebase.
Focus on structure, performance, readability, and maintainability.
Point out concrete improvements with reasons.
Respond in Korean.
```

## 3. 모델 사용
### 1) 초기 세팅
- Chat 화면 이동
- model: Llama 3 8B Instruct 선택

### 2) 프로젝트 코드 넣는 법
- 좌측 Files / Attach
- Enact 프로젝트 폴더 선택
