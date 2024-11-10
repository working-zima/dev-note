# Margin

요소의 마진을 제어하기 위한 유틸리티입니다.

## 기본 사용법

### 한쪽에만 마진 추가하기

`mt-*`, `mr-*`, `mb-*`, `ml-*` 유틸리티를 사용하여 요소의 특정 한쪽에만 마진을 추가할 수 있습니다.

예를 들어, `mt-6`은 요소의 위쪽에 `1.5rem`의 마진을 추가하고, `mr-4`는 오른쪽에 `1rem`의 마진을, `mb-8`은 아래쪽에 `2rem`의 마진을, `ml-2`는 왼쪽에 `0.5rem`의 마진을 추가합니다.

```html
<div class="mt-6 ...">mt-6</div>
<div class="mr-4 ...">mr-4</div>
<div class="mb-8 ...">mb-8</div>
<div class="ml-2 ...">ml-2</div>
```

### 수평 마진 추가하기

`mx-*` 유틸리티를 사용해 요소의 좌우(수평) 마진을 제어할 수 있습니다.

```html
<div class="mx-8 ...">mx-8</div>
```

### 수직 마진 추가하기

`my-*` 유틸리티를 사용해 요소의 위아래(수직) 마진을 제어할 수 있습니다.

```html
<div class="my-8 ...">my-8</div>
```

### 모든 면에 마진 추가하기

`m-*` 유틸리티를 사용해 요소의 모든 면에 마진을 추가할 수 있습니다.

```html
<div class="m-8 ...">m-8</div>
```

### 음수 값 사용하기

음수 마진 값을 사용하려면 클래스 이름 앞에 -를 붙여서 음수 값을 만들 수 있습니다.

```html
<div class="w-36 h-16 bg-sky-400 opacity-20 ..."></div>
<div class="-mt-8 bg-sky-300 ...">-mt-8</div>
```

### 논리적 속성 사용하기

`ms-*`와 `me-*` 유틸리티를 사용해 `margin-inline-start`와 `margin-inline-end` 논리적 속성을 설정할 수 있습니다. 이는 텍스트 방향에 따라 왼쪽 또는 오른쪽에 적용됩니다.

```html
<div dir="ltr">
  <div class="ms-8 ...">ms-8</div>
  <div class="me-8 ...">me-8</div>
</div>

<div dir="rtl">
  <div class="ms-8 ...">ms-8</div>
  <div class="me-8 ...">me-8</div>
</div>
```

더욱 세밀한 제어를 위해 LTR 및 RTL 수정자를 사용해 현재 텍스트 방향에 따라 특정 스타일을 조건부로 적용할 수도 있습니다.

## 조건부 적용

### Hover, Focus 및 기타 상태

Tailwind는 상태별로 유틸리티 클래스를 조건부로 적용할 수 있는 변형(modifier)을 제공합니다. 예를 들어, `hover:mt-8`을 사용하면 hover 시에만 `mt-8` 유틸리티가 적용됩니다.

```html
<div class="mt-4 hover:mt-8">
  <!-- ... -->
</div>
```

사용 가능한 모든 상태 수정자는 Hover, Focus & Other States 문서를 참조하세요.

### 브레이크포인트와 미디어 쿼리

미디어 쿼리와 같은 브레이크포인트, 다크 모드, `prefers-reduced-motion` 등에 대해 조건부로 적용할 수 있는 변형을 사용할 수 있습니다. 예를 들어 `md:mt-8`을 사용하면 중간 화면 크기 이상에서만 `mt-8` 유틸리티가 적용됩니다.

```html
<div class="mt-4 md:mt-8">
  <!-- ... -->
</div>
```

자세한 내용은 Responsive Design, Dark Mode 및 기타 미디어 쿼리 변형 문서를 참조하세요.

## 사용자 정의 값 사용하기

### 테마 사용자 정의하기

기본적으로 Tailwind의 마진 크기 조정은 기본 간격 크기(Spacing Scale)를 따릅니다. `tailwind.config.js` 파일에서 `theme.spacing` 또는 `theme.extend.spacing`을 편집해 간격 크기를 사용자 정의할 수 있습니다.

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

