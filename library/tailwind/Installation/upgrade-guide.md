# Upgrade guide

## Tailwind CSS 프로젝트를 v3에서 v4로 업그레이드하는 방법

Tailwind CSS v4.0은 프레임워크의 새로운 메이저 버전입니다. 우리는 가능한 한 하위 호환성을 유지하려 노력했지만, 일부 변경은 불가피합니다. 이 가이드는 v3에서 v4로 프로젝트를 업그레이드하는 데 필요한 모든 단계를 안내합니다.

**Tailwind CSS v4.0은 Safari 16.4+, Chrome 111+, Firefox 128+ 이상을 대상으로 설계되었습니다.**

이전 브라우저 지원이 필요하다면, v3.4 버전을 유지하는 것이 좋습니다.

## 업그레이드 도구 사용하기

v3에서 v4로 프로젝트를 업그레이드하려면, 아래의 업그레이드 도구를 사용하여 대부분의 작업을 자동화할 수 있습니다:

```bash
npx @tailwindcss/upgrade
```

대부분의 프로젝트에서 이 도구는 다음과 같은 작업을 자동으로 처리해줍니다:

- 의존성 업데이트
- 설정 파일을 CSS 기반으로 마이그레이션
- 템플릿 파일 변경 사항 반영

이 도구를 실행하려면 **Node.js 20 이상**이 필요하므로, 먼저 Node 버전을 확인하고 업데이트하세요.

**업그레이드 도구는 새로운 브랜치에서 실행할 것을 권장합니다.**

업데이트 후 변경 사항(diff)을 꼼꼼히 검토하고, 브라우저에서 프로젝트를 테스트해 보세요. 복잡한 프로젝트일 경우 수동으로 수정이 필요한 부분도 있을 수 있지만, 이 도구를 사용하면 많은 시간을 절약할 수 있습니다.

