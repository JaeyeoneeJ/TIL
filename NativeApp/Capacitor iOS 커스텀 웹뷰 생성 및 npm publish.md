# 0. 개요
- 기존 Capacitor inappbrowser가 커스텀에 문제가 있어 해당 기능을 커스텀 웹뷰로 구현하는 것에 중점을 둠
- 구현할 커스텀 웹뷰는 `backButton`의 동작에 중점을 둠
- WebView history가 있는 상태에서 `backButton` 동작 시 스와이프와 같은 뒤로가기 실행
- 없는 Root 상태에서 `backButton` 동작 시 WebView 종료 안내 `Toast`를 띄우고 2초 내 `backButton` 재동작 시 웹뷰 종료 로직 구현

# 1. Capacitor Custom Plugin 등록
- JS로 인자를 커스텀하기 위해 Custom Plugin을 등록(닫기 버튼 텍스트 등)

## 1) Capacitor 플러그인 생성
```bash
npx @capacitor/cli plugin:generate
# Plugin name: CustomWebView
# Plugin id: custom-webview
# Description: Custom native webview with header
# ? What directory should be used for your plugin? # 엔터 치기
# What should be the Package ID for your plugin? # com.test.lgsubscribenative.webview
# What is the repository URL for your plugin? # https://github.com/your-org/lg-subscribe-webview 추후 수정 가능

# root 내 custom-webview 폴더 생성 확인
```

## 2) Android 관련 옵션 제거
```
custom-webview/
├─ src/
│  ├─ definitions.ts
│  ├─ index.ts
│  └─ web.ts
├─ ios/
│  └─ Plugin/
│     └─ CustomWebviewPlugin.swift
├─ android/ # 제거
│  └─ src/main/java/...
├─ package.json # 내부 스크립트 등 Android 관련 옵션 제거
```

## 3) iOS 코드 수정
- enum 지정
```swift
// custom-webview/ios/Sources/CustomWebViewPlugin/CustomWebViewTypes.swift

import Foundation

@objc public enum ToolbarPosition: Int {
    case top
    case bottom
}
```

