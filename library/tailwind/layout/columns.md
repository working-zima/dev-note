# Columns

엘리먼트 내의 열 수를 제어하는 유틸리티입니다.

## 기본 사용법

### 열 수에 따른 추가

`columns-2`와 `columns-3`과 같은 유틸리티를 사용하여 엘리먼트 내의 콘텐츠에 대해 생성할 열의 수를 설정합니다. 열의 너비는 해당 열 수에 맞게 자동으로 조정됩니다.

```html
<div class="columns-3 ...">
  <img class="w-full aspect-video ..." src="..." />
  <img class="w-full aspect-square ..." src="..." />
  <!-- ... -->
</div>
```

### 열 너비에 따른 추가

`columns-xs`와 `columns-sm`과 같은 유틸리티를 사용하여 엘리먼트 내 콘텐츠의 이상적인 열 너비를 설정합니다. 열의 수(개수)는 해당 값을 수용할 수 있도록 자동으로 조정됩니다.

이 "티셔츠" 스케일은 max-width 스케일과 동일하며, `2xs`와 `3xs`가 추가되어 더 작은 열을 사용하는 것이 유용할 수 있습니다.

```html
<div class="columns-3xs ...">
  <img class="w-full aspect-video ..." src="..." />
  <img class="w-full aspect-square ..." src="..." />
  <!-- ... -->
</div>
```

### 열 간격 설정

열 사이의 간격을 지정하려면 [gap-x](/docs/gap) 유틸리티를 사용할 수 있습니다.

```html
<div class="gap-8 columns-3 ...">
  <img class="w-full aspect-video ..." src="..." />
  <img class="w-full aspect-square ..." src="..." />
  <!-- ... -->
</div>
```

---

## 조건부 적용

### hover, focus 및 기타 상태

Tailwind에서는 상태 변형자를 사용하여 유틸리티 클래스를 다양한 상태에서 조건부로 적용할 수 있습니다. 예를 들어, `hover:columns-3`를 사용하여 hover 상태에서만 `columns-3` 유틸리티를 적용할 수 있습니다.

```html
<div class="columns-2 hover:columns-3">
  <!-- ... -->
</div>
```

Hover, Focus 및 기타 상태에 대한 모든 상태 변형자의 전체 목록은 해당 문서를 참조하십시오.

### 반응형 및 미디어 쿼리

변형자를 사용하여 반응형 화면 크기, 다크 모드, 축소된 동작 선호도 등을 대상으로 하는 미디어 쿼리를 설정할 수 있습니다. 예를 들어, `md:columns-3`를 사용하여 중간 화면 크기 이상에서만 `columns-3` 유틸리티를 적용할 수 있습니다.

```html
<div class="columns-2 md:columns-3">
  <!-- ... -->
</div>
```

반응형 디자인, 다크 모드 및 기타 미디어 쿼리 변형자에 대한 자세한 내용은 관련 문서를 참조하십시오.

## 사용자 정의 값 사용

### 테마 사용자 정의

기본적으로 Tailwind는 `1-12`의 열 수 스케일과 `3xs-7xl`의 열 티셔츠 스케일을 제공합니다. 이러한 값을 `tailwind.config.js` 파일에서 `theme.columns` 또는 `theme.extend.columns`를 편집하여 사용자 정의할 수 있습니다.

```diff-js
  module.exports = {
    theme: {
      extend: {
+       columns: {
+         '4xs': '14rem',
+       }
      },
    }
  }
```

### 임의 값

테마에 포함할 필요가 없는 임의의 `columns` 값을 사용해야 하는 경우, 대괄호를 사용하여 임의 값을 사용하여 속성을 즉시 생성할 수 있습니다.

```html
<div class="columns-[10rem]">
  <!-- ... -->
</div>
```

## Quick reference

Class | Properties
:-: | :-:
columns-1 | columns: 1;
columns-2 | columns: 2;
columns-3 | columns: 3;
columns-4 | columns: 4;
columns-5 | columns: 5;
columns-6 | columns: 6;
columns-7 | columns: 7;
columns-8 | columns: 8;
columns-9 | columns: 9;
columns-10 | columns: 10;
columns-11 | columns: 11;
columns-12 | columns: 12;
columns-auto | columns: auto;
columns-3xs | columns: 16rem; /* 256px */
columns-2xs | columns: 18rem; /* 288px */
columns-xs | columns: 20rem; /* 320px */
columns-sm | columns: 24rem; /* 384px */
columns-md | columns: 28rem; /* 448px */
columns-lg | columns: 32rem; /* 512px */
columns-xl | columns: 36rem; /* 576px */
columns-2xl | columns: 42rem; /* 672px */
columns-3xl | columns: 48rem; /* 768px */
columns-4xl | columns: 56rem; /* 896px */
columns-5xl | columns: 64rem; /* 1024px */
columns-6xl | columns: 72rem; /* 1152px */
columns-7xl | columns: 80rem; /* 1280px */
​
