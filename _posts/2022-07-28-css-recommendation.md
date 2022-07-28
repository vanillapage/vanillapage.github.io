---
layout: post
title: IE 지원 종료로 인한 추천 CSS
subtitle: IE 공식지원이 종료됨에 따라 부분 제한적이던 CSS 스킬들 중 자주 사용할법한 것들을 선별하여 정리
categories: CSS
tags: [css]
---

### **[image-rendering](https://caniuse.com/?search=image-rendering)**

 

이미지의 원본 사이즈와 다른 사이즈 (확대 혹은 축소)로 변경 지정 시에만 적용되며, 이미지의 렌더링 방식을 지정하여 부드럽게, 혹은 선명하게 노출할 수 있습니다. (브라우저마다 지원하는 힌트가 상이합니다)

- auto
- pixelated (-ms-interpolation-mode: nearest-neighbor)
- crisp-egdes ( 크롬에서는 `-webkit-optimize-contrast` 란 값으로 지원)

만약, 위 속성값만으로도 해결이 안된다면 다음 속성을 추가하여 적용합니다.

```css
image-rendering: -webkit-optimize-contrast;
backface-visibility: hidden;
transform: translateZ(0);
```

### [**background-clip**](https://caniuse.com/?search=background-clip)

`background-clip` 은 박스 모델 내에서 요소의 배경이 차지할 부분을 정의합니다.

---

- border-box : 배경 요소가 테두리 경계까지 차지 (z축 기준 border 영역 하위에 위치)
- padding-box : 배경 요소가 안쪽 여백의 바깥 경계까지 차지
- content-box: 콘텐츠 박스에 맞춰 차지
- text: 텍스트 위 영역만 차지 (cliping-mask)

### [caret-color](https://caniuse.com/?search=caret-color)

텍스트 입력 영역 커서 색상을 변경할 수 있습니다.

---

```css
p { caret-color: green; }
```

### [clip-path](https://caniuse.com/?search=clip-path)

요소의 클리핑 범위를 지정할 수 있으며 지정한 범위 바깥 부분의 영역은 보이지 않게 됩니다.
클리핑 범위로는 SVG 요소로 지정하거나 기본 도형을 기준으로 수치를 입력하여 적용할 수 있습니다.

---

