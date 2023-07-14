## Podfile
- `Podfile`과 `Podfile.lock` 파일은 CocoaPods를 사용하여 iOS 프로젝트에서 종속성을 관리하는 데 사용되는 파일임
- `Podfile`: `Podfile`은 CocoaPods에서 사용하는 설정 파일로, 프로젝트에 포함된 라이브러리 및 프레임워크 종속성을 정의함. `Podfile`을 수정하여 프로젝트에 추가할 CocoaPods 종속성을 지정하고, 필요한 버전 또는 특정 설정을 설정할 수 있음. `Podfile`을 열어 종속성을 확인하고 필요한 경우 수정할 수 있음
- `Podfile.lock`: `Podfile.lock`은 CocoaPods를 통해 프로젝트에 설치된 종속성의 정확한 버전 및 상태를 기록하는 파일임. 이 파일은 프로젝트에 설치된 각 CocoaPods의 버전을 포함하며, 다른 개발자들이 동일한 종속성 버전을 사용할 수 있도록 보장함. 일반적으로 `Podfile.lock` 파일은 자동으로 생성되며, 직접 수정할 필요는 없음
- `Podfile`과 `Podfile.lock` 파일은 프로젝트 내부에서 CocoaPods를 구성하고 사용하는 데 중요한 역할을 함. CocoaPods를 사용하여 프로젝트에 종속성을 관리하는 경우, `Podfile`을 수정하여 필요한 종속성을 추가하거나 업데이트할 수 있음. 그러나 이 파일들을 수정할 때는 주의가 필요하며, 변경 사항을 정확히 이해하고 수행하는 것이 좋음

## CocoaPods
### 1) CocoaPods 설치
- 터미널에서 다음 명령어를 실행하여 CocoaPods를 설치함
- 이때, 터미널을 종료한 뒤 다시 열어 실행해야 환경 변수가 올바르게 설정됨
```ruby
sudo gem install cocoapods
```

### 2) CocoaPods 업데이트
- CocoaPods를 최신 버전으로 업데이트하려면 다음 명령어를 실행
```ruby
sudo gem update cocoapods
```

### 3) 환경 변수 확인
- 터미널에서 `echo $PATH` 명령어를 실행하여 환경 변수를 확인
- CocoaPods가 올바르게 설치되었는지 확인하고 경로에 포함되어 있는지 확인
- 예를 들어, `/usr/local/bin` 또는 `/usr/local/sbin`에 `pod` 실행 파일이 있는지 확인
- 만약 경로에 포함되어 있지 않다면, 환경 변수를 수동으로 추가해야 함
- 다음 예시는 `.zshrc` 파일에 경로를 추가하는 방법임. 터미널에서 다음 명령어를 실행
```ruby
echo 'export PATH="/usr/local/bin:$PATH"' >> ~/.zshrc
```

### 4) 터미널 재시작
- 변경 사항이 적용되기 위해 터미널을 재시작
- 위 단계를 수행한 후에는 `pod` 명령어를 사용하여 CocoaPods를 실행할 수 있어야 함
- 이제 `pod update` 명령어를 실행하여 프로젝트의 CocoaPods 종속성을 업데이트할 수 있음

### 5) 설치 확인
```ruby
pod --version
# 1.12.1
```
- 위와 같이 뜨면 CocoaPods이 정상적으로 설치된 것임