- 호출 함수부 구현
```swift
// custom-webview/ios/Sources/CustomWebViewPlugin/CustomWebView.swift

import UIKit
import WebKit

final class CustomWebViewController: UIViewController, WKNavigationDelegate {

    private let url: URL
    private let closeButtonText: String?
    private let closeWarningText: String
    private let webView = WKWebView()
    private var backButton: UIButton!
    private let toolbarPosition: ToolbarPosition
    private let toolbarHeight: CGFloat = 48

    // gradient view
    private let gradientView = UIView()

    init(url: URL, closeButtonText: String? = nil, closeWarningText: String? = nil, toolbarPosition: ToolbarPosition = .top) {
        self.url = url
        self.closeButtonText = closeButtonText
        self.closeWarningText = closeWarningText ?? "Tap again to close the WebView."
        self.toolbarPosition = toolbarPosition
        super.init(nibName: nil, bundle: nil)
    }

    required init?(coder: NSCoder) {
        fatalError()
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .white

        setupHeader()
        setupWebView()
        setupGradient()
        load()
    }

    // MARK: - Header

    private let header = UIView()

    private func setupHeader() {
        header.backgroundColor = UIColor.systemBackground.withAlphaComponent(0.95)
        header.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(header)

        let back = UIButton(type: .system)
        back.setImage(UIImage(systemName: "chevron.left"), for: .normal)
        back.addTarget(self, action: #selector(backTapped), for: .touchUpInside)
        back.translatesAutoresizingMaskIntoConstraints = false
        header.addSubview(back)
        self.backButton = back

        let close = UIButton(type: .system)
        if let text = closeButtonText, !text.isEmpty {
            close.setTitle(text, for: .normal)
        } else {
            close.setImage(UIImage(systemName: "xmark"), for: .normal)
        }
        close.addTarget(self, action: #selector(closeTapped), for: .touchUpInside)
        close.translatesAutoresizingMaskIntoConstraints = false
        header.addSubview(close)

        NSLayoutConstraint.activate([
            back.leadingAnchor.constraint(equalTo: header.leadingAnchor, constant: 8),
            back.centerYAnchor.constraint(equalTo: header.centerYAnchor),
            back.widthAnchor.constraint(equalToConstant: 44),
            back.heightAnchor.constraint(equalToConstant: 44),

            close.trailingAnchor.constraint(equalTo: header.trailingAnchor, constant: -8),
            close.centerYAnchor.constraint(equalTo: header.centerYAnchor),
            close.widthAnchor.constraint(equalToConstant: 44),
            close.heightAnchor.constraint(equalToConstant: 44)
        ])

        if toolbarPosition == .top {
            NSLayoutConstraint.activate([
                header.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor)
            ])
        } else {
            NSLayoutConstraint.activate([
                header.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -8)
            ])
        }

        NSLayoutConstraint.activate([
            header.leadingAnchor.constraint(equalTo: view.leadingAnchor),
            header.trailingAnchor.constraint(equalTo: view.trailingAnchor),
            header.heightAnchor.constraint(equalToConstant: toolbarHeight)
        ])
    }

    // MARK: - WebView

    private func setupWebView() {
        webView.translatesAutoresizingMaskIntoConstraints = false
        webView.allowsBackForwardNavigationGestures = true
        webView.navigationDelegate = self
        view.addSubview(webView)

        if toolbarPosition == .top {
            NSLayoutConstraint.activate([
                webView.topAnchor.constraint(equalTo: header.bottomAnchor),
                webView.bottomAnchor.constraint(equalTo: view.bottomAnchor)
            ])
        } else {
            NSLayoutConstraint.activate([
                webView.topAnchor.constraint(equalTo: view.topAnchor),
                webView.bottomAnchor.constraint(equalTo: header.topAnchor)
            ])
        }

        NSLayoutConstraint.activate([
            webView.leadingAnchor.constraint(equalTo: view.leadingAnchor),
            webView.trailingAnchor.constraint(equalTo: view.trailingAnchor)
        ])
    }

    // MARK: - Gradient Shadow
    private func setupGradient() {
        gradientView.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(gradientView)

        let gradientLayer = CAGradientLayer()
        gradientLayer.colors = [
            UIColor.black.withAlphaComponent(0.05).cgColor,
            UIColor.clear.cgColor
        ]
        gradientLayer.cornerRadius = 0

        let gradientHeight: CGFloat = 6

        if toolbarPosition == .top {
            gradientView.layer.addSublayer(gradientLayer)
            NSLayoutConstraint.activate([
                gradientView.topAnchor.constraint(equalTo: header.bottomAnchor),
                gradientView.leadingAnchor.constraint(equalTo: view.leadingAnchor),
                gradientView.trailingAnchor.constraint(equalTo: view.trailingAnchor),
                gradientView.heightAnchor.constraint(equalToConstant: gradientHeight)
            ])
            gradientLayer.startPoint = CGPoint(x: 0, y: 0)
            gradientLayer.endPoint = CGPoint(x: 0, y: 1)
        } else {
            gradientView.layer.addSublayer(gradientLayer)
            NSLayoutConstraint.activate([
                gradientView.bottomAnchor.constraint(equalTo: header.topAnchor),
                gradientView.leadingAnchor.constraint(equalTo: view.leadingAnchor),
                gradientView.trailingAnchor.constraint(equalTo: view.trailingAnchor),
                gradientView.heightAnchor.constraint(equalToConstant: gradientHeight)
            ])
            gradientLayer.startPoint = CGPoint(x: 0, y: 1)
            gradientLayer.endPoint = CGPoint(x: 0, y: 0)
        }

        gradientView.layoutIfNeeded()
        gradientLayer.frame = gradientView.bounds
    }

    // MARK: - Load URL
    private func load() {
        webView.load(URLRequest(url: url))
    }

    @objc private func closeTapped() {
        dismiss(animated: true)
    }

    // MARK: - Toast
    private var toastView: UIView?
    private var lastBackPressedAt: Date?

    private func showToast(_ message: String) {
        toastView?.removeFromSuperview()

        let label = UILabel()
        label.text = message
        label.textColor = .white
        label.font = UIFont.preferredFont(forTextStyle: .callout)
        label.textAlignment = .center
        label.numberOfLines = 0

        let container = UIView()
        container.backgroundColor = UIColor.black.withAlphaComponent(0.7)
        container.layer.cornerRadius = 10
        container.clipsToBounds = true
        container.alpha = 0

        label.translatesAutoresizingMaskIntoConstraints = false
        container.translatesAutoresizingMaskIntoConstraints = false

        container.addSubview(label)
        view.addSubview(container)
        toastView = container

        NSLayoutConstraint.activate([
            label.topAnchor.constraint(equalTo: container.topAnchor, constant: 10),
            label.bottomAnchor.constraint(equalTo: container.bottomAnchor, constant: -10),
            label.leadingAnchor.constraint(equalTo: container.leadingAnchor, constant: 14),
            label.trailingAnchor.constraint(equalTo: container.trailingAnchor, constant: -14),

            container.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            container.bottomAnchor.constraint(equalTo: view.safeAreaLayoutGuide.bottomAnchor, constant: -20),
            container.widthAnchor.constraint(lessThanOrEqualToConstant: 320)
        ])

        UIView.animate(withDuration: 0.25) {
            container.alpha = 1
        }

        DispatchQueue.main.asyncAfter(deadline: .now() + 1.8) {
            UIView.animate(withDuration: 0.25, animations: {
                container.alpha = 0
            }) { _ in
                container.removeFromSuperview()
            }
        }
    }

    // MARK: - Back button
    @objc private func backTapped() {
        if webView.canGoBack {
            webView.goBack()
            return
        }

        let now = Date()
        if let last = lastBackPressedAt, now.timeIntervalSince(last) < 2 {
            dismiss(animated: true)
        } else {
            lastBackPressedAt = now
            showToast(closeWarningText)
        }
    }
}
```

