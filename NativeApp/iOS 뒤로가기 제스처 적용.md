- iOS의 왼쪽 edge swipe는 별도 설정을 통해 적용 가능
- web의 touchstart / touchmove 로 잡히는 스와이프와는 완전히 다른 이벤트
- WKWebView 나 Safari가 아닌 UINavigationController의 interactivePopGestureRecognizer가 처리함
- 따라서 해당 제스처(이벤트)는 Native 레벨 제스처이며, Web(JavaScript)는 이 제스처(이벤트)를 캐치하지 못함
- 아래 명령어를 통해 제스처를 활성화 가능(defalut: false)

```swift
import UIKit
import Capacitor

@objc(MainViewController)
class MainViewController: CAPBridgeViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
                
        // ← 오른쪽 스와이프 제스처로 뒤로가기 허용
        webView.allowsBackForwardNavigationGestures = true
    }
  }
```