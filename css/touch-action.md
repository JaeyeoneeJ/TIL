## Introduction
- 프로젝트를 진행하면서 터치 이벤트를 제한해야 하는 상황이 자주 발생한다.
- 가장 대표적인 터치 이벤트는 스크롤일 것이다. 하지만 내가 작성한 코드가 아닌 부분을 제어할 수 없다면 제한된 환경에서 제어를 해야하는 상황이 발생한다.
- 대표적인 터치 스크롤 이벤트 제한 방법으로는 body 태그를 직접 제어하는 방식이다.
- 하지만, DOM element를 직접 조작한다는 측면에서 대단히 위험성이 높고, 다른 사람이 해당 부분을 계속해서 수정해 나간다면 이벤트 발생자가 꼬이는 현상이 발생하게 될 것이다. 마치 일시적인 상처를 덮기 위해 계속 덧대는 것과 같다.
- body 태그를 직접 제어하는 것은 최후의 방법으로 쓰여야 한다고 생각한다.
- 그러다가 단순한 터치의 조작을 제어하고 막을 수 있는 touch-action 속성에 대해 알게 되어 간단히 소개하고자 글을 적는다.


## touch-action
- CSS `touch-action` 속성은 터치스크린 사용자가 요소의 영역을 조작할 수 있는 방법을 설정함(예: 브라우저에 내장된 확대/축소 기능)

```css
/* Keyword values */
touch-action: auto;
touch-action: none;
touch-action: pan-x;
touch-action: pan-left;
touch-action: pan-right;
touch-action: pan-y;
touch-action: pan-up;
touch-action: pan-down;
touch-action: pinch-zoom;
touch-action: manipulation;

/* Global values */
touch-action: inherit;
touch-action: initial;
touch-action: revert;
touch-action: revert-layer;
touch-action: unset;
```

