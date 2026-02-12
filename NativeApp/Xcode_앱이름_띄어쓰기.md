- 일반적으로 앱 명을 변경 시 아래 방법으로 변경
	- App → Targets → General → Identity 내 `Display Name` 변경
	- Info.plist 내 Bundle Display Name 변경
- 띄어쓰기가 적용되지 안되는 이유
	- OS 버전에 따른 자체 공백 제거
	- 앱 명이 긴 경우 자동 제거
- 해결방법
	- Info.plist를 Open As → Source Code로 열기
	- CFBundleDisplayName에 띄어쓰기 대신 **&#x2007;** 입력
```xml
<key>CFBundleDisplayName</key>
<string>App&#x2007;Name</string> <!-- App Name -->
```