- JS 연결부 구현
```swift
// custom-webview/ios/Sources/CustomWebViewPlugin/CustomWebViewPlugin.swift

import Foundation
import Capacitor

@objc(CustomWebViewPlugin)
public class CustomWebViewPlugin: CAPPlugin, CAPBridgedPlugin {

    public let identifier = "CustomWebViewPlugin"
    public let jsName = "CustomWebView"

    public let pluginMethods: [CAPPluginMethod] = [
        CAPPluginMethod(name: "open", returnType: CAPPluginReturnPromise),
        CAPPluginMethod(name: "close", returnType: CAPPluginReturnNone)
    ]

    private var webVC: CustomWebViewController?

    @objc func open(_ call: CAPPluginCall) {
        let urlString = call.getString("url") ?? ""
        let closeButtonText = call.getString("closeButtonText")
        let closeWarningText = call.getString("closeWarningText")
        let positionString = call.getString("toolbarPosition") ?? "top"
        let position: ToolbarPosition = positionString == "bottom" ? .bottom : .top
        guard let url = URL(string: urlString) else {
            call.reject("Invalid URL")
            return
        }
        

        DispatchQueue.main.async {
            let vc = CustomWebViewController(url: url, closeButtonText: closeButtonText, closeWarningText: closeWarningText, toolbarPosition: position)
            vc.modalPresentationStyle = .fullScreen
            self.bridge?.viewController?.present(vc, animated: true)
            self.webVC = vc
        }

        call.resolve()
    }

    @objc func close(_ call: CAPPluginCall) {
        DispatchQueue.main.async {
            self.webVC?.dismiss(animated: true)
            self.webVC = nil
        }
        call.resolve()
    }
}
```

## 4) JS 코드 수정
- JS interface 구현
```ts
// custom-webview/src/definitions.ts

export interface CustomWebViewPlugin {
  open(options: { url: string; closeButtonText?: string; closeWarningText?: string; toolbarPosition?: 'top'|'bottom' }): Promise<void>;

  close(): Promise<void>;
}
```

## 5) 빌드 파일 생성

```bash
npm run build
# /dist 폴더 생성 확인
```

# 3. Custom Plugin local file 연결
- root 에서 npm 설치

```bash
npm install ./custom-webview
```

- package.json 내 설치 확인
```json
// package.json

"dependencies": {
	"custom-webview": "file:custom-webview",
},
```

## 1) Plugin 연결
```ts
import { CustomWebView } from 'custom-webview'; // local/custom-webview/dist/esm/index

const openWebView = async (url: string, closeButtonText = '닫기') => {
  await CustomWebView.open({
  	url: url,
  	closeButtonText: closeButtonText,
  	closeWarningText: '웹뷰를 닫으려면 다시 탭하세요.',
  	toolbarPosition: 'bottom'
  });
}
```

# 4. npm publish
> npm publish 용도로, 해당 과정은 건너뛰어도 됨

## 1) npm publish 등록

```bash
pwd
# root/custom-webview

npm login
# 브라우저에서 npm 로그인
```
- 로그인 후 profile > access Token > generate New Token 선택
- Bypass two-factor authentication (2FA) 체크(npm publish 2FA 우회용)
- packages and scopes > permissions > Read and write 체크
- genarate token 버튼 클릭
- 토큰 복사 후 아래 명령어 내 `YOUR_TOKEN`에 값 입력

> Allowed IP randges 사용 시, 내 IP 주소 / 32 입력
> e.g. 1.2.3.4/32

```bash
npm config set //registry.npmjs.org/:_authToken=YOUR_TOKEN
```

- npm publish(공개용으로 설정, private는 유료)
```bash
npm publish —access=public
```

> 패키지 명은 `lowercase` + `-` 만 허용
- npm page 확인(또는 [npm 사이트](https://www.npmjs.com/) 확인)

```bash
npm view custom-webview
# custom-webview@0.0.1 | MIT | deps: none | versions: 1
# YOUR_NICKNAME / Capacitor IOS Webview
# https://github.com/YOUR_GITHUB_ADDRESS#readme

# keywords: capacitor, plugin, native

# dist
# .tarball: https://registry.npmjs.org/YOUR_PACKAGE_NAME
# .shasum: ...
# .integrity: sha512-...
# .unpackedSize: 23.6 kB

# maintainers:
# - YOUR_NICKNAME <YOUR_EMAIL>

# dist-tags:
# latest: 0.0.1  

# published 2 minutes ago by YOUR_NICKNAME <YOUR_EMAIL>
```

## 2) npm publish version 관리
- 수정사항이 생긴 경우, 현재 git 형상을 그대로 publish에 업로드 함

```
npm version patch
# v0.0.2

# version push
git push

# build 결과물(dist) 업데이트
npm run build

# 현재 버전 publish
npm publish —access=public
```

## 3) npm publish install
- publish에 등록된 npm 라이브러리를 원격 install

```bash
npm install custom-webview
```

```json
// package.json

"dependencies": {
	"custom-webview": "^0.0.2",
},
```