또한 [v4의 변경 사항](https://www.notion.so/Upgrade-guide-21177f07850e80c3ab68e5d5f6d01801?pvs=21) 전체를 검토하여, 도구가 처리하지 못한 부분이 있는지 확인하는 것이 좋습니다.

## 수동 업그레이드

---

### PostCSS 사용 시

v3에서는 `tailwindcss` 패키지가 PostCSS 플러그인이었지만, v4에서는 `@tailwindcss/postcss`라는 전용 패키지로 분리되었습니다.

또한 v4에서는 import 및 벤더 프리픽스(vendor prefix) 처리를 자동으로 수행하므로, `postcss-import`와 `autoprefixer`를 제거할 수 있습니다.

```jsx
// postcss.config.mjs
export default {
  plugins: {
    // 제거할 항목
    // "postcss-import": {},
    // "autoprefixer": {},

    // 변경된 설정
    "@tailwindcss/postcss": {},
  },
};
```

### Vite 사용 시

Vite를 사용하는 경우, 기존의 PostCSS 플러그인 대신 성능과 개발 경험을 개선한 **전용 Vite 플러그인**으로 전환하는 것을 권장합니다:

```tsx
// vite.config.ts
import { defineConfig } from "vite";
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
  plugins: [tailwindcss()],
});
```

### Tailwind CLI 사용하기

v4에서는 Tailwind CLI가 전용 패키지인 `@tailwindcss/cli`로 분리되었습니다. 기존의 빌드 명령어는 아래와 같이 수정해야 합니다:

```bash
# 변경 전 (v3)
npx tailwindcss -i input.css -o output.css

# 변경 후 (v4)
npx @tailwindcss/cli -i input.css -o output.css
```

## v3에서 변경된 사항들

---

Tailwind CSS v4.0의 주요 **breaking changes**(기존 코드가 동작하지 않을 수 있는 변경 사항) 목록입니다.

[업그레이드 도구](https://www.notion.so/Upgrade-guide-21177f07850e80c3ab68e5d5f6d01801?pvs=21)가 대부분 자동으로 처리해주지만, 전체 내용을 이해하고 프로젝트 전반을 검토하는 것이 중요합니다.

### 브라우저 지원 기준 변경

Tailwind CSS v4.0은 최신 브라우저를 기준으로 설계되었습니다.

다음 브라우저 버전 이상에서 정상 동작합니다:

- Safari 16.4+
- Chrome 111+
- Firefox 128+

핵심 기능 구현에 `@property`, `color-mix()` 등 **최신 CSS 기능**을 사용하기 때문에, 구형 브라우저에서는 제대로 작동하지 않습니다.

> 구형 브라우저 지원이 필요할 경우, v3.4를 유지하는 것을 권장합니다.
>
> 향후 업그레이드를 돕기 위한 호환 모드를 검토 중이며, 이에 대한 소식도 예정되어 있습니다.

### `@tailwind` 지시자 제거

v3에서는 Tailwind CSS를 아래와 같이 `@tailwind` 지시어로 불러왔습니다:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

v4에서는 일반 CSS `@import` 구문을 사용합니다:

```css
@import "tailwindcss";
```

### 제거된 Deprecated 유틸리티

v3에서 이미 사용 중단(deprecated)된 유틸리티들이 v4에서 완전히 제거되었습니다.

아래는 제거된 유틸리티들과 그 대체 방법입니다:

| 제거된 유틸리티         | 대체 방법                                  |
| ----------------------- | ------------------------------------------ |
| `bg-opacity-*`          | `bg-black/50` 과 같은 불투명도 수식어 사용 |
| `text-opacity-*`        | `text-black/50` 사용                       |
| `border-opacity-*`      | `border-black/50` 사용                     |
| `divide-opacity-*`      | `divide-black/50` 사용                     |
| `ring-opacity-*`        | `ring-black/50` 사용                       |
| `placeholder-opacity-*` | `placeholder-black/50` 사용                |
| `flex-shrink-*`         | `shrink-*` 으로 대체                       |
| `flex-grow-*`           | `grow-*` 으로 대체                         |
| `overflow-ellipsis`     | `text-ellipsis` 사용                       |
| `decoration-slice`      | `box-decoration-slice` 사용                |
| `decoration-clone`      | `box-decoration-clone` 사용                |

### 이름이 변경된 유틸리티

Tailwind CSS v4에서는 아래 유틸리티 클래스들의 이름이 변경되었습니다. 새로운 명명 방식은 크기 기준 접미사(xs, sm 등)를 보다 일관성 있게 적용합니다.

| v3                 | v4                 |
| ------------------ | ------------------ |
| `shadow-sm`        | `shadow-xs`        |
| `shadow`           | `shadow-sm`        |
| `drop-shadow-sm`   | `drop-shadow-xs`   |
| `drop-shadow`      | `drop-shadow-sm`   |
| `blur-sm`          | `blur-xs`          |
| `blur`             | `blur-sm`          |
| `backdrop-blur-sm` | `backdrop-blur-xs` |
| `backdrop-blur`    | `backdrop-blur-sm` |
| `rounded-sm`       | `rounded-xs`       |
| `rounded`          | `rounded-sm`       |
| `outline-none`     | `outline-hidden`   |
| `ring`             | `ring-3`           |

> 예를 들어, 기존의 `shadow`는 이제 `shadow-sm`으로, `shadow-sm`은 `shadow-xs`로 바뀌었습니다.
>
> 프로젝트 전반에서 사용하는 클래스를 일괄적으로 변경해주어야 합니다.

### shadow, radius, blur 스케일 이름 변경

Tailwind CSS v4에서는 `shadow`, `border-radius`, `blur` 관련 유틸리티의 크기 스케일을 더 명확하게 만들기 위해 이름이 변경되었습니다.

- 모든 유틸리티는 이제 명시적인 크기 접미사(`xs`, `sm` 등)를 갖도록 리네이밍되었습니다.
- 기존의 "bare" 버전 (`shadow`, `rounded`, `blur`)은 하위 호환성을 위해 여전히 작동하지만, 스타일이 변경되었을 수 있습니다.

**업데이트 방법:**

```html
<!-- v3 -->
<input class="shadow-sm" />
<!-- v4 -->
<input class="shadow-xs" />

<!-- v3 -->
<input class="shadow" />
<!-- v4 -->
<input class="shadow-sm" />
```

### outline 유틸리티 이름 변경

v4부터는 `outline` 유틸리티가 `outline-width: 1px`를 기본으로 설정하여 `border` 및 `ring` 유틸리티와 일관성을 맞추었습니다.

또한 `outline-<숫자>` 유틸리티는 `outline-style: solid`를 기본으로 포함하므로, 별도로 `outline` 클래스를 함께 쓰지 않아도 됩니다.

**업데이트 방법:**

```html
<!-- v3 -->
<input class="outline outline-2" />
<!-- v4 -->
<input class="outline-2" />
```

### `outline-none` → `outline-hidden`으로 변경

이전 버전의 `outline-none`은 실제로는 `outline-style: none`을 설정하지 않고, 강제 색상 모드(forced colors mode)에서 접근성을 위해 보이지 않는 외곽선을 사용했습니다.

v4에서는 이를 명확히 하기 위해 다음과 같이 변경했습니다:

- `outline-none`: 실제로 `outline-style: none`을 설정함
- `outline-hidden`: 이전 버전의 기능을 그대로 유지 (강제 색상 모드에서만 표시)

**업데이트 방법:**

```html
<!-- v3 -->
<input class="focus:outline-none" />
<!-- v4 -->
<input class="focus:outline-hidden" />
```

### 기본 ring 굵기 변경

v3에서 `ring` 유틸리티는 `3px` 너비의 ring을 추가했습니다.

v4에서는 기본 ring 너비를 `1px`로 변경하여 `border`, `outline`과 일관성을 유지합니다.

**기존과 동일한 시각 효과를 유지하고 싶다면**, `ring` → `ring-3`으로 바꿔야 합니다.

**업데이트 방법:**

```html
<!-- v3 -->
<input class="ring ring-blue-500" />
<!-- v4 -->
<input class="ring-3 ring-blue-500" />
```

### `space-between` 선택자 변경

v4에서는 `space-x-*` 및 `space-y-*` 유틸리티가 **큰 페이지에서의 성능 문제**를 해결하기 위해 내부 CSS 선택자가 변경되었습니다.

### 변경 전 (v3)

```css
.space-y-4 > :not([hidden]) ~ :not([hidden]) {
  margin-top: 1rem;
}
```

### 변경 후 (v4)

```css
.space-y-4 > :not(:last-child) {
  margin-bottom: 1rem;
}
```

### 영향

- `inline` 요소에서 의도치 않게 레이아웃이 변경될 수 있음
- 자식 요소에 `margin`을 직접 추가했다면, spacing이 달라질 수 있음

### 대안

이 변경으로 문제가 발생하면, `flex`나 `grid` 레이아웃으로 전환하고 `gap`을 사용하는 것을 권장합니다:

```html
<!-- v3 방식 -->
<div class="space-y-4 p-4">
  <label for="name">Name</label>
  <input type="text" name="name" />
</div>

<!-- 권장 v4 방식 -->
<div class="flex flex-col gap-4 p-4">
  <label for="name">Name</label>
  <input type="text" name="name" />
</div>
```

### 그라디언트에서 variant 사용 방식 개선

v3에서는 `dark:from-*`과 같은 variant가 **전체 gradient를 재설정**하여, 아래 예시처럼 `to-yellow-400`이 투명하게 되는 문제가 있었습니다:

```html
<!-- v3 -->
<div class="bg-gradient-to-r from-red-500 to-yellow-400 dark:from-blue-500">
  ...
</div>
```

v4에서는 해당 문제가 해결되어, 나머지 gradient 값은 그대로 유지됩니다.

### 참고: 특정 상태에서 gradient stop을 제거하고 싶다면 `via-none`을 명시적으로 사용하세요

```html
<!-- dark 모드에서 두 단계 gradient로 변경 -->
<div
  class="bg-linear-to-r from-red-500 via-orange-400 to-yellow-400 dark:via-none dark:from-blue-500 dark:to-teal-400"
>
  ...
</div>
```

### 컨테이너(`container`) 설정 변경

v3에서는 `container` 유틸리티에 대해 `center: true`, `padding` 등 다양한 설정이 가능했습니다.

하지만 v4에서는 해당 설정 옵션이 제거되었습니다.

### 커스터마이징 방법

v4에서는 `@utility` 지시어를 사용하여 `container` 유틸리티를 확장합니다:

```css
@utility container {
  margin-inline: auto;
  padding-inline: 2rem;
}
```

### 기본 border 색상 변경

v3에서는 `border-*`와 `divide-*` 유틸리티가 기본으로 `gray-200` 색상을 사용했습니다.

v4에서는 **브라우저 기본 스타일**과의 일관성을 위해 `currentColor`로 변경되었습니다.

### 해결 방법 1: 색상 지정 추가

```html
<div class="border border-gray-200 px-2 py-3">...</div>
```

### 해결 방법 2: 기본 스타일 덮어쓰기 (v3 스타일 유지)

```css
@layer base {
  *,
  ::after,
  ::before,
  ::backdrop,
  ::file-selector-button {
    border-color: var(--color-gray-200, currentColor);
  }
}
```

### 기본 ring 너비 및 색상 변경

v4에서는 `ring` 유틸리티의 기본 너비를 `3px` → `1px`, 기본 색상을 `blue-500` → `currentColor`로 변경했습니다.

이는 `border-*`, `divide-*`, `outline-*` 유틸리티와의 일관성을 높이기 위한 조치입니다.

### 업데이트 방법

1. 기존의 `ring` → `ring-3`으로 변경:

```html
<!-- v3 -->
<button class="focus:ring ...">...</button>

<!-- v4 -->
<button class="focus:ring-3 ...">...</button>
```

1. 색상을 명시적으로 지정:

```html
<button class="focus:ring-3 focus:ring-blue-500 ...">...</button>
```

### v3 스타일을 유지하려면

아래 CSS 변수 설정을 프로젝트에 추가하세요:

```css
@theme {
  --default-ring-width: 3px;
  --default-ring-color: var(--color-blue-500);
}
```

> 단, 이 방식은 하위 호환을 위한 지원일 뿐이며, v4의 공식 권장 방식은 아닙니다.

### Preflight 기본 스타일 변경 사항

v4에서는 Preflight(기본 리셋 스타일)에 몇 가지 변경이 있습니다.

### placeholder 색상 변경

- **v3**: 기본적으로 `gray-400`
- **v4**: `currentColor`의 50% 불투명도

> 대부분의 경우 시각적 차이가 크지 않으며, 오히려 더 나아 보일 수 있습니다.

### placeholder 색상 변경 v3 스타일 유지 방법

```css
@layer base {
  input::placeholder,
  textarea::placeholder {
    color: var(--color-gray-400);
  }
}
```

### 버튼 커서 변경

- **v3**: `cursor: pointer`
- **v4**: `cursor: default` (브라우저 기본값과 일치)

### 버튼 커서 변경 v3 스타일 유지 방법

```css
@layer base {
  button:not(:disabled),
  [role="button"]:not(:disabled) {
    cursor: pointer;
  }
}
```

### `<dialog>` 마진 제거

v4의 Preflight는 `<dialog>` 요소의 마진을 제거하여 다른 요소들과 일관성을 맞췄습니다.

### 가운데 정렬을 유지하려면

```css
@layer base {
  dialog {
    margin: auto;
  }
}
```

### 접두사(Prefix) 사용하기

v4에서는 접두사가 마치 variant처럼 작동하며, 항상 클래스 이름의 **가장 앞부분**에 위치합니다.

```html
<div class="tw:flex tw:bg-red-500 tw:hover:bg-red-600">...</div>
```

### 설정 방식

`prefix(tw)`처럼 설정하면 Tailwind가 내부적으로 모든 클래스에 `tw-` 접두사를 붙여 생성합니다:

```css
@import "tailwindcss" prefix(tw);

@theme {
  --font-display: "Satoshi", "sans-serif";
  --breakpoint-3xl: 120rem;
  --color-avocado-100: oklch(0.99 0 0);
  --color-avocado-200: oklch(0.98 0.04 113.22);
  --color-avocado-300: oklch(0.94 0.11 115.03);
}
```

> 위에서 설정한 변수는 최종적으로 아래처럼 접두사가 포함된 이름으로 컴파일됩니다:

```css
:root {
  --tw-font-display: "Satoshi", "sans-serif";
  --tw-breakpoint-3xl: 120rem;
  --tw-color-avocado-100: oklch(0.99 0 0);
  --tw-color-avocado-200: oklch(0.98 0.04 113.22);
  --tw-color-avocado-300: oklch(0.94 0.11 115.03);
}
```

### 사용자 정의 유틸리티 추가

v3에서는 `@layer utilities`나 `@layer components` 안에 커스텀 클래스를 정의할 수 있었습니다. 하지만 v4에서는 **네이티브 CSS cascade layer를 지원**하기 때문에 더 이상 `@layer` 문법을 Tailwind가 특별히 처리하지 않습니다.

> 이를 대신하여, v4에서는 새로운 @utility API를 도입했습니다.

### 예시: 탭 크기 유틸리티

```css
/* v3 */
@layer utilities {
  .tab-4 {
    tab-size: 4;
  }
}

/* v4 */
@utility tab-4 {
  tab-size: 4;
}
```

### 예시: 버튼 유틸리티

```css
/* v3 */
@layer components {
  .btn {
    border-radius: 0.5rem;
    padding: 0.5rem 1rem;
    background-color: ButtonFace;
  }
}

/* v4 */
@utility btn {
  border-radius: 0.5rem;
  padding: 0.5rem 1rem;
  background-color: ButtonFace;
}
```

> Tailwind는 속성 수가 적은 유틸리티를 우선 적용하므로, .btn 같은 커스텀 유틸리티도 Tailwind 기본 유틸리티로 덮어쓸 수 있습니다.

### Variant 적용 순서 변경

v3에서는 stacked variant(`first:*`, `hover:focus:` 등)를 **오른쪽에서 왼쪽**으로 처리했습니다.

v4에서는 보다 **CSS와 유사하게 왼쪽 → 오른쪽 순서**로 적용됩니다.

### Variant 적용 순서 변경 업데이트 방법

```html
<!-- v3 -->
<ul class="py-4 first:*:pt-0 last:*:pb-0">
  <!-- v4 -->
  <ul class="py-4 *:first:pt-0 *:last:pb-0">
    <li>One</li>
    <li>Two</li>
    <li>Three</li>
  </ul>
</ul>
```

> 해당 문법은 \* (직계 자식 선택자)나 prose-headings 같은 타이포그래피 플러그인에서 주로 사용됩니다.

### 임의 값에서 변수 문법 변경

v3에서는 다음과 같이 **`var()` 없이 변수명을 직접 대괄호로 묶어** 사용할 수 있었습니다:

```html
<!-- v3 -->
<div class="bg-[--brand-color]"></div>
```

v4에서는 **모호성을 줄이기 위해 소괄호**로 변경되었습니다:

```html
<!-- v4 -->
<div class="bg-(--brand-color)"></div>
```

> 정리하자면:
>
> - `[]` → `()`로 변경됨
> - `-`로 시작하는 CSS 변수를 사용하는 경우 이 문법을 적용해야 함

Tailwind CSS v4는 **성능 향상, 네이티브 CSS 기능 통합, 일관된 문법**을 위한 방향으로 크게 변화되었습니다.

필요하시다면 `tailwind.config.js`, 플러그인 마이그레이션 예시 등도 도와드릴 수 있습니다.

### 모바일에서의 Hover 스타일

v4에서는 `hover` variant가 **hover를 지원하는 입력 장치**에서만 적용되도록 변경되었습니다.

```css
@media (hover: hover) {
  .hover\\:underline:hover {
    text-decoration: underline;
  }
}
```

### 모바일에서의 Hover 스타일 영향

- 모바일(터치 기반)에서는 더 이상 `hover` 스타일이 적용되지 않음
- **탭 시 hover 효과를 의도한 경우** 스타일이 동작하지 않을 수 있음

### 해결 방법

기존 v3 방식으로 되돌리고 싶다면 아래처럼 커스텀 variant를 정의할 수 있습니다:

```css
@custom-variant hover (&:hover);
```

> 하지만 일반적으로는 hover 스타일을 기능 향상(enhancement) 수준으로 간주하고, 사이트 핵심 동작이 hover에 의존하지 않도록 구성하는 것을 권장합니다.

### outline-color에 transition 적용

이제 `transition`, `transition-color` 유틸리티에 `outline-color` 속성도 포함되었습니다.

### 예시

```html
<!-- 예전 방식: 포커스 시 outline-color만 바뀜 -->
<button class="transition hover:outline-2 hover:outline-cyan-500"></button>

<!-- 권장 방식: 초기에도 outline 색상을 명시 -->
<button class="outline-cyan-500 transition hover:outline-2"></button>
```

> 이렇게 하면, 포커스 시 기본 색상에서 커스텀 색상으로 부드럽게 전환됩니다.
>
> 초기 색상을 설정하지 않으면 의도치 않은 색상 변화가 발생할 수 있습니다.

### corePlugins 비활성화 중단

v3에서는 `corePlugins` 옵션을 사용해 특정 유틸리티를 완전히 비활성화할 수 있었습니다.

```js
// v3 예시
module.exports = {
  corePlugins: {
    float: false,
    clear: false,
  },
};
```

v4에서는 이 기능이 **더 이상 지원되지 않습니다.**

> 대체로 사용하지 않는 유틸리티는 purge(사용자 코드 기반 트리 셰이킹)로 제거됩니다.

### theme() 함수 사용 방식 변경

v4부터는 모든 테마 값이 **CSS 변수**로 정의되므로, 가능한 한 `theme()` 함수 대신 `var(--color-xxx)` 형식을 사용하는 것을 권장합니다.

### theme() 함수 사용 방식 변경 예시

```css
/* v3 */
.my-class {
  background-color: theme(colors.red.500);
}

/* v4 권장 방식 */
.my-class {
  background-color: var(--color-red-500);
}
```

### 미디어 쿼리에서는?

CSS 변수는 미디어 쿼리 내에서는 사용할 수 없기 때문에 여전히 `theme()` 함수가 필요합니다.

단, **dot 표기법 대신 변수 이름 형식**을 사용해야 합니다:

```css
/* v3 */
@media (width >= theme(screens.xl)) {
  ...;
}

/* v4 */
@media (width >= theme(--breakpoint-xl)) {
  ...;
}
```

### JavaScript 설정 파일 사용하기

v4에서도 JavaScript 기반의 설정 파일(`tailwind.config.js`)을 **하위 호환성 차원에서 지원**하지만, 이제는 **자동으로 감지되지 않습니다.**

### 설정 파일을 명시적으로 불러오는 방법

```css
@config "../../tailwind.config.js";
```

> 이제부터는 반드시 @config 디렉티브를 사용해 명시적으로 불러와야 합니다.

### 주의: 아래 옵션들은 더 이상 지원되지 않습니다

- `corePlugins`
- `safelist`
- `separator`

### JavaScript에서 테마 값 접근하기

v3에서는 `resolveConfig()` 함수를 통해 설정 파일을 객체로 변환해 JS 코드에서 사용했습니다.

v4에서는 이 방식을 제거하고, 대신 **CSS 변수**를 직접 사용하는 방법을 권장합니다.

### 예시: Motion 라이브러리에서 CSS 변수로 애니메이션 적용

```jsx
<motion.div animate={{ backgroundColor: "var(--color-blue-500)" }} />
```

### 예시: JS에서 테마 변수 값 읽기

```jsx
let styles = getComputedStyle(document.documentElement);
let shadow = styles.getPropertyValue("--shadow-xl");
```

> 더 간단하고 번들 사이즈도 줄일 수 있는 방식입니다.

### Vue, Svelte, CSS Modules에서 @apply 사용하기

v4에서는 **메인 CSS 파일이 아닌 곳**(예: Vue `<style>`, Svelte 스타일, CSS 모듈)에서는 다른 파일의 테마 변수, 커스텀 유틸리티, 커스텀 variant에 접근할 수 없습니다.

### 해결 방법 1: `@reference` 지시어 사용

```html
<template>
  <h1>Hello world!</h1>
</template>

<style>
  @reference "../../app.css";

  h1 {
    @apply text-2xl font-bold text-red-500;
  }
</style>
```

> 이렇게 하면 app.css에서 정의한 테마 값을 현재 스타일에서 참조만 할 수 있고, CSS가 중복되지 않습니다.

### 해결 방법 2: `@apply` 대신 CSS 변수 직접 사용

```html
<template>
  <h1>Hello world!</h1>
</template>

<style>
  h1 {
    color: var(--text-red-500);
  }
</style>
```

> 성능이 더 좋으며, Tailwind가 해당 스타일을 별도로 처리하지 않아도 됩니다.

[CSS Modules에서 Tailwind 사용하기](https://tailwindcss.com/docs/compatibility#css-modules)

### Sass, Less, Stylus와의 호환성

Tailwind CSS v4는 Sass, Less, Stylus와 같은 CSS 전처리기와 함께 사용하도록 설계되지 않았습니다.

> Tailwind 자체가 CSS 전처리기 역할을 하기 때문입니다.

### 지원 불가 항목

- `.scss`, `.sass`, `.less`, `.styl` 확장자 사용
- Vue, Svelte, Astro 등의 `<style lang="scss">` 형태

> Sass를 사용하는 것이 Stylus와 충돌하는 것처럼, Tailwind와 Sass를 함께 사용하는 것도 개념적으로 맞지 않습니다.

[자세한 호환성 문서 보기](https://tailwindcss.com/docs/compatibility#sass-less-and-stylus)
