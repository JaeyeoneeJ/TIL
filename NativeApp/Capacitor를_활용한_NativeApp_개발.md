# 0. 개요

## 1) Capacitor와 Expo 차이
- `Capacitor`는 `WebApp`을 `Native App` 으로 감싸주는 도구로 생성된 결과물이 `WebView(WKWebView)`임    
- `Expo`는 `React Native` 개발을 편하게 도와주는 플랫폼으로 생성된 결과물이 `Native UI`이므로 `WebView`가 아님    
- 따라서 `WebView` 개발 시 `Capacitor`를 사용하는 것을 적극 권장    

## 2) Capacitor 사용 기준
- 기존 `WebApp`이 있고 웹 코드를 거의 대부분 재사용하게 되는 경우 → 빠른 `Native App`화 가능    
- Web 기능이 주력인 경우(Native의 기능을 거의 사용하지 않거나 커스텀하지 않는 경우)    

## 3) 앱 활용 방안
- Cordova 또는 Capacitor와 같은 Native 변환 Framework를 사용할 경우 iOS/AOS 결과물을 모두 출력할 수 있음    
- 단, 개발환경 세팅 및 iOS safe-area 처리 등 세부 설정은 각 Native 단에서 일부 커스텀 필요

# 1. 환경설정 및 설치

## 1) Shell App 생성
```bash
# Vite를 이용한 껍데기 앱 생성(CRA도 무방)
npm create vite@latest shell-app
# Framework: Vanilla(임의대로 생성해도 무방)
# Variant: JavaScript   (TS로 해도 됨)

cd shell-app
npm install
npm run dev
```

## 2) Capacitor 설치
```bash
npm install @capacitor/core @capacitor/cli
npx cap init
# App name: ShellApp
# App ID: com.test.shellapp
# Web asset directory: dist (CRA인 경우 build)
```

## 3) 테스트용 네이티브 기능 추가(Alert)
```bash
npm install @capacitor/dialog
npx cap sync # 싱크 맞춤
```

## 4) 웹 코드 수정
```jsx
// src/App.jsx

import { Dialog } from "@capacitor/dialog";
import { useEffect, useState } from "react";
import reactLogo from "./assets/react.svg";
import viteLogo from "/vite.svg";
import "./App.css";

function App() {
  useEffect(() => {
    const dialog = async () => {
      await Dialog.alert({
        title: "안내",
        message: "Vite 앱에서 네이티브 호출 성공",
      });
    };
    document.getElementById("btn").addEventListener("click", dialog);

    return () => {
      document.getElementById("btn").removeEventListener("click", dialog);
    };
  }, []);

  return (
    <>
      <button id="btn">
        네이티브 알림
      </button>
    </>
  );
}

export default App;
```

## 5) 웹 빌드 → 앱 반영
```bash
npm run build
npx cap sync
```

## 6) 플랫폼 추가
```bash
npx cap add ios
npx cap add android
# root 폴더 내 ios, android 폴더 생성 확인
```

## 7) 실행
```bash
# ios, xcode 실행
npx cap open ios

# aos, android studio 실행
npx cap open android
```

# 2. 개발환경 세팅

## 1) 라이브 리로드(앱에서 실시간 Vite 화면 반영)
```JSON
// capacitor.config.json
{
  "appId": "com.test.shellapp",
  "appName": "ShellApp",
  "webDir": "dist", // ShellApp이 빌드된 폴더 경로
  "server": {
    "url": "http://localhost:5173", // 또는 yourLocalIp:5173, 앱이 로드된 직후 즉시 리다이렉트할 주소
    "allowNavigation": ["localhost:5173", "yourLocalIp:5173"], // 허용할 IP 리스트
    "clearText": true // HTTP(Non Secure Protocol) 허용 여부, Android ERR_CLEARTEXT_NOT_PERMITTED 우회
  }
}
```

```bash
npm run dev
npx cap run ios -l --external
```

## 2) AOS `ERR_CLEARTEXT_NOT_PERMITTED` 에러 우회
### 2-1) WebApp 세팅
```bash
npm run dev # localhost만 뜸
#  VITE v7.3.1  ready in 131 ms
#
#  ➜  Local:   http://localhost:5173/
#  ➜  Network: use --host to expose
#  ➜  press h + enter to show help

npm run dev -- --host # localhost도 뜸
#  VITE v7.3.1  ready in 78 ms
#
#  ➜  Local:   http://localhost:5173/
#  ➜  Network: http://10.0.0.21:5173/
#  ➜  press h + enter to show help
```

```JSON
// capacitor.config.json
{
  "appId": "com.test.shellapp",
  "appName": "ShellApp",
  "webDir": "dist",
  "server": {
    "url": "http://10.0.0.21:5173", // android가 localhost를 캐치하지 못하므로 본인 로컬 IP
    "allowNavigation": ["10.0.0.21:5173"], // 허용할 IP 리스트
    "clearText": true // HTTP(Non Secure Protocol) 허용 여부, Android ERR_CLEARTEXT_NOT_PERMITTED 우회
  }
}
```

### 2-2) AOS Setting
- `network_security_config.xml` 생성(**경로 중요**)    
```xml
<!--android/app/src/main/res/xml/network_security_config.xml-->

<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <base-config cleartextTrafficPermitted="true">
        <trust-anchors>
            <certificates src="system" />
        </trust-anchors>
    </base-config>
</network-security-config>
```

- `AndroidManifest.xml` 수정
```xml
<!--android/app/src/main/res/AndroidManifest.xml-->

<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme"
        
        <!--/추가/-->
        android:networkSecurityConfig="@xml/network_security_config"
        android:usesCleartextTraffic="true"
        >
        ...
    </application>

    <!-- Permissions -->
    <uses-permission android:name="android.permission.INTERNET" />
</manifest>
```

## 3) iOS `safe area` 세팅
[iOS safe-area setting](./iOS_safe-area_setting.md) 참고