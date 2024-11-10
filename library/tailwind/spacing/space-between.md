# Space Between

자식 요소들 간의 간격을 제어하기 위한 유틸리티입니다.

## 기본 사용법

### 자식 요소 간 수평 간격 추가하기

`space-x-*` 유틸리티를 사용해 요소들 간 수평 간격을 설정할 수 있습니다.

```html
<div class="flex space-x-4 ...">
  <div>01</div>
  <div>02</div>
  <div>03</div>
</div>
```

### 자식 요소 간 수직 간격 추가하기

`space-y-*` 유틸리티를 사용해 요소들 간 수직 간격을 설정할 수 있습니다.

```html
<div class="flex flex-col space-y-4 ...">
  <div>01</div>
  <div>02</div>
  <div>03</div>
</div>
```

### 자식 요소 순서 반전

요소들이 역순으로 배치된 경우(`flex-row-reverse` 또는 `flex-col-reverse` 사용 시), `space-x-reverse` 또는 `space-y-reverse` 유틸리티를 사용하여 올바른 쪽에 간격이 적용되도록 할 수 있습니다.

```html
<div class="flex flex-row-reverse space-x-4 space-x-reverse ...">
  <div>01</div>
  <div>02</div>
  <div>03</div>
</div>
```

### 음수 값 사용하기

음수 간격 값을 사용하려면 클래스 이름 앞에 -를 붙여 음수 값을 설정할 수 있습니다.

```html
<div class="flex -space-x-4 ...">
  <!-- ... -->
</div>
```

### 제한 사항

이 유틸리티는 그룹 내 첫 번째 요소를 제외한 나머지 요소에 마진을 추가하는 단축 코드에 불과하며, 그리드와 같이 복잡한 레이아웃이나, DOM 순서와 다른 맞춤 순서로 렌더링되는 경우에는 적합하지 않습니다.

이런 상황에서는 가능한 경우 간격(gap) 유틸리티를 사용하는 것이 좋으며, 부모 요소에 맞춤형 음수 마진을 적용한 뒤 각 요소에 마진을 추가하는 방식으로 해결할 수 있습니다.

```html
<div class="flow-root">
  <div class="-m-2 flex flex-wrap">
    <div class="m-2 ..."></div>
    <div class="m-2 ..."></div>
    <div class="m-2 ..."></div>
  </div>
</div>
```

### Divide 유틸리티와 함께 사용할 수 없음

`space-*` 유틸리티는 divide 유틸리티와 함께 사용하도록 설계되지 않았습니다. 이 경우 자식 요소에 직접 마진/패딩 유틸리티를 추가하는 것이 좋습니다.

## 조건부 적용

### Hover, Focus 및 기타 상태

Tailwind는 상태별로 유틸리티 클래스를 조건부로 적용할 수 있는 변형(modifier)을 제공합니다. 예를 들어 `hover:space-x-8`을 사용하면 hover 시에만 `space-x-8` 유틸리티가 적용됩니다.

```html
<div class="flex space-x-2 hover:space-x-8">
  <!-- ... -->
</div>
```

사용 가능한 모든 상태 수정자는 Hover, Focus & Other States 문서를 참조하세요.

### 브레이크포인트와 미디어 쿼리

미디어 쿼리와 같은 브레이크포인트, 다크 모드, prefers-reduced-motion 등에 대해 조건부로 적용할 수 있는 변형을 사용할 수 있습니다. 예를 들어 `md:space-x-8`을 사용하면 중간 화면 크기 이상에서만 `space-x-8` 유틸리티가 적용됩니다.

```html
<div class="flex space-x-2 md:space-x-8">
  <!-- ... -->
</div>
```

자세한 내용은 Responsive Design, Dark Mode 및 기타 미디어 쿼리 변형 문서를 참조하세요.

## 사용자 정의 값 사용하기

### 테마 사용자 정의하기

기본적으로 Tailwind의 간격 크기 조정은 기본 간격 크기(Spacing Scale)를 따릅니다. `tailwind.config.js` 파일에서 `theme.spacing` 또는 `theme.extend.spacing`을 편집해 간격 크기를 사용자 정의할 수 있습니다.

```js
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      spacing: {
        '5px': '5px',
      }
    }
  }
}
```

또는 `theme.space` 또는 `theme.extend.space`를 편집해 간격 크기만 별도로 사용자 정의할 수도 있습니다.

```js
// tailwind.config.js

module.exports = {
  theme: {
    extend: {
      space: {
        '5px': '5px',
      }
    }
  }
}
```

기본 테마 사용자 정의에 대한 자세한 내용은 Theme Customization 문서를 참조하세요.

### 임의 값 사용하기

테마에 포함할 필요가 없는 임의의 간격 값을 사용해야 할 경우, 대괄호(`[]`)를 사용해 원하는 값을 즉석에서 설정할 수 있습니다.

```html
<div class="space-y-[5px]">
  <!-- ... -->
</div>
```

## Quick reference