- 기본적으로 패닝(스크롤) 및 집기 제스처는 브라우저에서만 처리됨
- [포인터 이벤트](https://developer.mozilla.org/en-US/docs/Web/API/Pointer_events)를 사용하는 애플리케이션은 브라우저가 터치 제스처를 처리하기 시작할 때 [`pointercancel`](https://developer.mozilla.org/en-US/docs/Web/API/Element/pointercancel_event) 이벤트를 수신함
- 브라우저에서 처리해야 하는 제스처를 명시적으로 지정하여 애플리케이션은 나머지 제스처에 대해 [`pointermove`](https://developer.mozilla.org/en-US/docs/Web/API/Element/pointermove_event) 및 [`pointerup`](https://developer.mozilla.org/en-US/docs/Web/API/Element/pointerup_event) 리스너에서 고유한 동작을 제공할 수 있음
- 터치 이벤트를 사용하는 애플리케이션은 [`preventDefault()`](https://developer.mozilla.org/en-US/docs/Web/API/Event/preventDefault)를 호출하여 브라우저의 제스처 처리를 비활성화하지만 `touch-action`을 사용하여 이벤트 리스너가 호출되기 전에 브라우저가 애플리케이션의 의도를 알 수 있도록 해야 함

- 제스처가 시작되면 브라우저는 터치된 요소와 해당 조상의 터치 동작 값을 제스처를 구현하는 요소(즉, 스크롤 요소를 포함하는 첫 번째 요소)까지 교차함
- 즉, 실제로 터치 동작은 해당 요소의 하위 항목에 대해 명시적으로 터치 동작을 지정할 필요 없이 일부 사용자 지정 동작이 있는 최상위 요소에만 일반적으로 적용됩니다.

> 참고: 제스처가 시작된 후 `touch-action`을 변경해도 현재 제스처의 동작에는 영향을 미치지 않음
> 
> 터치 동작은 일반적으로 터치(touchstart, touchend,  touchmove 등), 확대-축소(pinch zoom) 등 여러가지로 쓰이는데, 만일 하나의 동작이 시작되었다면 다른 동작으로의 변경이 불가하다는 뜻으로 보임
> 
> 즉, 터치로 drag하여 스크롤 이벤트가 발생했다면, pinch zoom과 같은 동작으로 변경이 되지 않는다는 뜻으로 보임


## Syntax
- `touch-action` 속성은 다음 중 하나로 지정할 수 있음

- `auto`, `none`, `manipulation` 중 하나
- `pan-x`, `pan-left`, `pan-right` 키워드 중 하나 and/or `pan-y`, `pan-up`, `pan-down` 키워드 중 하나 및 선택적으로 `pinch-zoom` 키워드

### Values
1. `auto`
- 모든 이동 및 확대/축소 제스처의 브라우저 처리를 활성화 함

2. `none`
- 모든 이동 및 확대/축소 제스처의 브라우저 처리를 비활성화 함

3. `pan-x`
- 한 손가락 수평 패닝 제스처를 활성화 함(1개의 터치 동작으로 x축 끌기만을 허용, x축 스크롤만 허용하고 싶을 때 등)
- `pan-y`, `pan-up`, `pan-down` and/or `pinch-zoom`과 결합될 수 있음

4. `pan-y`
- 한 손가락 수직 패닝 제스처를 활성화 함(1개의 터치 동작으로 y축 끌기만을 허용, y축 스크롤만 허용하고 싶을 때 등)
- `pan-x`, `pan-left`, `pan-right` and/or `pinch-zoom`과 결합될 수 있음

5. `manipulation`
- 패닝 및 핀치 확대/축소 제스처를 활성화하지만 두 번 탭하여 확대/축소하는 것과 같은 추가 비표준 제스처는 비활성화함
- 두 번 탭하여 확대/축소를 비활성화하면 사용자가 화면을 탭할 때 브라우저에서 클릭 이벤트 생성을 지연시킬 필요가 없음
- 이것은 "**pan-x pan-y pinch-zoom**"(호환성을 위해 여전히 유효함)의 별칭임(이것 대신 "**manipulation**"을 적어 같은 속성을 활성화할 수 있음)

6. `pan-left`, `pan-right`, `pan-up`, `pan-down`
- 지정된 방향으로 스크롤하여 시작하는 한 손가락 제스처를 활성화함
- 스크롤이 시작되면 방향이 여전히 반대일 수 있음
	- "**up**"(pan-up) 스크롤한다는 것은 사용자가 화면 표면에서 손가락을 아래쪽으로 드래그한다는 것을 의미하고 마찬가지로 왼쪽으로 이동한다는 것은 사용자가 손가락을 오른쪽으로 드래그한다는 것을 의미함 
- 더 간단한 표현이 있는 경우를 제외하고는 여러 방향을 결합할 수 있음(예: "**pan-x**"가 더 간단하기 때문에 "**pan-left pan-right**"는 유효하지 않지만 "**pan-left pan-down**"은 유효함)

7. `pinch-zoom`
- 페이지의 다중 손가락 패닝 및 확대/축소를 활성화 함. 모든 **pan-value**과 결합될 수 있음


## Accessibility concerns
- `touch-action: none;` 선언은 브라우저의 확대/축소 기능 작동을 방해할 수 있음
	- 폰트의 크기가 디바이스에 고정이 되어버리기 때문에, 시력이 약한 사람들이 페이지 내용을 읽고 이해하지 못할 수 있음
	- [MDN Understanding WCAG, Guideline 1.4 explanations](https://developer.mozilla.org/en-US/docs/Web/Accessibility/Understanding_WCAG/Perceivable#guideline_1.4_make_it_easier_for_users_to_see_and_hear_content_including_separating_foreground_from_background)
	- [Understanding Success Criterion 1.4.4 | Understanding WCAG 2.0](https://www.w3.org/TR/UNDERSTANDING-WCAG20/visual-audio-contrast-scale.html)


## Formal definition

|   |   |
|---|---|
|[Initial value](https://developer.mozilla.org/en-US/docs/Web/CSS/initial_value)|`auto`|
|Applies to|대체되지 않은 인라인 요소, 테이블 행, 행 그룹, 테이블 열 및 열 그룹을 제외한 모든 요소|
|[Inherited](https://developer.mozilla.org/en-US/docs/Web/CSS/Inheritance)|no|
|[Computed value](https://developer.mozilla.org/en-US/docs/Web/CSS/computed_value)|as specified|
|Animation type|Not animatable|

### Formal syntax
```css
touch-action: auto | none | [[ pan-x | pan-left | pan-right ]  || [ pan-y | pan-up | pan-down ] || pinch-zoom ] | manipulation
```


## Example

### Disabling all gestures
- 가장 일반적인 사용법은 지도 또는 게임 표면과 같은 자체 드래그 및 확대/축소 동작을 제공하는 요소(및 스크롤할 수 없는 하위 항목)에서 모든 제스처를 비활성화하는 것임

```html
<div id="map"></div>
```

```css
#map {
  height: 150vh;
  width: 70vw;
  background: linear-gradient(blue, green);
  touch-action: none;
}
```



<hr>
## ref.
- https://developer.mozilla.org/en-US/docs/Web/CSS/touch-action#browser_compatibility