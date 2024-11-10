# Padding

요소의 패딩을 제어하기 위한 유틸리티입니다.

## 기본 사용법

### 한쪽에만 패딩 추가하기

`pt-*`, `pr-*`, `pb-*`, `pl-*` 유틸리티를 사용해 요소의 한쪽에만 패딩을 적용할 수 있습니다.

예를 들어, `pt-6`는 요소의 위쪽에 `1.5rem`의 패딩을 추가하고, `pr-4`는 오른쪽에 `1rem`의 패딩을, `pb-8`은 아래쪽에 `2rem`의 패딩을, `pl-2`는 왼쪽에 `0.5rem`의 패딩을 추가합니다.

```html
<div class="pt-6 ...">pt-6</div>
<div class="pr-4 ...">pr-4</div>
<div class="pb-8 ...">pb-8</div>
<div class="pl-2 ...">pl-2</div>
```

### 수평 패딩 추가하기

`px-*` 유틸리티를 사용해 요소의 좌우(수평) 패딩을 제어할 수 있습니다.

```html
<div class="px-8 ...">px-8</div>
```

### 수직 패딩 추가하기

`py-*` 유틸리티를 사용해 요소의 위아래(수직) 패딩을 제어할 수 있습니다.

```html
<div class="py-8 ...">py-8</div>
```

### 모든 면에 패딩 추가하기

`p-*` 유틸리티를 사용해 요소의 모든 면에 패딩을 추가할 수 있습니다.

```html
<div class="p-8 ...">p-8</div>
```

### 논리적 속성 사용하기

`ps-*`와 `pe-*` 유틸리티를 사용해 `padding-inline-start`와 `padding-inline-end` 논리적 속성을 설정할 수 있습니다. 이는 텍스트 방향에 따라 왼쪽 또는 오른쪽으로 적용됩니다.

```html
<div dir="ltr">
  <div class="ps-8 ...">ps-8</div>
  <div class="pe-8 ...">pe-8</div>
</div>

<div dir="rtl">
  <div class="ps-8 ...">ps-8</div>
  <div class="pe-8 ...">pe-8</div>
</div>
```

더욱 세밀하게 제어하기 위해, LTR 및 RTL 수정자를 사용해 현재 텍스트 방향에 따라 특정 스타일을 조건부로 적용할 수도 있습니다.

## 조건부 적용

### Hover, Focus 및 기타 상태

Tailwind는 상태별로 유틸리티 클래스를 조건부로 적용할 수 있는 변형(modifier)을 제공합니다. 예를 들어 `hover:py-8`을 사용하면 hover 시에만 `py-8` 유틸리티가 적용됩니다.

```html
<div class="py-4 hover:py-8">
  <!-- ... -->
</div>
```

사용 가능한 모든 상태 수정자의 목록은 Hover, Focus & Other States 문서를 참조하세요.

브레이크포인트와 미디어 쿼리
미디어 쿼리와 같은 브레이크포인트, 다크 모드, prefers-reduced-motion 등에 대해 조건부로 적용할 수 있는 변형도 사용할 수 있습니다. 예를 들어, md:py-8을 사용하면 중간 화면 크기 이상에서만 py-8 유틸리티가 적용됩니다.

```html
<div class="py-4 md:py-8">
  <!-- ... -->
</div>
```

자세한 내용은 Responsive Design, Dark Mode 및 기타 미디어 쿼리 변형 문서를 참조하세요.

사용자 지정 값 사용하기
테마 사용자 정의하기
기본적으로 Tailwind의 패딩 크기 조정은 기본 간격 크기(Spacing Scale)를 따릅니다. tailwind.config.js 파일에서 theme.spacing 또는 theme.extend.spacing을 편집해 간격 크기를 사용자 정의할 수 있습니다.

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

또는 theme.padding 또는 theme.extend.padding을 편집해 패딩 크기만 별도로 사용자 정의할 수도 있습니다.

