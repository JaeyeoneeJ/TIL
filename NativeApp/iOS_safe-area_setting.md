# 1. Full View 사용하기
## 1) WebApp Setting
```html
<meta
  name='viewport'
  content="
    width=device-width,
    initial-scale=1.0,
    maximum-scale=1.0,
    user-scalable=no,
    viewport-fit=cover // cover 처리 시 ios safe-area가 풀 스크린으로 잡힘
  "
/>
```

```css
body {
  padding-top: env(safe-area-inset-top);
}

.header {
  position: fixed;
  top: 0;
  padding-top: env(safe-area-inset-top);
}
```

## 2) iOS Native App Setting
### 2-1) MainViewController.swift
```swift
// App/App/MainViewController.swift

import UIKit
import Capacitor

@objc(MainViewController)
class MainViewController: CAPBridgeViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        guard let webView = self.bridge?.webView else { return }

        // 기존 WebView에 safeAreaLayoutGuide constraint 적용
        webView.translatesAutoresizingMaskIntoConstraints = false

        NSLayoutConstraint.activate([
            webView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor),
            webView.bottomAnchor.constraint(equalTo: view.safeAreaLayoutGuide.bottomAnchor),
            webView.leadingAnchor.constraint(equalTo: view.safeAreaLayoutGuide.leadingAnchor),
            webView.trailingAnchor.constraint(equalTo: view.safeAreaLayoutGuide.trailingAnchor)
        ])

        webView.scrollView.contentInsetAdjustmentBehavior = .never // safe-area를 iOS에서 자동처리하지 않음
        // webView.scrollView.contentInsetAdjustmentBehavior = .automatic // safe-area를 iOS에서 자동으로 처리
    }
  }
```

### 2-2) SceneDelegate.swift
```swift
// App/App/SceneDelegate.swift

import UIKit
import Capacitor

class SceneDelegate: UIResponder, UIWindowSceneDelegate {

    var window: UIWindow?

    func scene(
        _ scene: UIScene,
        willConnectTo session: UISceneSession,
        options connectionOptions: UIScene.ConnectionOptions
    ) {
        guard let windowScene = (scene as? UIWindowScene) else { return }

        let window = UIWindow(windowScene: windowScene)
        window.rootViewController = MainViewController() // MainViewController 연결
        self.window = window
        window.makeKeyAndVisible()
    }
}
```

### 2-3) Info.plist
- `UIApplicationSceneManifest`가 있어야 iOS가 SceneDelegate를 Scene에 연결함
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    // 아래 내용 추가
    <key>UIApplicationSceneManifest</key>
    <dict>
      <key>UIApplicationSupportsMultipleScenes</key>
      <false/>
      <key>UISceneConfigurations</key>
      <dict>
        <key>UIWindowSceneSessionRoleApplication</key>
        <array>
          <dict>
            <key>UISceneConfigurationName</key>
            <string>Default Configuration</string>
            <key>UISceneDelegateClassName</key>
            <string>$(PRODUCT_MODULE_NAME).SceneDelegate</string>
          </dict>
        </array>
      </dict>
    </dict>
    ...
</dict>
</plist>
```

## 3) 적용 결과

| **iOS 미적용**                                                                                                                                                                                                                                                                                                                                                                                                                              | **iOS: .automatic + CSS**                                                                                                                                                                                                                                                                                                                                                                                                                                      | **iOS: .never + CSS**                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <img width="250" alt="Image" src="https://github-production-user-asset-6210df.s3.amazonaws.com/77138259/544085986-66e10930-fadd-4920-b26f-05226d99d8ec.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAVCODYLSA53PQK4ZA%2F20260203%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20260203T021602Z&X-Amz-Expires=300&X-Amz-Signature=81963e6946a70f0d9051a52542b126ace0337e2ebafd274ba4b968a289722d35&X-Amz-SignedHeaders=host" /> | <img width="250" alt="CSS 적용, iOS .automatic 인 경우" src="https://github-production-user-asset-6210df.s3.amazonaws.com/77138259/544085660-df3a4fa3-4d49-4b80-979f-83a5dcd9500a.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAVCODYLSA53PQK4ZA%2F20260203%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20260203T021435Z&X-Amz-Expires=300&X-Amz-Signature=ccd1d4f3577eeda9ff712c1155d69071ed42e0307d877cd9581decc71607e03d&X-Amz-SignedHeaders=host" /> | <img width="250" alt="css 적용, iOS .never인 경우" src="https://github-production-user-asset-6210df.s3.amazonaws.com/77138259/544084724-faa076f0-1a9d-42c8-92b6-572c1f396ad1.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAVCODYLSA53PQK4ZA%2F20260203%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20260203T021247Z&X-Amz-Expires=300&X-Amz-Signature=b3bca0521c7770c1fbfbb3716eefead0ab370ca6d34426da5e59bf7f18afc55e&X-Amz-SignedHeaders=host" /> |

# # 번외: safe-area 없는 네모 영역만 사용하기
```swift
import UIKit
import Capacitor

@objc(MainViewController)
class MainViewController: CAPBridgeViewController {
    private var didApplySafeArea = false

    override func viewDidLayoutSubviews() {
        super.viewDidLayoutSubviews()

        guard
            let webView = self.webView,
            !didApplySafeArea
        else { return }

        webView.scrollView.contentInsetAdjustmentBehavior = .never

        let safeFrame = view.safeAreaLayoutGuide.layoutFrame
        webView.frame = safeFrame

        didApplySafeArea = true
    }
}
```

<img width="250" alt="Image" src="https://github-production-user-asset-6210df.s3.amazonaws.com/77138259/544086235-068f2130-916f-4718-b7ef-2149e9446b34.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAVCODYLSA53PQK4ZA%2F20260203%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20260203T021632Z&X-Amz-Expires=300&X-Amz-Signature=d2579fd39d1dcf34cf1af0e976ce2d2f2eea44ea6a4c2c8eb3b76fb9c5c7eb72&X-Amz-SignedHeaders=host" />
