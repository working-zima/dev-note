# Preflight

## Preflight란?

Tailwind에서 기본으로 제공하는 **Opinionated한 Reset 스타일**입니다.

[modern-normalize](https://github.com/sindresorhus/modern-normalize)를 기반으로 만들어졌으며,

**브라우저 간의 스타일 차이를 없애고**, **디자인 시스템 기반 작업을 쉽게** 할 수 있도록 도와줍니다.

Tailwind v4에서는 `@import "tailwindcss";` 한 줄만 사용하면 자동으로 포함됩니다.

```css
@layer theme, base, components, utilities;

@import "tailwindcss/theme.css" layer(theme);
@import "tailwindcss/preflight.css" layer(base); /* 자동 포함 */
@import "tailwindcss/utilities.css" layer(utilities);
```

## Preflight이 수행하는 일

### 1. 기본 마진 제거

```css
*,
::after,
::before,
::backdrop,
::file-selector-button {
  margin: 0;
  padding: 0;
}
```

- 모든 요소의 기본 마진 제거 (`<h1>`, `<p>`, `<blockquote>` 등)
- 사용자 에이전트(User-Agent) 스타일에 의존하지 않고, Tailwind spacing scale을 사용하게 유도

### 2. 박스 사이징과 테두리 초기화

```css
*,
::after,
::before,
::backdrop,
::file-selector-button {
  box-sizing: border-box;
  border: 0 solid;
}
```

- `box-sizing: border-box`는 패딩과 보더를 width 계산에 포함시켜 **레이아웃 계산을 예측 가능하게** 함
- `border` 클래스 사용 시 `1px solid currentColor`가 적용되도록 사전 정리

### 3. 외부 라이브러리와의 충돌 예방

Preflight는 전역 요소에 영향을 주기 때문에, 외부 라이브러리(Google Maps 등)와 충돌할 수 있습니다.

이 경우 `@layer base`를 활용해 덮어쓰는 방식으로 문제를 해결할 수 있습니다.

```css
@layer base {
  .google-map * {
    border-style: none;
  }
}
```

## 글로벌 CSS와 Preflight 충돌 여부

- **Preflight는 초기화** 목적 (margin, padding, border 등 제거)
- **글로벌 CSS는 설정 추가** 목적 (배경색, 폰트 등 지정)
- 따라서, 역할이 다르며 충돌보다는 **우선순위가 중요**

예시:

```css
@layer base {
  body {
    background-color: var(--color-sub);
  }
}
```

Preflight 이후에 `@layer base`로 선언하면 정상 작동합니다.

## 참고 링크

- [Preflight 공식 문서](https://tailwindcss.com/docs/preflight)
- [Preflight 스타일시트 원본](https://github.com/tailwindlabs/tailwindcss/blob/main/packages/tailwindcss/preflight.css)

## 요약

| 항목               | 설명                                   |
| ------------------ | -------------------------------------- |
| 목적               | 브라우저 기본 스타일 제거, 일관성 확보 |
| 주요 기능          | margin/padding 제거, border 초기화 등  |
| 적용 방법          | `@import "tailwindcss"` 시 자동 포함   |
| 우선순위 조정 방법 | `@layer base` 사용하여 덮어쓰기        |
| 충돌 방지 방법     | 외부 라이브러리 영역은 별도로 override |

### 제목 요소는 스타일이 적용되지 않음

모든 제목 요소(`h1`부터 `h6`)는 기본적으로 스타일이 적용되지 않으며, 일반 텍스트와 동일한 글꼴 크기와 두께를 가집니다:

```css
h1,
h2,
h3,
h4,
h5,
h6 {
  font-size: inherit;
  font-weight: inherit;
}
```

이러한 처리 방식에는 두 가지 이유가 있습니다:

- **타입 스케일(type scale)에서 무심코 벗어나는 것을 방지할 수 있습니다.**
  브라우저는 기본적으로 Tailwind의 타입 스케일에 존재하지 않는 글꼴 크기를 제목 요소에 적용하므로, 자신만의 스케일을 사용할 경우 예기치 않은 스타일이 적용될 수 있습니다.
- **UI 개발에서는 제목 요소를 시각적으로 강조하지 않는 것이 일반적입니다.**
  기본적으로 제목을 스타일 없이 처리함으로써, 개발자가 의도적으로 스타일을 적용하도록 유도합니다.

기본 제목 스타일을 프로젝트에 추가하고 싶다면 [base 스타일을 추가](https://www.notion.so/docs/adding-custom-styles#adding-base-styles)하면 됩니다.

### 리스트는 스타일이 적용되지 않음

순서 있는 리스트(`ol`)와 순서 없는 리스트(`ul`)는 기본적으로 불릿이나 번호가 없는 상태로 처리됩니다:

```css
ol,
ul,
menu {
  list-style: none;
}
```

리스트에 스타일을 적용하고 싶다면, 다음과 같은 유틸리티 클래스를 사용할 수 있습니다:

```html
<ul class="list-inside list-disc">
  <li>One</li>
  <li>Two</li>
  <li>Three</li>
</ul>
```

기본 리스트 스타일을 프로젝트에 추가하고 싶다면 [base 스타일을 추가](https://www.notion.so/docs/adding-custom-styles#adding-base-styles)하면 됩니다.

### 접근성 고려 사항

스타일이 제거된 리스트는 [VoiceOver 같은 화면 낭독기에서 리스트로 인식되지 않을 수 있습니다](https://unfetteredthoughts.net/2017/09/26/voiceover-and-list-style-type-none/). 콘텐츠가 진짜 리스트라면, 스타일을 제거하더라도 아래와 같이 `role="list"`를 추가하는 것이 좋습니다:

```html
<ul role="list">
  <li>One</li>
  <li>Two</li>
  <li>Three</li>
</ul>
```

### 이미지 요소는 block-level로 처리됨

이미지 및 기타 치환 요소(`svg`, `video`, `canvas`, `audio` 등)는 기본적으로 `display: block`이 적용됩니다:

```css
img,
svg,
video,
canvas,
audio,
iframe,
embed,
object {
  display: block;
  vertical-align: middle;
}
```

이는 브라우저의 기본값인 `inline` 사용 시 발생할 수 있는 예기치 않은 정렬 문제를 방지해줍니다.

이러한 요소를 `block`이 아닌 `inline`으로 바꾸고 싶다면 `inline` 유틸리티 클래스를 사용하면 됩니다:

```html
<img class="inline" src="..." alt="..." />
```

### 이미지 크기 자동 제한

이미지와 비디오는 고유한 종횡비(aspect ratio)를 유지한 채 부모 요소의 너비에 맞춰지도록 제한됩니다:

```css
img,
video {
  max-width: 100%;
  height: auto;
}
```

이 설정은 컨테이너를 넘치지 않도록 방지하며, 기본적으로 반응형(responsive)으로 작동하게 만듭니다. 만약 이러한 동작을 해제하고 싶다면 `max-w-none` 유틸리티 클래스를 사용하면 됩니다:

```html
<img class="max-w-none" src="..." alt="..." />
```

## Preflight 확장하기

Preflight 위에 자신만의 기본 스타일을 추가하고 싶다면 `@layer base`를 사용하여 `base` 레이어에 작성하면 됩니다:

```css
@layer base {
  h1 {
    font-size: var(--text-2xl);
  }
  h2 {
    font-size: var(--text-xl);
  }
  h3 {
    font-size: var(--text-lg);
  }
  a {
    color: var(--color-blue-600);
    text-decoration-line: underline;
  }
}
```

자세한 내용은 [기본 스타일 추가하기 문서](https://www.notion.so/docs/adding-custom-styles#adding-base-styles)를 참고하세요.

## Preflight 비활성화하기

Tailwind를 기존 프로젝트에 통합하거나, 직접 작성한 기본 스타일을 사용하고 싶을 경우 **Preflight를 완전히 비활성화**할 수 있습니다.

기본적으로 `@import "tailwindcss";`는 다음과 같은 내용을 포함합니다:

```css
@layer theme, base, components, utilities;

@import "tailwindcss/theme.css" layer(theme);
@import "tailwindcss/preflight.css" layer(base);
@import "tailwindcss/utilities.css" layer(utilities);
```

Preflight를 비활성화하려면, `preflight.css`만 생략하고 나머지 항목은 그대로 유지하면 됩니다:

```css
@layer theme, base, components, utilities;

@import "tailwindcss/theme.css" layer(theme);
/* @import "tailwindcss/preflight.css" layer(base); */ /* 주석 처리 또는 제거 */
@import "tailwindcss/utilities.css" layer(utilities);
```