- clip-path maker [https://bennettfeely.com/clippy/](https://bennettfeely.com/clippy/)

### [filter](https://caniuse.com/?search=filter)

이미지 요소에 시각 효과를 적용하거나 배경&테두리의 렌더링을 조정할 때 사용합니다. 여러 속성을 나열하여 중첩으로 효과를 적용할 수도 있습니다.

---

- blur, drop-shadow, grayscale 등… 과 같은 효과 적용
- `url()` : SVG 내 필터를 가리키는 URL을 기입하여 filter가 적용된 SVG 요소를 삽입

### [image-set()](https://caniuse.com/?search=image-set)

디바이스 해상도와 이미지 타입별 image 를 분기하여 적용할 수 있습니다.

<aside>
💡 * Firefox를 제외한 모던 브라우저에서 일부 기능이 제한되어 있습니다. 브라우저 지원 범위 확인 후 verdor prefix 적용이 필요합니다.

</aside>

---

```css
div {
	background-image: image-set (
		url("sample.jpg") 1x,
		url("sample@2x.jpg") 2x);
}

div {
	background-image: image-set (
		"sample.webp" type("image/webp"),
    "sample2.jpg" type("image/jpeg")); /* 이전 형식을 지원하지 않을 경우 다음 명세된 경로로 적용 */
}
```

### [line-clamp](https://caniuse.com/?search=line-clamp)

블록 타입 컨테이너의 콘텐츠를 지정한 줄 (line) 로 제한하여 벗어난 영역은 말줄임으로 노출할 수 있습니다.

<aside>
💡 display 속성을 `-webkit-box` || `webkit-inline-box` 로 적용 + `-webkit-box-orient: vertical` 로 함께 지정해야 적용이 가능합니다.

</aside>

---

```css
.text {
	overflow: hidden;
	display: -webkit-box;
	-webkit-box-orient: vertical;
	-webkit-line-clamp: 3;
}
```

### [position: sticky](https://caniuse.com/?search=sticky)

<aside>
💡 상위 scroll 컨테이너기준으로 offset 값이 적용됩니다.

</aside>

### [inset](https://caniuse.com/?search=inset)

`top`, `right`, `bottom`, `left` 각각의 속성들을 `inset` 이라는 속성의 값으로 축약하여 선언할 수 있습니다.
단, `relative`, `absolute`, `fixed`의 경우에만 적용 가능합니다.

### [CSS Variable (custom properties)](https://caniuse.com/?search=var())

사용자 지정 변수도 제한 없이 사용 가능합니다.

### [font-family: system-ui](https://caniuse.com/?search=system-ui)

font-family 를 OS 글꼴로 적용 가능합니다.

### [object-fit](https://caniuse.com/?search=object-fit), [object-position](https://caniuse.com/?search=object-position)

이미지나 영상 등의 콘텐츠 비율, 사이즈, 위치 등을 조절합니다.

### [shape-outside](https://caniuse.com/?search=shape-outside), [shape-margin](https://caniuse.com/?search=shape-margin), [shape-image-threshold](https://caniuse.com/?search=shape-image-threshold)

float가 적용된 요소 주변 스타일을 조정하는 속성입니다.

---

- **shape-outside** :  float가 적용된 요소와 인접한 콘텐츠가 감싸는 영역을 기존 사각형이 아닌 특정 형태로 지정하여 적용. 값이 아닌 `url` 값을 이용해 이미지를 적용하거나 `linear-gradient`를 이용해 gradient shape으로도 적용이 가능
- **shape-margin**: `shape-outside` 로 지정된 영역과 인접한 콘텐츠 간 사이의 여백을 지정
- **shape-image-threshold**: `shape-outside`를 이미지로 적용했을 경우 해당 이미지&인접 콘텐츠 간 구분 기준점을 이미지의 불투명도 기준으로 적용하는데 이 때 기준 수치를 shape-image-shreshold 로 변경 가능. (`0.0 ~ 1.0`)
0.5의 값을 적용했을 경우 경계선의 불투명도가 50%를 초과하는 모든 픽셀을 포함함.

### [prefers-color-scheme](https://caniuse.com/?search=prefers-color-scheme)

해당 미디어 특성은 사용자 시스템의 테마 사용 여부를 탐지합니다.

```css
@media (prefers-color-scheme: light) {
	.text { color: #000; }
}

@media (prefers-color-scheme: dark) {
	.text { color: #fff; }
}
```

### ****[will-change](https://caniuse.com/?search=will-change)****

브라우저에게 변화가 예상되는 요소를 미리 알려주어 최적화된 렌더링을 할 수 있도록 하기 위한 속성입니다.

<aside>
💡 해당 속성은 빠른 렌더링에는 도움을 주지만 이에 따른 비용은 큽니다.
필요한 부분에만 최소한으로 사용하고, 동작을 마친 뒤 기본 상태로 되돌리는 등의 작업을 통해 브라우저의 자원 소모를 아끼는것이 좋습니다.

</aside>

### [scroll-behavior](https://caniuse.com/?search=scroll-behavior)

anchor tag로 이동하는 스크롤의 움직임을 제어합니다.

+) Safari는 비교적 최신 버전에서 지원되는 속성이므로 적용 시 브라우저 지원 범위를 확인하고 적용합니다.

ref.
- [https://developer.mozilla.org/](https://developer.mozilla.org/)
- [https://naradesign.github.io/css-ie-not-supported.html](https://naradesign.github.io/css-ie-not-supported.html)
- [https://www.w3schools.com/cssref/css3_browsersupport.asp](https://www.w3schools.com/cssref/css3_browsersupport.asp)