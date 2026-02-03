# ipk 옵션(TV 옵션 영향)
```JSON
// appinfo.json
{
  ...
  "memoryOptimizeLevel": 0, // GPU 성능 우선
  "resolution": ["1920x1080"] // 해상도 고정
}
```

# 브라우저 DPR 고정
```jsx
// src/App/App.js

useEffect(() => {
	Object.defineProperty(window, 'devicePixelRatio', {
		get: () => 1 // DPR 1로 고정
	});
}, []);
```

# WebGL 사용 시
```jsx
// WebGL 사용 시 context 옵션
import mapboxgl from 'mapbox-gl';
import 'mapbox-gl/dist/mapbox-gl.css';

...

const map = new mapboxgl.Map({
  container: 'map',
  style: 'mapbox://styles/...',
  antialias: false, // GPU 부하 감소
  preserveDrawingBuffer: false // GPU 메모리 절약
  fadeDuration: 0,               // 타일 페이드 제거 (모자이크 감소)
  attributionControl: false,
  logoPosition: 'bottom-left'
});
```