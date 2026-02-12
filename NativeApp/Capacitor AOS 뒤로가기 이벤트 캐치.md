# 0. 개요
- iOS의 경우, window.eventListener `backbutton` 이 없으나, AOS의 경우 `backbutton`을 통해 뒤로가기 또는 앱 종료 등을 수행함
- webView history가 있는 경우, 뒤로가기 기능을, 없는 경우 앱 종료를 강제함
- 해당 Back 이벤트는 Native에서 Back을 먼저 가로채고, 해당 이벤트를 window.eventListener `backbutton`에 담음
- 일반적인 Pure Native인 경우, AOS에서 `onBackPressed` 를 Override 해야 의도된 동작을 수행할 수 있음
- Capacitor를 통해 AOS 빌더를 생성한 경우, Android back press를 intercept하여 WebView에 window 이벤트를 전달하고 기존 back 종료 동작을 진행함

# 1. hook 생성

```tsx
// src/hooks/useDoubleBackExit.ts

import { App } from '@capacitor/app';
import { Toast } from '@capacitor/toast';
import { useEffect, useRef } from 'react';

const useDoubleBackExit = () => {
	const lastBack = useRef(0);

	useEffect(() => {
		let handler: any;

		const setup = async () => {
			handler = await App.addListener('backButton', async () => {
                // 기존 WebView history가 있는 경우 기존 뒤로가기 동작 처리
				if (window.history.length > 1) {
					window.history.back();
					return;
				}

				const now = Date.now();

                if (now - lastBack.current < 2000) {
     				// history가 없는 상태의 마지막 뒤로가기 이벤트가 수신된지 2초 미만인 경우, 앱 종료
					App.exitApp();
				} else {
       				// history가 없는 상태의 마지막 뒤로가기 이벤트 수신 기록이 없거나 2초 이상인 경우, 토스트 띄움
					lastBack.current = now;
					await Toast.show({
						text: '한 번 더 누르면 앱이 종료됩니다.',
						duration: 'short'
					});
				}
			});
		};

		setup();

		return () => {
			handler?.remove(); // 훅 종료 시 이벤트 제거
		};
	}, []);
};

export default useDoubleBackExit;
```

# 2. 연결

```tsx
// src/App.tsx
import useDoubleBackExit from './hooks/useDoubleBackExit';

function App() {
  useDoubleBackExit();
  
  return ...
}
```

