# Aspect Ratio

요소의 가로 세로 비율(aspect ratio)을 제어하는 유틸리티입니다.

## 기본 사용법

### 가로 세로 비율 설정하기

`aspect-*` 유틸리티를 사용하여 요소의 가로 세로 비율을 설정할 수 있습니다.

```html
<iframe class="w-full aspect-video ..." src="https://www.youtube.com/..."></iframe>
```

Tailwind는 기본적으로 다양한 비율 값을 제공하지 않으며, 필요에 따라 임의의 값을 사용하기가 더 쉽습니다. 자세한 내용은 임의 값 사용 섹션을 참조하세요.

### 브라우저 지원

`aspect-*` 유틸리티는 네이티브 `aspect-ratio` CSS 속성을 사용하며, Safari 15 이전 버전에서는 지원되지 않았습니다. Safari 15의 보급 이전에는 Tailwind의 `aspect-ratio` 플러그인을 사용하는 것이 좋습니다.

## 조건부 적용

### Hover, Focus 및 기타 상태

Tailwind는 상태별로 유틸리티 클래스를 조건부로 적용할 수 있는 변형(modifier)을 제공합니다. 예를 들어, `hover:aspect-square`를 사용하면 hover 시에만 `aspect-square` 유틸리티가 적용됩니다.

```html
<iframe class="w-full aspect-video hover:aspect-square" src="https://www.youtube.com/..."></iframe>
```

사용 가능한 모든 상태 수정자는 Hover, Focus & Other States 문서를 참조하세요.

### 브레이크포인트와 미디어 쿼리

미디어 쿼리와 같은 브레이크포인트, 다크 모드, `prefers-reduced-motion` 등에 대해 조건부로 적용할 수 있는 변형을 사용할 수 있습니다. 예를 들어, `md:aspect-square`를 사용하면 중간 화면 크기 이상에서만 `aspect-square` 유틸리티가 적용됩니다.

```html
<iframe class="w-full aspect-video md:aspect-square" src="https://www.youtube.com/..."></iframe>
```

자세한 내용은 Responsive Design, Dark Mode 및 기타 미디어 쿼리 변형 문서를 참조하세요.

## 사용자 정의 값 사용하기

### 테마 사용자 정의하기

기본적으로 Tailwind는 최소한의 `aspect-ratio` 유틸리티를 제공합니다. `tailwind.config.js` 파일의 `theme.aspectRatio` 또는 `theme.extend.aspectRatio`를 편집하여 값을 사용자 정의할 수 있습니다.

```js
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      aspectRatio: {
        '4/3': '4 / 3',
      },
    }
  }
}
```

기본 테마 사용자 정의에 대한 자세한 내용은 Theme Customization 문서를 참조하세요.

### 임의 값 사용하기

테마에 포함할 필요가 없는 임의의 `aspect-ratio` 값을 사용해야 할 경우, 대괄호(`[]`)를 사용해 원하는 값을 즉석에서 설정할 수 있습니다.

```html
<iframe class="w-full aspect-[4/3]" src="https://www.youtube.com/..."></iframe>
```

## Quick reference

Class | Properties
:-: | :-:
aspect-auto | aspect-ratio: auto;
aspect-square | aspect-ratio: 1 / 1;
aspect-video | aspect-ratio: 16 / 9;
