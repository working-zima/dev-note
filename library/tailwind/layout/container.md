# 컨테이너

현재 브레이크포인트에 맞게 요소의 너비를 고정하는 컴포넌트입니다.

## 기본 사용법

### 컨테이너 사용하기

`container` 클래스는 요소의 `max-width`를 현재 브레이크포인트의 `min-width`에 맞게 설정합니다. 이는 유동적인 뷰포트를 수용하려는 대신, 고정된 화면 크기를 기준으로 디자인하려는 경우 유용합니다.

다른 프레임워크에서 사용했던 컨테이너와는 달리, **Tailwind의 컨테이너는 자동으로 중앙에 배치되지 않으며 기본적인 수평 패딩이 없습니다.**

컨테이너를 중앙에 배치하려면 `mx-auto` 유틸리티를 사용하세요:

```html
<div class="container mx-auto">
  <!-- ... -->
</div>
```

수평 패딩을 추가하려면 `px-*` 유틸리티를 사용하세요:

```html
<div class="container mx-auto px-4">
  <!-- ... -->
</div>
```

컨테이너를 기본적으로 중앙에 배치하거나 기본 수평 패딩을 포함하려면 아래의 [사용자 정의 옵션](#customizing)을 참조하세요.

---

## 조건에 따라 적용하기

### 반응형 변형

`container` 클래스는 기본적으로 `md:container`와 같은 반응형 변형도 포함하고 있어, 특정 브레이크포인트 이상에서만 컨테이너처럼 동작하도록 만들 수 있습니다:

```html
<!-- `md` 브레이크포인트까지는 전체 너비, 그 이후에는 컨테이너로 고정 -->
<div class="md:container md:mx-auto">
  <!-- ... -->
</div>
```

---

## 사용자 정의

### 기본적으로 중앙에 배치하기

컨테이너를 기본적으로 중앙에 배치하려면 `theme.container` 섹션의 `center` 옵션을 `true`로 설정하세요:

```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  theme: {
    container: {
      center: true,
    },
  },
}
```

### 기본적으로 수평 패딩 추가하기

기본적으로 수평 패딩을 추가하려면 `theme.container` 섹션의 `padding` 옵션에서 원하는 패딩 값을 지정하세요:

```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  theme: {
    container: {
      padding: '2rem',
    },
  },
}
```

각 브레이크포인트에 대해 다른 패딩 값을 지정하려면, 객체를 사용하여 `default` 값을 제공하고 각 브레이크포인트에 대한 오버라이드를 추가할 수 있습니다:

```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  theme: {
    container: {
      padding: {
        DEFAULT: '1rem',
        sm: '2rem',
        lg: '4rem',
        xl: '5rem',
        '2xl': '6rem',
      },
    },
  },
};
```

## Quick reference

Class | Breakpoint | Properties
:-: | :-: | :-:
container | None | width: 100%;
container | sm (640px) | max-width: 640px;
container| md (768px) | max-width: 768px;
container| lg (1024px) | max-width: 1024px;
container| xl (1280px) | max-width: 1280px;
container| 2xl (1536px) | max-width: 1536px;