```js
// tailwind.config.js

module.exports = {
  theme: {
    extend: {
      padding: {
        '5px': '5px',
      }
    }
  }
}
```

기본 테마 사용자 정의에 대한 자세한 내용은 Theme Customization 문서를 참조하세요.

임의 값 사용하기
테마에 포함할 필요가 없는 임의의 패딩 값을 사용해야 할 경우, 대괄호([])를 사용해 원하는 값을 즉석에서 설정할 수 있습니다.

```html
<div class="p-[5px]">
  <!-- ... -->
</div>
```

임의 값 지원에 대한 자세한 내용은 Arbitrary Values 문서를 참조하세요.

## Quick reference

Class  |  Properties
:-: | :-:
p-0 | padding: 0px;
px-0 | padding-left: 0px; <br/> padding-right: 0px;
py-0 | padding-top: 0px; <br/> padding-bottom: 0px;
ps-0 | padding-inline-start: 0px;
pe-0 | padding-inline-end: 0px;
pt-0 | padding-top: 0px;
pr-0 | padding-right: 0px;
pb-0 | padding-bottom: 0px;
pl-0 | padding-left: 0px;
p-px | padding: 1px;
px-px | padding-left: 1px; <br/> padding-right: 1px;
py-px | padding-top: 1px; <br/> padding-bottom: 1px;
ps-px | padding-inline-start: 1px;
pe-px | padding-inline-end: 1px;
pt-px | padding-top: 1px;
pr-px | padding-right: 1px;
pb-px | padding-bottom: 1px;
pl-px | padding-left: 1px;
p-0.5 | padding: 0.125rem; /*2px*/
px-0.5 | padding-left: 0.125rem; /*2px */ <br/> padding-right: 0.125rem; /* 2px*/
py-0.5 | padding-top: 0.125rem; /*2px */ <br/> padding-bottom: 0.125rem; /* 2px*/
ps-0.5 | padding-inline-start: 0.125rem; /*2px*/
pe-0.5 | padding-inline-end: 0.125rem; /*2px*/
pt-0.5 | padding-top: 0.125rem; /*2px*/
pr-0.5 | padding-right: 0.125rem; /*2px*/
pb-0.5 | padding-bottom: 0.125rem; /*2px*/
pl-0.5 | padding-left: 0.125rem; /*2px*/
p-1 | padding: 0.25rem; /*4px*/
px-1 | padding-left: 0.25rem; /*4px */ <br/> padding-right: 0.25rem; /* 4px*/
py-1 | padding-top: 0.25rem; /*4px */ <br/> padding-bottom: 0.25rem; /* 4px*/
ps-1 | padding-inline-start: 0.25rem; /*4px*/
pe-1 | padding-inline-end: 0.25rem; /*4px*/
pt-1 | padding-top: 0.25rem; /*4px*/
pr-1 | padding-right: 0.25rem; /*4px*/
pb-1 | padding-bottom: 0.25rem; /*4px*/
pl-1 | padding-left: 0.25rem; /*4px*/
p-1.5 | padding: 0.375rem; /*6px*/
px-1.5 | padding-left: 0.375rem; /*6px */ <br/> padding-right: 0.375rem; /* 6px*/
py-1.5 | padding-top: 0.375rem; /*6px */ <br/> padding-bottom: 0.375rem; /* 6px*/
ps-1.5 | padding-inline-start: 0.375rem; /*6px*/
pe-1.5 | padding-inline-end: 0.375rem; /*6px*/
pt-1.5 | padding-top: 0.375rem; /*6px*/
pr-1.5 | padding-right: 0.375rem; /*6px*/
pb-1.5 | padding-bottom: 0.375rem; /*6px*/
pl-1.5 | padding-left: 0.375rem; /*6px*/
p-2 | padding: 0.5rem; /*8px*/
px-2 | padding-left: 0.5rem; /*8px */ <br/> padding-right: 0.5rem; /* 8px*/
py-2 | padding-top: 0.5rem; /*8px */ <br/> padding-bottom: 0.5rem; /* 8px*/
ps-2 | padding-inline-start: 0.5rem; /*8px*/
pe-2 | padding-inline-end: 0.5rem; /*8px*/
pt-2 | padding-top: 0.5rem; /*8px*/
pr-2 | padding-right: 0.5rem; /*8px*/
pb-2 | padding-bottom: 0.5rem; /*8px*/
pl-2 | padding-left: 0.5rem; /*8px*/
p-2.5 | padding: 0.625rem; /*10px*/
px-2.5 | padding-left: 0.625rem; /*10px */ <br/> padding-right: 0.625rem; /* 10px*/
py-2.5 | padding-top: 0.625rem; /*10px */ <br/> padding-bottom: 0.625rem; /* 10px*/
ps-2.5 | padding-inline-start: 0.625rem; /*10px*/
pe-2.5 | padding-inline-end: 0.625rem; /*10px*/
pt-2.5 | padding-top: 0.625rem; /*10px*/
pr-2.5 | padding-right: 0.625rem; /*10px*/
pb-2.5 | padding-bottom: 0.625rem; /*10px*/
pl-2.5 | padding-left: 0.625rem; /*10px*/
p-3 | padding: 0.75rem; /*12px*/
px-3 | padding-left: 0.75rem; /*12px */ <br/> padding-right: 0.75rem; /* 12px*/
py-3 | padding-top: 0.75rem; /*12px */ <br/> padding-bottom: 0.75rem; /* 12px*/
ps-3 | padding-inline-start: 0.75rem; /*12px*/
pe-3 | padding-inline-end: 0.75rem; /*12px*/
pt-3 | padding-top: 0.75rem; /*12px*/
pr-3 | padding-right: 0.75rem; /*12px*/
pb-3 | padding-bottom: 0.75rem; /*12px*/
pl-3 | padding-left: 0.75rem; /*12px*/
p-3.5 | padding: 0.875rem; /*14px*/
px-3.5 | padding-left: 0.875rem; /*14px */ <br/> padding-right: 0.875rem; /* 14px*/
py-3.5 | padding-top: 0.875rem; /*14px */ <br/> padding-bottom: 0.875rem; /* 14px*/
ps-3.5 | padding-inline-start: 0.875rem; /*14px*/
pe-3.5 | padding-inline-end: 0.875rem; /*14px*/
pt-3.5 | padding-top: 0.875rem; /*14px*/
pr-3.5 | padding-right: 0.875rem; /*14px*/
pb-3.5 | padding-bottom: 0.875rem; /*14px*/
pl-3.5 | padding-left: 0.875rem; /*14px*/
p-4 | padding: 1rem; /*16px*/
px-4 | padding-left: 1rem; /*16px */ <br/> padding-right: 1rem; /* 16px*/
py-4 | padding-top: 1rem; /*16px */ <br/> padding-bottom: 1rem; /* 16px*/
ps-4 | padding-inline-start: 1rem; /*16px*/
pe-4 | padding-inline-end: 1rem; /*16px*/
pt-4 | padding-top: 1rem; /*16px*/
pr-4 | padding-right: 1rem; /*16px*/
pb-4 | padding-bottom: 1rem; /*16px*/
pl-4 | padding-left: 1rem; /*16px*/
p-5 | padding: 1.25rem; /*20px*/
px-5 | padding-left: 1.25rem; /*20px */ <br/> padding-right: 1.25rem; /* 20px*/
py-5 | padding-top: 1.25rem; /*20px */ <br/> padding-bottom: 1.25rem; /* 20px*/
ps-5 | padding-inline-start: 1.25rem; /*20px*/
pe-5 | padding-inline-end: 1.25rem; /*20px*/
pt-5 | padding-top: 1.25rem; /*20px*/
pr-5 | padding-right: 1.25rem; /*20px*/
pb-5 | padding-bottom: 1.25rem; /*20px*/
pl-5 | padding-left: 1.25rem; /*20px*/
p-6 | padding: 1.5rem; /*24px*/
px-6 | padding-left: 1.5rem; /*24px */ <br/> padding-right: 1.5rem; /* 24px*/
py-6 | padding-top: 1.5rem; /*24px */ <br/> padding-bottom: 1.5rem; /* 24px*/
ps-6 | padding-inline-start: 1.5rem; /*24px*/
pe-6 | padding-inline-end: 1.5rem; /*24px*/
pt-6 | padding-top: 1.5rem; /*24px*/
pr-6 | padding-right: 1.5rem; /*24px*/
pb-6 | padding-bottom: 1.5rem; /*24px*/
pl-6 | padding-left: 1.5rem; /*24px*/
p-7 | padding: 1.75rem; /*28px*/
px-7 | padding-left: 1.75rem; /*28px */ <br/> padding-right: 1.75rem; /* 28px*/
py-7 | padding-top: 1.75rem; /*28px */ <br/> padding-bottom: 1.75rem; /* 28px*/
ps-7 | padding-inline-start: 1.75rem; /*28px*/
pe-7 | padding-inline-end: 1.75rem; /*28px*/
pt-7 | padding-top: 1.75rem; /*28px*/
pr-7 | padding-right: 1.75rem; /*28px*/
pb-7 | padding-bottom: 1.75rem; /*28px*/
pl-7 | padding-left: 1.75rem; /*28px*/
p-8 | padding: 2rem; /*32px*/
px-8 | padding-left: 2rem; /*32px */ <br/> padding-right: 2rem; /* 32px*/
py-8 | padding-top: 2rem; /*32px */ <br/> padding-bottom: 2rem; /* 32px*/
ps-8 | padding-inline-start: 2rem; /*32px*/
pe-8 | padding-inline-end: 2rem; /*32px*/
pt-8 | padding-top: 2rem; /*32px*/
pr-8 | padding-right: 2rem; /*32px*/
pb-8 | padding-bottom: 2rem; /*32px*/
pl-8 | padding-left: 2rem; /*32px*/
p-9 | padding: 2.25rem; /*36px*/
px-9 | padding-left: 2.25rem; /*36px */ <br/> padding-right: 2.25rem; /* 36px*/
py-9 | padding-top: 2.25rem; /*36px */ <br/> padding-bottom: 2.25rem; /* 36px*/
ps-9 | padding-inline-start: 2.25rem; /*36px*/
pe-9 | padding-inline-end: 2.25rem; /*36px*/
pt-9 | padding-top: 2.25rem; /*36px*/
pr-9 | padding-right: 2.25rem; /*36px*/
pb-9 | padding-bottom: 2.25rem; /*36px*/
pl-9 | padding-left: 2.25rem; /*36px*/
p-10 | padding: 2.5rem; /*40px*/
px-10 | padding-left: 2.5rem; /*40px */ <br/> padding-right: 2.5rem; /* 40px*/
py-10 | padding-top: 2.5rem; /*40px */ <br/> padding-bottom: 2.5rem; /* 40px*/
ps-10 | padding-inline-start: 2.5rem; /*40px*/
pe-10 | padding-inline-end: 2.5rem; /*40px*/
pt-10 | padding-top: 2.5rem; /*40px*/
pr-10 | padding-right: 2.5rem; /*40px*/
pb-10 | padding-bottom: 2.5rem; /*40px*/
pl-10 | padding-left: 2.5rem; /*40px*/
p-11 | padding: 2.75rem; /*44px*/
px-11 | padding-left: 2.75rem; /*44px */ <br/> padding-right: 2.75rem; /* 44px*/
py-11 | padding-top: 2.75rem; /*44px */ <br/> padding-bottom: 2.75rem; /* 44px*/
ps-11 | padding-inline-start: 2.75rem; /*44px*/
pe-11 | padding-inline-end: 2.75rem; /*44px*/
pt-11 | padding-top: 2.75rem; /*44px*/
pr-11 | padding-right: 2.75rem; /*44px*/
pb-11 | padding-bottom: 2.75rem; /*44px*/
pl-11 | padding-left: 2.75rem; /*44px*/
p-12 | padding: 3rem; /*48px*/
px-12 | padding-left: 3rem; /*48px */ <br/> padding-right: 3rem; /* 48px*/
py-12 | padding-top: 3rem; /*48px */ <br/> padding-bottom: 3rem; /* 48px*/
ps-12 | padding-inline-start: 3rem; /*48px*/
pe-12 | padding-inline-end: 3rem; /*48px*/
pt-12 | padding-top: 3rem; /*48px*/
pr-12 | padding-right: 3rem; /*48px*/
pb-12 | padding-bottom: 3rem; /*48px*/
pl-12 | padding-left: 3rem; /*48px*/
p-14 | padding: 3.5rem; /*56px*/
px-14 | padding-left: 3.5rem; /*56px */ <br/> padding-right: 3.5rem; /* 56px*/
py-14 | padding-top: 3.5rem; /*56px */ <br/> padding-bottom: 3.5rem; /* 56px*/
ps-14 | padding-inline-start: 3.5rem; /*56px*/
pe-14 | padding-inline-end: 3.5rem; /*56px*/
pt-14 | padding-top: 3.5rem; /*56px*/
pr-14 | padding-right: 3.5rem; /*56px*/
pb-14 | padding-bottom: 3.5rem; /*56px*/
pl-14 | padding-left: 3.5rem; /*56px*/
p-16 | padding: 4rem; /*64px*/
px-16 | padding-left: 4rem; /*64px */ <br/> padding-right: 4rem; /* 64px*/
py-16 | padding-top: 4rem; /*64px */ <br/> padding-bottom: 4rem; /* 64px*/
ps-16 | padding-inline-start: 4rem; /*64px*/
pe-16 | padding-inline-end: 4rem; /*64px*/
pt-16 | padding-top: 4rem; /*64px*/
pr-16 | padding-right: 4rem; /*64px*/
pb-16 | padding-bottom: 4rem; /*64px*/
pl-16 | padding-left: 4rem; /*64px*/
p-20 | padding: 5rem; /*80px*/
px-20 | padding-left: 5rem; /*80px */ <br/> padding-right: 5rem; /* 80px*/
py-20 | padding-top: 5rem; /*80px */ <br/> padding-bottom: 5rem; /* 80px*/
ps-20 | padding-inline-start: 5rem; /*80px*/
pe-20 | padding-inline-end: 5rem; /*80px*/
pt-20 | padding-top: 5rem; /*80px*/
pr-20 | padding-right: 5rem; /*80px*/
pb-20 | padding-bottom: 5rem; /*80px*/
pl-20 | padding-left: 5rem; /*80px*/
p-24 | padding: 6rem; /*96px*/
px-24 | padding-left: 6rem; /*96px */ <br/> padding-right: 6rem; /* 96px*/
py-24 | padding-top: 6rem; /*96px */ <br/> padding-bottom: 6rem; /* 96px*/
ps-24 | padding-inline-start: 6rem; /*96px*/
pe-24 | padding-inline-end: 6rem; /*96px*/
pt-24 | padding-top: 6rem; /*96px*/
pr-24 | padding-right: 6rem; /*96px*/
pb-24 | padding-bottom: 6rem; /*96px*/
pl-24 | padding-left: 6rem; /*96px*/
p-28 | padding: 7rem; /*112px*/
px-28 | padding-left: 7rem; /*112px */ <br/> padding-right: 7rem; /* 112px*/
py-28 | padding-top: 7rem; /*112px */ <br/> padding-bottom: 7rem; /* 112px*/
ps-28 | padding-inline-start: 7rem; /*112px*/
pe-28 | padding-inline-end: 7rem; /*112px*/
pt-28 | padding-top: 7rem; /*112px*/
pr-28 | padding-right: 7rem; /*112px*/
pb-28 | padding-bottom: 7rem; /*112px*/
pl-28 | padding-left: 7rem; /*112px*/
p-32 | padding: 8rem; /*128px*/
px-32 | padding-left: 8rem; /*128px */ <br/> padding-right: 8rem; /* 128px*/
py-32 | padding-top: 8rem; /*128px */ <br/> padding-bottom: 8rem; /* 128px*/
ps-32 | padding-inline-start: 8rem; /*128px*/
pe-32 | padding-inline-end: 8rem; /*128px*/
pt-32 | padding-top: 8rem; /*128px*/
pr-32 | padding-right: 8rem; /*128px*/
pb-32 | padding-bottom: 8rem; /*128px*/
pl-32 | padding-left: 8rem; /*128px*/
p-36 | padding: 9rem; /*144px*/
px-36 | padding-left: 9rem; /*144px */ <br/> padding-right: 9rem; /* 144px*/
py-36 | padding-top: 9rem; /*144px */ <br/> padding-bottom: 9rem; /* 144px*/
ps-36 | padding-inline-start: 9rem; /*144px*/
pe-36 | padding-inline-end: 9rem; /*144px*/
pt-36 | padding-top: 9rem; /*144px*/
pr-36 | padding-right: 9rem; /*144px*/
pb-36 | padding-bottom: 9rem; /*144px*/
pl-36 | padding-left: 9rem; /*144px*/
p-40 | padding: 10rem; /*160px*/
px-40 | padding-left: 10rem; /*160px */ <br/> padding-right: 10rem; /* 160px*/
py-40 | padding-top: 10rem; /*160px */ <br/> padding-bottom: 10rem; /* 160px*/
ps-40 | padding-inline-start: 10rem; /*160px*/
pe-40 | padding-inline-end: 10rem; /*160px*/
pt-40 | padding-top: 10rem; /*160px*/
pr-40 | padding-right: 10rem; /*160px*/
pb-40 | padding-bottom: 10rem; /*160px*/
pl-40 | padding-left: 10rem; /*160px*/
p-44 | padding: 11rem; /*176px*/
px-44 | padding-left: 11rem; /*176px */ <br/> padding-right: 11rem; /* 176px*/
py-44 | padding-top: 11rem; /*176px */ <br/> padding-bottom: 11rem; /* 176px*/
ps-44 | padding-inline-start: 11rem; /*176px*/
pe-44 | padding-inline-end: 11rem; /*176px*/
pt-44 | padding-top: 11rem; /*176px*/
pr-44 | padding-right: 11rem; /*176px*/
pb-44 | padding-bottom: 11rem; /*176px*/
pl-44 | padding-left: 11rem; /*176px*/
p-48 | padding: 12rem; /*192px*/
px-48 | padding-left: 12rem; /*192px */ <br/> padding-right: 12rem; /* 192px*/
py-48 | padding-top: 12rem; /*192px */ <br/> padding-bottom: 12rem; /* 192px*/
ps-48 | padding-inline-start: 12rem; /*192px*/
pe-48 | padding-inline-end: 12rem; /*192px*/
pt-48 | padding-top: 12rem; /*192px*/
pr-48 | padding-right: 12rem; /*192px*/
pb-48 | padding-bottom: 12rem; /*192px*/
pl-48 | padding-left: 12rem; /*192px*/
p-52 | padding: 13rem; /*208px*/
px-52 | padding-left: 13rem; /*208px */ <br/> padding-right: 13rem; /* 208px*/
py-52 | padding-top: 13rem; /*208px */ <br/> padding-bottom: 13rem; /* 208px*/
ps-52 | padding-inline-start: 13rem; /*208px*/
pe-52 | padding-inline-end: 13rem; /*208px*/
pt-52 | padding-top: 13rem; /*208px*/
pr-52 | padding-right: 13rem; /*208px*/
pb-52 | padding-bottom: 13rem; /*208px*/
pl-52 | padding-left: 13rem; /*208px*/
p-56 | padding: 14rem; /*224px*/
px-56 | padding-left: 14rem; /*224px */ <br/> padding-right: 14rem; /* 224px*/
py-56 | padding-top: 14rem; /*224px */ <br/> padding-bottom: 14rem; /* 224px*/
ps-56 | padding-inline-start: 14rem; /*224px*/
pe-56 | padding-inline-end: 14rem; /*224px*/
pt-56 | padding-top: 14rem; /*224px*/
pr-56 | padding-right: 14rem; /*224px*/
pb-56 | padding-bottom: 14rem; /*224px*/
pl-56 | padding-left: 14rem; /*224px*/
p-60 | padding: 15rem; /*240px*/
px-60 | padding-left: 15rem; /*240px */ <br/> padding-right: 15rem; /* 240px*/
py-60 | padding-top: 15rem; /*240px */ <br/> padding-bottom: 15rem; /* 240px*/
ps-60 | padding-inline-start: 15rem; /*240px*/
pe-60 | padding-inline-end: 15rem; /*240px*/
pt-60 | padding-top: 15rem; /*240px*/
pr-60 | padding-right: 15rem; /*240px*/
pb-60 | padding-bottom: 15rem; /*240px*/
pl-60 | padding-left: 15rem; /*240px*/
p-64 | padding: 16rem; /*256px*/
px-64 | padding-left: 16rem; /*256px */ <br/> padding-right: 16rem; /* 256px*/
py-64 | padding-top: 16rem; /*256px */ <br/> padding-bottom: 16rem; /* 256px*/
ps-64 | padding-inline-start: 16rem; /*256px*/
pe-64 | padding-inline-end: 16rem; /*256px*/
pt-64 | padding-top: 16rem; /*256px*/
pr-64 | padding-right: 16rem; /*256px*/
pb-64 | padding-bottom: 16rem; /*256px*/
pl-64 | padding-left: 16rem; /*256px*/
p-72 | padding: 18rem; /*288px*/
px-72 | padding-left: 18rem; /*288px */ <br/> padding-right: 18rem; /* 288px*/
py-72 | padding-top: 18rem; /*288px */ <br/> padding-bottom: 18rem; /* 288px*/
ps-72 | padding-inline-start: 18rem; /*288px*/
pe-72 | padding-inline-end: 18rem; /*288px*/
pt-72 | padding-top: 18rem; /*288px*/
pr-72 | padding-right: 18rem; /*288px*/
pb-72 | padding-bottom: 18rem; /*288px*/
pl-72 | padding-left: 18rem; /*288px*/
p-80 | padding: 20rem; /*320px*/
px-80 | padding-left: 20rem; /*320px */ <br/> padding-right: 20rem; /* 320px*/
py-80 | padding-top: 20rem; /*320px */ <br/> padding-bottom: 20rem; /* 320px*/
ps-80 | padding-inline-start: 20rem; /*320px*/
pe-80 | padding-inline-end: 20rem; /*320px*/
pt-80 | padding-top: 20rem; /*320px*/
pr-80 | padding-right: 20rem; /*320px*/
pb-80 | padding-bottom: 20rem; /*320px*/
pl-80 | padding-left: 20rem; /*320px*/
p-96 | padding: 24rem; /*384px*/
px-96 | padding-left: 24rem; /*384px */ <br/> padding-right: 24rem; /* 384px*/
py-96 | padding-top: 24rem; /*384px */ <br/> padding-bottom: 24rem; /* 384px*/
ps-96 | padding-inline-start: 24rem; /*384px*/
pe-96 | padding-inline-end: 24rem; /*384px*/
pt-96 | padding-top: 24rem; /*384px*/
pr-96 | padding-right: 24rem; /*384px*/
pb-96 | padding-bottom: 24rem; /*384px*/
pl-96 | padding-left: 24rem; /*384px*/