또는 `theme.margin` 또는 `theme.extend.margin`을 편집해 마진 크기만 별도로 사용자 정의할 수도 있습니다.

```js
// tailwind.config.js

module.exports = {
  theme: {
    extend: {
      margin: {
        '5px': '5px',
      }
    }
  }
}
```

기본 테마 사용자 정의에 대한 자세한 내용은 Theme Customization 문서를 참조하세요.

### 임의 값 사용하기

테마에 포함할 필요가 없는 임의의 마진 값을 사용해야 할 경우, 대괄호([])를 사용해 원하는 값을 즉석에서 설정할 수 있습니다.

```html
<div class="m-[5px]">
  <!-- ... -->
</div>
```

## Quick reference

Class | Properties
:-: | :-:
m-0 | margin: 0px;
mx-0 | margin-left: 0px; <br/> margin-right: 0px;
my-0 | margin-top: 0px; <br/> margin-bottom: 0px;
ms-0 | margin-inline-start: 0px;
me-0 | margin-inline-end: 0px;
mt-0 | margin-top: 0px;
mr-0 | margin-right: 0px;
mb-0 | margin-bottom: 0px;
ml-0 | margin-left: 0px;
m-px | margin: 1px;
mx-px | margin-left: 1px; <br/> margin-right: 1px;
my-px | margin-top: 1px; <br/> margin-bottom: 1px;
ms-px | margin-inline-start: 1px;
me-px | margin-inline-end: 1px;
mt-px | margin-top: 1px;
mr-px | margin-right: 1px;
mb-px | margin-bottom: 1px;
ml-px | margin-left: 1px;
m-0.5 | margin: 0.125rem; /*2px*/
mx-0.5 | margin-left: 0.125rem; /*2px */ <br/> margin-right: 0.125rem; /* 2px*/
my-0.5 | margin-top: 0.125rem; /*2px */ <br/> margin-bottom: 0.125rem; /* 2px*/
ms-0.5 | margin-inline-start: 0.125rem; /*2px*/
me-0.5 | margin-inline-end: 0.125rem; /*2px*/
mt-0.5 | margin-top: 0.125rem; /*2px*/
mr-0.5 | margin-right: 0.125rem; /*2px*/
mb-0.5 | margin-bottom: 0.125rem; /*2px*/
ml-0.5 | margin-left: 0.125rem; /*2px*/
m-1 | margin: 0.25rem; /*4px*/
mx-1 | margin-left: 0.25rem; /*4px */ <br/> margin-right: 0.25rem; /* 4px*/
my-1 | margin-top: 0.25rem; /*4px */ <br/> margin-bottom: 0.25rem; /* 4px*/
ms-1 | margin-inline-start: 0.25rem; /*4px*/
me-1 | margin-inline-end: 0.25rem; /*4px*/
mt-1 | margin-top: 0.25rem; /*4px*/
mr-1 | margin-right: 0.25rem; /*4px*/
mb-1 | margin-bottom: 0.25rem; /*4px*/
ml-1 | margin-left: 0.25rem; /*4px*/
m-1.5 | margin: 0.375rem; /*6px*/
mx-1.5 | margin-left: 0.375rem; /*6px */ <br/> margin-right: 0.375rem; /* 6px*/
my-1.5 | margin-top: 0.375rem; /*6px */ <br/> margin-bottom: 0.375rem; /* 6px*/
ms-1.5 | margin-inline-start: 0.375rem; /*6px*/
me-1.5 | margin-inline-end: 0.375rem; /*6px*/
mt-1.5 | margin-top: 0.375rem; /*6px*/
mr-1.5 | margin-right: 0.375rem; /*6px*/
mb-1.5 | margin-bottom: 0.375rem; /*6px*/
ml-1.5 | margin-left: 0.375rem; /*6px*/
m-2 | margin: 0.5rem; /*8px*/
mx-2 | margin-left: 0.5rem; /*8px*/ <br/> margin-right: 0.5rem; /*8px*/
my-2 | margin-top: 0.5rem; /*8px*/ <br/> margin-bottom: 0.5rem; /*8px*/
ms-2 | margin-inline-start: 0.5rem; /*8px*/
me-2 | margin-inline-end: 0.5rem; /*8px*/
mt-2 | margin-top: 0.5rem; /*8px*/
mr-2 | margin-right: 0.5rem; /*8px*/
mb-2 | margin-bottom: 0.5rem; /*8px*/
ml-2 | margin-left: 0.5rem; /*8px*/
m-2.5 | margin: 0.625rem; /*10px*/
mx-2.5 | margin-left: 0.625rem; /*10px*/ <br/> margin-right: 0.625rem; /*10px*/
my-2.5 | margin-top: 0.625rem; /*10px*/ <br/> margin-bottom: 0.625rem; /*10px*/
ms-2.5 | margin-inline-start: 0.625rem; /*10px*/
me-2.5 | margin-inline-end: 0.625rem; /*10px*/
mt-2.5 | margin-top: 0.625rem; /*10px*/
mr-2.5 | margin-right: 0.625rem; /*10px*/
mb-2.5 | margin-bottom: 0.625rem; /*10px*/
ml-2.5 | margin-left: 0.625rem; /*10px*/
m-3 | margin: 0.75rem; /*12px*/
mx-3 | margin-left: 0.75rem; /*12px*/ <br/> margin-right: 0.75rem; /*12px*/
my-3 | margin-top: 0.75rem; /*12px*/ <br/> margin-bottom: 0.75rem; /*12px*/
ms-3 | margin-inline-start: 0.75rem; /*12px*/
me-3 | margin-inline-end: 0.75rem; /*12px*/
mt-3 | margin-top: 0.75rem; /*12px*/
mr-3 | margin-right: 0.75rem; /*12px*/
mb-3 | margin-bottom: 0.75rem; /*12px*/
ml-3 | margin-left: 0.75rem; /*12px*/
m-3.5 | margin: 0.875rem; /*14px*/
mx-3.5 | margin-left: 0.875rem; /*14px*/ <br/> margin-right: 0.875rem; /*14px*/
my-3.5 | margin-top: 0.875rem; /*14px*/ <br/> margin-bottom: 0.875rem; /*14px*/
ms-3.5 | margin-inline-start: 0.875rem; /*14px*/
me-3.5 | margin-inline-end: 0.875rem; /*14px*/
mt-3.5 | margin-top: 0.875rem; /*14px*/
mr-3.5 | margin-right: 0.875rem; /*14px*/
mb-3.5 | margin-bottom: 0.875rem; /*14px*/
ml-3.5 | margin-left: 0.875rem; /*14px*/
m-4 | margin: 1rem; /*16px*/
mx-4 | margin-left: 1rem; /*16px*/ <br/> margin-right: 1rem; /*16px*/
my-4 | margin-top: 1rem; /*16px*/ <br/> margin-bottom: 1rem; /*16px*/
ms-4 | margin-inline-start: 1rem; /*16px*/
me-4 | margin-inline-end: 1rem; /*16px*/
mt-4 | margin-top: 1rem; /*16px*/
mr-4 | margin-right: 1rem; /*16px*/
mb-4 | margin-bottom: 1rem; /*16px*/
ml-4 | margin-left: 1rem; /*16px*/
m-5 | margin: 1.25rem; /*20px*/
mx-5 | margin-left: 1.25rem; /*20px*/ <br/> margin-right: 1.25rem; /*20px*/
my-5 | margin-top: 1.25rem; /*20px*/ <br/> margin-bottom: 1.25rem; /*20px*/
ms-5 | margin-inline-start: 1.25rem; /*20px*/
me-5 | margin-inline-end: 1.25rem; /*20px*/
mt-5 | margin-top: 1.25rem; /*20px*/
mr-5 | margin-right: 1.25rem; /*20px*/
mb-5 | margin-bottom: 1.25rem; /*20px*/
ml-5 | margin-left: 1.25rem; /*20px*/
m-6 | margin: 1.5rem; /*24px*/
mx-6 | margin-left: 1.5rem; /*24px*/ <br/> margin-right: 1.5rem; /*24px*/
my-6 | margin-top: 1.5rem; /*24px*/ <br/> margin-bottom: 1.5rem; /*24px*/
ms-6 | margin-inline-start: 1.5rem; /*24px*/
me-6 | margin-inline-end: 1.5rem; /*24px*/
mt-6 | margin-top: 1.5rem; /*24px*/
mr-6 | margin-right: 1.5rem; /*24px*/
mb-6 | margin-bottom: 1.5rem; /*24px*/
ml-6 | margin-left: 1.5rem; /*24px*/
m-7 | margin: 1.75rem; /*28px*/
mx-7 | margin-left: 1.75rem; /*28px*/ <br/> margin-right: 1.75rem; /*28px*/
my-7 | margin-top: 1.75rem; /*28px*/ <br/> margin-bottom: 1.75rem; /*28px*/
ms-7 | margin-inline-start: 1.75rem; /*28px*/
me-7 | margin-inline-end: 1.75rem; /*28px*/
mt-7 | margin-top: 1.75rem; /*28px*/
mr-7 | margin-right: 1.75rem; /*28px*/
mb-7 | margin-bottom: 1.75rem; /*28px*/
ml-7 | margin-left: 1.75rem; /*28px*/
m-8 | margin: 2rem; /*32px*/
mx-8 | margin-left: 2rem; /*32px*/ <br/> margin-right: 2rem; /*32px*/
my-8 | margin-top: 2rem; /*32px*/ <br/> margin-bottom: 2rem; /*32px*/
ms-8 | margin-inline-start: 2rem; /*32px*/
me-8 | margin-inline-end: 2rem; /*32px*/
mt-8 | margin-top: 2rem; /*32px*/
mr-8 | margin-right: 2rem; /*32px*/
mb-8 | margin-bottom: 2rem; /*32px*/
ml-8 | margin-left: 2rem; /*32px*/
m-9 | margin: 2.25rem; /*36px*/
mx-9 | margin-left: 2.25rem; /*36px*/ <br/> margin-right: 2.25rem; /*36px*/
my-9 | margin-top: 2.25rem; /*36px*/ <br/> margin-bottom: 2.25rem; /*36px*/
ms-9 | margin-inline-start: 2.25rem; /*36px*/
me-9 | margin-inline-end: 2.25rem; /*36px*/
mt-9 | margin-top: 2.25rem; /*36px*/
mr-9 | margin-right: 2.25rem; /*36px*/
mb-9 | margin-bottom: 2.25rem; /*36px*/
ml-9 | margin-left: 2.25rem; /*36px*/
m-10 | margin: 2.5rem; /*40px*/
mx-10 | margin-left: 2.5rem; /*40px*/ <br/> margin-right: 2.5rem; /*40px*/
my-10 | margin-top: 2.5rem; /*40px*/ <br/> margin-bottom: 2.5rem; /*40px*/
ms-10 | margin-inline-start: 2.5rem; /*40px*/
me-10 | margin-inline-end: 2.5rem; /*40px*/
mt-10 | margin-top: 2.5rem; /*40px*/
mr-10 | margin-right: 2.5rem; /*40px*/
mb-10 | margin-bottom: 2.5rem; /*40px*/
ml-10 | margin-left: 2.5rem; /*40px*/
m-11 | margin: 2.75rem; /*44px*/
mx-11 | margin-left: 2.75rem; /*44px*/ <br/> margin-right: 2.75rem; /*44px*/
my-11 | margin-top: 2.75rem; /*44px*/ <br/> margin-bottom: 2.75rem; /*44px*/
ms-11 | margin-inline-start: 2.75rem; /*44px*/
me-11 | margin-inline-end: 2.75rem; /*44px*/
mt-11 | margin-top: 2.75rem; /*44px*/
mr-11 | margin-right: 2.75rem; /*44px*/
mb-11 | margin-bottom: 2.75rem; /*44px*/
ml-11 | margin-left: 2.75rem; /*44px*/
m-12 | margin: 3rem; /*48px*/
mx-12 | margin-left: 3rem; /*48px*/ <br/> margin-right: 3rem; /*48px*/
my-12 | margin-top: 3rem; /*48px*/ <br/> margin-bottom: 3rem; /*48px*/
ms-12 | margin-inline-start: 3rem; /*48px*/
me-12 | margin-inline-end: 3rem; /*48px*/
mt-12 | margin-top: 3rem; /*48px*/
mr-12 | margin-right: 3rem; /*48px*/
mb-12 | margin-bottom: 3rem; /*48px*/
ml-12 | margin-left: 3rem; /*48px*/
m-14 | margin: 3.5rem; /*56px*/
mx-14 | margin-left: 3.5rem; /*56px*/ <br/> margin-right: 3.5rem; /*56px*/
my-14 | margin-top: 3.5rem; /*56px*/ <br/> margin-bottom: 3.5rem; /*56px*/
ms-14 | margin-inline-start: 3.5rem; /*56px*/
me-14 | margin-inline-end: 3.5rem; /*56px*/
mt-14 | margin-top: 3.5rem; /*56px*/
mr-14 | margin-right: 3.5rem; /*56px*/
mb-14 | margin-bottom: 3.5rem; /*56px*/
ml-14 | margin-left: 3.5rem; /*56px*/
m-16 | margin: 4rem; /*64px*/
mx-16 | margin-left: 4rem; /*64px*/ <br/> margin-right: 4rem; /*64px*/
my-16 | margin-top: 4rem; /*64px*/ <br/> margin-bottom: 4rem; /*64px*/
ms-16 | margin-inline-start: 4rem; /*64px*/
me-16 | margin-inline-end: 4rem; /*64px*/
mt-16 | margin-top: 4rem; /*64px*/
mr-16 | margin-right: 4rem; /*64px*/
mb-16 | margin-bottom: 4rem; /*64px*/
ml-16 | margin-left: 4rem; /*64px*/
m-20 | margin: 5rem; /*80px*/
mx-20 | margin-left: 5rem; /*80px*/ <br/> margin-right: 5rem; /*80px*/
my-20 | margin-top: 5rem; /*80px*/ <br/> margin-bottom: 5rem; /*80px*/
ms-20 | margin-inline-start: 5rem; /*80px*/
me-20 | margin-inline-end: 5rem; /*80px*/
mt-20 | margin-top: 5rem; /*80px*/
mr-20 | margin-right: 5rem; /*80px*/
mb-20 | margin-bottom: 5rem; /*80px*/
ml-20 | margin-left: 5rem; /*80px*/
m-24 | margin: 6rem; /*96px*/
mx-24 | margin-left: 6rem; /*96px*/ <br/> margin-right: 6rem; /*96px*/
my-24 | margin-top: 6rem; /*96px*/ <br/> margin-bottom: 6rem; /*96px*/
ms-24 | margin-inline-start: 6rem; /*96px*/
me-24 | margin-inline-end: 6rem; /*96px*/
mt-24 | margin-top: 6rem; /*96px*/
mr-24 | margin-right: 6rem; /*96px*/
mb-24 | margin-bottom: 6rem; /*96px*/
ml-24 | margin-left: 6rem; /*96px*/
m-28 | margin: 7rem; /*112px*/
mx-28 | margin-left: 7rem; /*112px*/ <br/> margin-right: 7rem; /*112px*/
my-28 | margin-top: 7rem; /*112px*/ <br/> margin-bottom: 7rem; /*112px*/
ms-28 | margin-inline-start: 7rem; /*112px*/
me-28 | margin-inline-end: 7rem; /*112px*/
mt-28 | margin-top: 7rem; /*112px*/
mr-28 | margin-right: 7rem; /*112px*/
mb-28 | margin-bottom: 7rem; /*112px*/
ml-28 | margin-left: 7rem; /*112px*/
m-32 | margin: 8rem; /*128px*/
mx-32 | margin-left: 8rem; /*128px*/ <br/> margin-right: 8rem; /*128px*/
my-32 | margin-top: 8rem; /*128px*/ <br/> margin-bottom: 8rem; /*128px*/
ms-32 | margin-inline-start: 8rem; /*128px*/
me-32 | margin-inline-end: 8rem; /*128px*/
mt-32 | margin-top: 8rem; /*128px*/
mr-32 | margin-right: 8rem; /*128px*/
mb-32 | margin-bottom: 8rem; /*128px*/
ml-32 | margin-left: 8rem; /*128px*/
m-36 | margin: 9rem; /*144px*/
mx-36 | margin-left: 9rem; /*144px*/ <br/> margin-right: 9rem; /*144px*/
my-36 | margin-top: 9rem; /*144px*/ <br/> margin-bottom: 9rem; /*144px*/
ms-36 | margin-inline-start: 9rem; /*144px*/
me-36 | margin-inline-end: 9rem; /*144px*/
mt-36 | margin-top: 9rem; /*144px*/
mr-36 | margin-right: 9rem; /*144px*/
mb-36 | margin-bottom: 9rem; /*144px*/
ml-36 | margin-left: 9rem; /*144px*/
m-40 | margin: 10rem; /*160px*/
mx-40 | margin-left: 10rem; /*160px*/ <br/> margin-right: 10rem; /*160px*/
my-40 | margin-top: 10rem; /*160px*/ <br/> margin-bottom: 10rem; /*160px*/
ms-40 | margin-inline-start: 10rem; /*160px*/
me-40 | margin-inline-end: 10rem; /*160px*/
mt-40 | margin-top: 10rem; /*160px*/
mr-40 | margin-right: 10rem; /*160px*/
mb-40 | margin-bottom: 10rem; /*160px*/
ml-40 | margin-left: 10rem; /*160px*/
m-44 | margin: 11rem; /*176px*/
mx-44 | margin-left: 11rem; /*176px*/ <br/> margin-right: 11rem; /*176px*/
my-44 | margin-top: 11rem; /*176px*/ <br/> margin-bottom: 11rem; /*176px*/
ms-44 | margin-inline-start: 11rem; /*176px*/
me-44 | margin-inline-end: 11rem; /*176px*/
mt-44 | margin-top: 11rem; /*176px*/
mr-44 | margin-right: 11rem; /*176px*/
mb-44 | margin-bottom: 11rem; /*176px*/
ml-44 | margin-left: 11rem; /*176px*/
m-48 | margin: 12rem; /*192px*/
mx-48 | margin-left: 12rem; /*192px*/ <br/> margin-right: 12rem; /*192px*/
my-48 | margin-top: 12rem; /*192px*/ <br/> margin-bottom: 12rem; /*192px*/
ms-48 | margin-inline-start: 12rem; /*192px*/
me-48 | margin-inline-end: 12rem; /*192px*/
mt-48 | margin-top: 12rem; /*192px*/
mr-48 | margin-right: 12rem; /*192px*/
mb-48 | margin-bottom: 12rem; /*192px*/
ml-48 | margin-left: 12rem; /*192px*/
m-52 | margin: 13rem; /*208px*/
mx-52 | margin-left: 13rem; /*208px*/ <br/> margin-right: 13rem; /*208px*/
my-52 | margin-top: 13rem; /*208px*/ <br/> margin-bottom: 13rem; /*208px*/
ms-52 | margin-inline-start: 13rem; /*208px*/
me-52 | margin-inline-end: 13rem; /*208px*/
mt-52 | margin-top: 13rem; /*208px*/
mr-52 | margin-right: 13rem; /*208px*/
mb-52 | margin-bottom: 13rem; /*208px*/
ml-52 | margin-left: 13rem; /*208px*/
m-56 | margin: 14rem; /*224px*/
mx-56 | margin-left: 14rem; /*224px*/ <br/> margin-right: 14rem; /*224px*/
my-56 | margin-top: 14rem; /*224px*/ <br/> margin-bottom: 14rem; /*224px*/
ms-56 | margin-inline-start: 14rem; /*224px*/
me-56 | margin-inline-end: 14rem; /*224px*/
mt-56 | margin-top: 14rem; /*224px*/
mr-56 | margin-right: 14rem; /*224px*/
mb-56 | margin-bottom: 14rem; /*224px*/
ml-56 | margin-left: 14rem; /*224px*/
m-60 | margin: 15rem; /*240px*/
mx-60 | margin-left: 15rem; /*240px*/ <br/> margin-right: 15rem; /*240px*/
my-60 | margin-top: 15rem; /*240px*/ <br/> margin-bottom: 15rem; /*240px*/
ms-60 | margin-inline-start: 15rem; /*240px*/
me-60 | margin-inline-end: 15rem; /*240px*/
mt-60 | margin-top: 15rem; /*240px*/
mr-60 | margin-right: 15rem; /*240px*/
mb-60 | margin-bottom: 15rem; /*240px*/
ml-60 | margin-left: 15rem; /*240px*/
m-64 | margin: 16rem; /*256px*/
mx-64 | margin-left: 16rem; /*256px*/ <br/> margin-right: 16rem; /*256px*/
my-64 | margin-top: 16rem; /*256px*/ <br/> margin-bottom: 16rem; /*256px*/
ms-64 | margin-inline-start: 16rem; /*256px*/
me-64 | margin-inline-end: 16rem; /*256px*/
mt-64 | margin-top: 16rem; /*256px*/
mr-64 | margin-right: 16rem; /*256px*/
mb-64 | margin-bottom: 16rem; /*256px*/
ml-64 | margin-left: 16rem; /*256px*/
m-72 | margin: 18rem; /*288px*/
mx-72 | margin-left: 18rem; /*288px*/ <br/> margin-right: 18rem; /*288px*/
my-72 | margin-top: 18rem; /*288px*/ <br/> margin-bottom: 18rem; /*288px*/
ms-72 | margin-inline-start: 18rem; /*288px*/
me-72 | margin-inline-end: 18rem; /*288px*/
mt-72 | margin-top: 18rem; /*288px*/
mr-72 | margin-right: 18rem; /*288px*/
mb-72 | margin-bottom: 18rem; /*288px*/
ml-72 | margin-left: 18rem; /*288px*/
m-80 | margin: 20rem; /*320px*/
mx-80 | margin-left: 20rem; /*320px*/ <br/> margin-right: 20rem; /*320px*/
my-80 | margin-top: 20rem; /*320px*/ <br/> margin-bottom: 20rem; /*320px*/
ms-80 | margin-inline-start: 20rem; /*320px*/
me-80 | margin-inline-end: 20rem; /*320px*/
mt-80 | margin-top: 20rem; /*320px*/
mr-80 | margin-right: 20rem; /*320px*/
mb-80 | margin-bottom: 20rem; /*320px*/
ml-80 | margin-left: 20rem; /*320px*/
m-96 | margin: 24rem; /*384px*/
mx-96 | margin-left: 24rem; /*384px*/ <br/> margin-right: 24rem; /*384px*/
my-96 | margin-top: 24rem; /*384px*/ <br/> margin-bottom: 24rem; /*384px*/
ms-96 | margin-inline-start: 24rem; /*384px*/
me-96 | margin-inline-end: 24rem; /*384px*/
mt-96 | margin-top: 24rem; /*384px*/
mr-96 | margin-right: 24rem; /*384px*/
mb-96 | margin-bottom: 24rem; /*384px*/
ml-96 | margin-left: 24rem; /*384px*/
m-auto | margin: auto;
mx-auto | margin-left: auto; <br/> margin-right: auto;
my-auto | margin-top: auto; <br/> margin-bottom: auto;
ms-auto | margin-inline-start: auto;
me-auto | margin-inline-end: auto;
mt-auto | margin-top: auto;
mr-auto | margin-right: auto;
mb-auto | margin-bottom: auto;
ml-auto | margin-left: auto;