Class | Properties
:-: | :-:
space-x-0 > * + * | margin-left: 0px;
space-y-0 > * + * | margin-top: 0px;
space-x-0.5 > * + * | margin-left: 0.125rem; /* 2px */
space-y-0.5 > * + * | margin-top: 0.125rem; /* 2px */
space-x-1 > * + * | margin-left: 0.25rem; /* 4px */
space-y-1 > * + * | margin-top: 0.25rem; /* 4px */
space-x-1.5 > * + * | margin-left: 0.375rem; /* 6px */
space-y-1.5 > * + * | margin-top: 0.375rem; /* 6px */
space-x-2 > * + * | margin-left: 0.5rem; /* 8px */
space-y-2 > * + * | margin-top: 0.5rem; /* 8px */
space-x-2.5 > * + * | margin-left: 0.625rem; /* 10px */
space-y-2.5 > * + * | margin-top: 0.625rem; /* 10px */
space-x-3 > * + * | margin-left: 0.75rem; /* 12px */
space-y-3 > * + * | margin-top: 0.75rem; /* 12px */
space-x-3.5 > * + * | margin-left: 0.875rem; /* 14px */
space-y-3.5 > * + * | margin-top: 0.875rem; /* 14px */
space-x-4 > * + * | margin-left: 1rem; /* 16px */
space-y-4 > * + * | margin-top: 1rem; /* 16px */
space-x-5 > * + * | margin-left: 1.25rem; /* 20px */
space-y-5 > * + * | margin-top: 1.25rem; /* 20px */
space-x-6 > * + * | margin-left: 1.5rem; /* 24px */
space-y-6 > * + * | margin-top: 1.5rem; /* 24px */
space-x-7 > * + * | margin-left: 1.75rem; /* 28px */
space-y-7 > * + * | margin-top: 1.75rem; /* 28px */
space-x-8 > * + * | margin-left: 2rem; /* 32px */
space-y-8 > * + * | margin-top: 2rem; /* 32px */
space-x-9 > * + * | margin-left: 2.25rem; /* 36px */
space-y-9 > * + * | margin-top: 2.25rem; /* 36px */
space-x-10 > * + * | margin-left: 2.5rem; /* 40px */
space-y-10 > * + * | margin-top: 2.5rem; /* 40px */
space-x-11 > * + * | margin-left: 2.75rem; /* 44px */
space-y-11 > * + * | margin-top: 2.75rem; /* 44px */
space-x-12 > * + * | margin-left: 3rem; /* 48px */
space-y-12 > * + * | margin-top: 3rem; /* 48px */
space-x-14 > * + * | margin-left: 3.5rem; /* 56px */
space-y-14 > * + * | margin-top: 3.5rem; /* 56px */
space-x-16 > * + * | margin-left: 4rem; /* 64px */
space-y-16 > * + * | margin-top: 4rem; /* 64px */
space-x-20 > * + * | margin-left: 5rem; /* 80px */
space-y-20 > * + * | margin-top: 5rem; /* 80px */
space-x-24 > * + * | margin-left: 6rem; /* 96px */
space-y-24 > * + * | margin-top: 6rem; /* 96px */
space-x-28 > * + * | margin-left: 7rem; /* 112px */
space-y-28 > * + * | margin-top: 7rem; /* 112px */
space-x-32 > * + * | margin-left: 8rem; /* 128px */
space-y-32 > * + * | margin-top: 8rem; /* 128px */
space-x-36 > * + * | margin-left: 9rem; /* 144px */
space-y-36 > * + * | margin-top: 9rem; /* 144px */
space-x-40 > * + * | margin-left: 10rem; /* 160px */
space-y-40 > * + * | margin-top: 10rem; /* 160px */
space-x-44 > * + * | margin-left: 11rem; /* 176px */
space-y-44 > * + * | margin-top: 11rem; /* 176px */
space-x-48 > * + * | margin-left: 12rem; /* 192px */
space-y-48 > * + * | margin-top: 12rem; /* 192px */
space-x-52 > * + * | margin-left: 13rem; /* 208px */
space-y-52 > * + * | margin-top: 13rem; /* 208px */
space-x-56 > * + * | margin-left: 14rem; /* 224px */
space-y-56 > * + * | margin-top: 14rem; /* 224px */
space-x-60 > * + * | margin-left: 15rem; /* 240px */
space-y-60 > * + * | margin-top: 15rem; /* 240px */
space-x-64 > * + * | margin-left: 16rem; /* 256px */
space-y-64 > * + * | margin-top: 16rem; /* 256px */
space-x-72 > * + * | margin-left: 18rem; /* 288px */
space-y-72 > * + * | margin-top: 18rem; /* 288px */
space-x-80 > * + * | margin-left: 20rem; /* 320px */
space-y-80 > * + * | margin-top: 20rem; /* 320px */
space-x-96 > * + * | margin-left: 24rem; /* 384px */
space-y-96 > * + * | margin-top: 24rem; /* 384px */
space-x-px > * + * | margin-left: 1px;
space-y-px > * + * | margin-top: 1px;
space-y-reverse > * + * | --tw-space-y-reverse: 1;
space-x-reverse > * + * | --tw-space-x-reverse: 1;
