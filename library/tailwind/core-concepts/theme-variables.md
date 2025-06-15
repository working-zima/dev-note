# Theme variables

Tailwind의 유틸리티 클래스는 디자인 토큰을 위한 API처럼 동작하도록 설계되어 있습니다.

## 개요

Tailwind는 맞춤형 디자인을 만들기 위한 프레임워크이며, 프로젝트마다 타이포그래피, 색상, 그림자, 반응형 기준점 등 다양한 디자인 결정이 달라집니다.

이러한 저수준 디자인 결정 요소들을 **디자인 토큰(design tokens)**이라고 하며, Tailwind 프로젝트에서는 이 값을 **theme 변수**에 저장합니다.

### theme 변수란?

theme 변수는 `@theme` 디렉티브를 사용해 정의하는 특별한 CSS 변수입니다. 이 변수들은 프로젝트 내에서 어떤 유틸리티 클래스가 생성될지를 결정합니다.

예를 들어, `--color-mint-500`이라는 theme 변수를 정의하면 다음과 같이 사용할 수 있습니다:

```css
@import "tailwindcss";

@theme {
  --color-mint-500: oklch(0.72 0.11 178);
}
```

이제 HTML에서 아래와 같이 사용할 수 있습니다:

```html
<div class="bg-mint-500">
  <!-- ... -->
</div>
```

Tailwind는 이 theme 변수를 기반으로 일반 CSS 변수도 생성하므로, 인라인 스타일이나 임의 값에서도 재사용할 수 있습니다:

```html
<div style="background-color: var(--color-mint-500)">
  <!-- ... -->
</div>
```

theme 변수와 유틸리티 클래스의 매핑 방식은 [theme variable namespaces](https://www.notion.so/Theme-variables-21177f07850e80b5baf8db0e1fb1775f?pvs=21) 문서를 참고하세요.

### 왜 `:root` 대신 `@theme`를 사용할까?

theme 변수는 단순한 CSS 변수가 아닙니다. Tailwind에게 해당 값에 맞는 유틸리티 클래스를 **생성하라고 지시하는 역할**도 수행합니다.

이처럼 일반 CSS 변수보다 많은 기능을 하기 때문에, `@theme`라는 **특별한 문법**을 사용하여 이를 명시적으로 정의하게 되어 있습니다. 또한 theme 변수는 꼭 최상위에서만 정의되어야 하며, 선택자나 미디어 쿼리 내에서는 사용할 수 없습니다.

Tailwind 프로젝트 내에서도 단순히 변수만 정의하고 싶을 때는 여전히 `:root`를 사용할 수 있습니다.

- 유틸리티 클래스로 연결하고 싶다면 `@theme`
- 그냥 CSS 변수만 정의하고 싶다면 `:root`를 사용하세요.

### 유틸리티 클래스와의 관계

Tailwind의 유틸리티 클래스 중 `flex`, `object-cover` 같은 것은 **고정된 클래스**이며 어떤 프로젝트에서도 항상 존재합니다.

하지만 대부분의 클래스는 theme 변수에 의해 생성되며, 해당 변수가 정의되지 않으면 해당 클래스도 존재하지 않습니다.

예를 들어, `--font-*` 네임스페이스로 정의된 변수들은 프로젝트에 어떤 `font-family` 유틸리티가 존재하는지를 결정합니다:

```css
@theme {
  --font-sans: ui-sans-serif, system-ui, sans-serif, "Apple Color Emoji",
    "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji";
  --font-serif: ui-serif, Georgia, Cambria, "Times New Roman", Times, serif;
  --font-mono: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas,
    "Liberation Mono", "Courier New", monospace;
}
```

위 변수가 존재하기 때문에 Tailwind는 `font-sans`, `font-serif`, `font-mono` 클래스를 기본으로 제공합니다.

추가로 `--font-poppins` 같은 변수를 정의하면, 아래와 같이 `font-poppins` 유틸리티가 자동으로 생성됩니다:

```css
@import "tailwindcss";

@theme {
  --font-poppins: Poppins, sans-serif;
}
```

```html
<h1 class="font-poppins">This headline will use Poppins.</h1>
```

이처럼 각 네임스페이스 안에서 원하는 이름으로 theme 변수를 정의하면, **동일한 이름의 유틸리티 클래스**가 자동으로 생성됩니다.

### variants와의 관계

일부 theme 변수는 유틸리티 클래스가 아닌 **variant(변형)** 를 정의하는 데 사용됩니다.

예를 들어, `--breakpoint-*` 네임스페이스에 정의된 theme 변수는 프로젝트에서 사용할 수 있는 반응형 브레이크포인트 variant를 결정합니다:

```css
@import "tailwindcss";

@theme {
  --breakpoint-3xl: 120rem;
}
```

이제 뷰포트가 120rem 이상일 때만 작동하는 `3xl:*` variant를 사용할 수 있습니다:

```html
<div class="3xl:grid-cols-6 grid grid-cols-2 md:grid-cols-4">
  <!-- ... -->
</div>
```

theme 변수가 다양한 유틸리티 클래스나 variant로 어떻게 매핑되는지는 [theme variable namespaces](https://www.notion.so/Theme-variables-21177f07850e80b5baf8db0e1fb1775f?pvs=21) 문서를 참고하세요.

### theme 변수 네임스페이스

theme 변수는 **네임스페이스(namespace)** 단위로 정의되며, 각 네임스페이스는 하나 이상의 유틸리티 클래스 또는 variant API와 연결되어 있습니다.

아래와 같은 네임스페이스에서 theme 변수를 새로 정의하면, 이에 대응하는 유틸리티 클래스나 variant가 자동으로 프로젝트에 추가됩니다:

| 네임스페이스       | 생성되는 유틸리티 클래스 또는 variant                        |
| ------------------ | ------------------------------------------------------------ |
| `--color-*`        | `bg-red-500`, `text-sky-300` 등 색상 관련 유틸리티           |
| `--font-*`         | `font-sans` 등 글꼴 관련 유틸리티                            |
| `--text-*`         | `text-xl` 등 글자 크기 유틸리티                              |
| `--font-weight-*`  | `font-bold` 등 글꼴 굵기 유틸리티                            |
| `--tracking-*`     | `tracking-wide` 등 글자 간격 유틸리티                        |
| `--leading-*`      | `leading-tight` 등 줄 간격 유틸리티                          |
| `--breakpoint-*`   | `sm:*` 등의 반응형 브레이크포인트 variant                    |
| `--container-*`    | `@sm:*` 컨테이너 쿼리 variant, `max-w-md` 등의 크기 유틸리티 |
| `--spacing-*`      | `px-4`, `max-h-16` 등 간격/크기 유틸리티                     |
| `--radius-*`       | `rounded-sm` 등 테두리 반경 유틸리티                         |
| `--shadow-*`       | `shadow-md` 등 박스 그림자 유틸리티                          |
| `--inset-shadow-*` | `inset-shadow-xs` 등 내부 그림자 유틸리티                    |
| `--drop-shadow-*`  | `drop-shadow-md` 등 drop-shadow 필터 유틸리티                |
| `--blur-*`         | `blur-md` 등 블러 필터 유틸리티                              |
| `--perspective-*`  | `perspective-near` 등 원근감 유틸리티                        |
| `--aspect-*`       | `aspect-video` 등 비율 유틸리티                              |
| `--ease-*`         | `ease-out` 등 전환 곡선 유틸리티                             |
| `--animate-*`      | `animate-spin` 등 애니메이션 유틸리티                        |

전체 기본 theme 변수 목록은 [기본 theme 변수 참고 문서](https://www.notion.so/Theme-variables-21177f07850e80b5baf8db0e1fb1775f?pvs=21)를 확인하세요.

### 기본 theme 변수

CSS 파일 최상단에서 `tailwindcss`를 import 하면, 시작을 돕기 위한 기본 theme 변수들이 함께 포함됩니다.

다음은 `@import "tailwindcss"`가 실제로 불러오는 내용입니다:

```css
@layer theme, base, components, utilities;

@import "./theme.css" layer(theme);
@import "./preflight.css" layer(base);
@import "./utilities.css" layer(utilities);
```

여기서 `theme.css` 파일은 기본 색상 팔레트, 글꼴 크기 체계(type scale), 그림자(shadow), 글꼴(font) 등을 포함하고 있습니다:

```css
@theme {
  --font-sans: ui-sans-serif, system-ui, sans-serif, "Apple Color Emoji",
    "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji";
  --font-serif: ui-serif, Georgia, Cambria, "Times New Roman", Times, serif;
  --font-mono: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas,
    "Liberation Mono", "Courier New", monospace;

  --color-red-50: oklch(0.971 0.013 17.38);
  --color-red-100: oklch(0.936 0.032 17.717);
  --color-red-200: oklch(0.885 0.062 18.334);
  /* ... */

  --shadow-2xs: 0 1px rgb(0 0 0 / 0.05);
  --shadow-xs: 0 1px 2px 0 rgb(0 0 0 / 0.05);
  --shadow-sm: 0 1px 3px 0 rgb(0 0 0 / 0.1), 0 1px 2px -1px rgb(0 0 0 / 0.1);
  /* ... */
}
```

이러한 이유로 `bg-red-200`, `font-serif`, `shadow-sm` 같은 유틸리티 클래스가 기본으로 제공되는 것입니다. 이러한 클래스는 `flex-col`, `pointer-events-none`처럼 프레임워크에 하드코딩된 것이 아니라 theme 변수로부터 생성됩니다.

기본 theme 변수 목록 전체는 [default theme variable reference](https://www.notion.so/Theme-variables-21177f07850e80b5baf8db0e1fb1775f?pvs=21)에서 확인할 수 있습니다.

### theme 사용자 정의

기본 theme 변수는 매우 범용적이며 다양한 디자인을 만들 수 있도록 설계되어 있지만, 어디까지나 시작점일 뿐입니다. 원하는 디자인을 만들기 위해 색상 팔레트, 글꼴, 그림자 등을 커스터마이징하는 것은 매우 일반적인 일입니다.

### 기본 theme 확장

`@theme`을 사용해 새로운 theme 변수를 정의하면 기본 theme를 확장할 수 있습니다:

```css
@import "tailwindcss";

@theme {
  --font-script: Great Vibes, cursive;
}
```

이제 HTML에서 기본 `font-sans`나 `font-mono` 유틸리티처럼 `font-script` 유틸리티 클래스를 사용할 수 있습니다:

```html
<p class="font-script">This will use the Great Vibes font family.</p>
```

theme 변수가 유틸리티 클래스와 variant로 어떻게 연결되는지 더 알고 싶다면

[theme variable namespaces](https://www.notion.so/Theme-variables-21177f07850e80b5baf8db0e1fb1775f?pvs=21) 문서를 참고하세요.

### 기본 theme 덮어쓰기

기존 기본 theme 변수를 `@theme` 블록 안에서 다시 정의함으로써 값을 덮어쓸 수 있습니다:

```css
@import "tailwindcss";

@theme {
  --breakpoint-sm: 30rem;
}
```

이제 `sm:*` variant는 기본 40rem 대신 30rem에서 작동하게 됩니다:

```html
<div class="grid grid-cols-1 sm:grid-cols-3">
  <!-- ... -->
</div>
```

기본 theme의 특정 네임스페이스 전체를 완전히 제거하고 싶다면, `*` 와일드카드 문법을 사용하여 그 네임스페이스를 `initial`로 설정한 뒤 직접 값을 다시 정의할 수 있습니다:

```css
@import "tailwindcss";

@theme {
  --color-*: initial;
  --color-white: #fff;
  --color-purple: #3f3cbb;
  --color-midnight: #121063;
  --color-tahiti: #3ab7bf;
  --color-bermuda: #78dcca;
}
```

이렇게 하면 `bg-red-500`과 같은 기본 유틸리티는 제거되고, `bg-midnight` 같은 사용자 정의 유틸리티만 사용 가능하게 됩니다.

유틸리티 클래스 및 variant로의 매핑 방식은 [theme variable namespaces](https://www.notion.so/Theme-variables-21177f07850e80b5baf8db0e1fb1775f?pvs=21) 문서를 참고하세요.

### 커스텀 테마 사용하기

기본 테마를 완전히 비활성화하고 커스텀 값만 사용하려면, 전역 theme 변수 네임스페이스를 `initial`로 설정합니다:

```css
@import "tailwindcss";

@theme {
  --*: initial;

  --spacing: 4px;

  --font-body: Inter, sans-serif;

  --color-lagoon: oklch(0.72 0.11 221.19);
  --color-coral: oklch(0.74 0.17 40.24);
  --color-driftwood: oklch(0.79 0.06 74.59);
  --color-tide: oklch(0.49 0.08 205.88);
  --color-dusk: oklch(0.82 0.15 72.09);
}
```

이제 기본 theme 변수를 기반으로 만들어진 유틸리티 클래스는 더 이상 사용할 수 없으며, `font-body`나 `text-dusk`처럼 사용자가 정의한 테마 변수에 해당하는 유틸리티 클래스만 사용 가능합니다.

### 애니메이션 keyframes 정의하기

- `-animate-*` 테마 변수에 대응되는 `@keyframes` 규칙은 `@theme` 내부에 함께 정의하여 CSS에 포함시킬 수 있습니다:

```css
@import "tailwindcss";

@theme {
  --animate-fade-in-scale: fade-in-scale 0.3s ease-out;

  @keyframes fade-in-scale {
    0% {
      opacity: 0;
      transform: scale(0.95);
    }
    100% {
      opacity: 1;
      transform: scale(1);
    }
  }
}
```

만약 `--animate-*` 테마 변수 없이도 항상 해당 `@keyframes` 규칙이 포함되기를 원한다면, `@theme` 외부에 `@keyframes`를 정의하면 됩니다.

### 다른 변수를 참조하기

다른 변수 값을 참조하여 theme 변수를 정의할 경우, `inline` 옵션을 사용합니다:

```css
@import "tailwindcss";

@theme inline {
  --font-sans: var(--font-inter);
}
```

`inline` 옵션을 사용하면 유틸리티 클래스가 theme 변수 **값(value)** 을 직접 사용하게 됩니다:

```css
.font-sans {
  font-family: var(--font-inter);
}
```

`inline`을 사용하지 않으면, CSS에서 변수 해석 방식 때문에 유틸리티 클래스가 예상치 못한 값으로 해석될 수 있습니다.

예를 들어, 아래의 경우 Inter가 아닌 sans-serif로 fallback되는 현상이 발생할 수 있습니다:

```html
<div id="parent" style="--font-sans: var(--font-inter, sans-serif);">
  <div id="child" style="--font-inter: Inter; font-family: var(--font-sans);">
    This text will use the sans-serif font, not Inter.
  </div>
</div>
```

이 현상이 발생하는 이유는 `--font-sans`가 정의된 위치(여기선 `#parent`)에서 `--font-inter` 값을 찾기 때문입니다. 하지만 `--font-inter`는 DOM 구조상 아래(`#child`)에 정의되어 있으므로, 해당 시점에서 값이 없다고 판단되고 fallback 값인 sans-serif를 사용하게 됩니다.

### 모든 CSS 변수 생성하기

기본적으로 최종 CSS 출력에는 실제로 **사용된** CSS 변수만 생성됩니다. 모든 CSS 변수를 항상 생성하고 싶다면 `static` 테마 옵션을 사용할 수 있습니다:

```css
@import "tailwindcss";

@theme static {
  --color-primary: var(--color-red-500);
  --color-secondary: var(--color-blue-500);
}
```

이렇게 하면 해당 변수를 직접 사용하는 유틸리티 클래스가 없어도 CSS에 항상 포함됩니다.

### 프로젝트 간 공유하기

테마 변수는 CSS로 정의되어 있으므로, 프로젝트 간 공유도 매우 간단합니다. 공유하고자 하는 변수들을 별도의 CSS 파일에 담고, 각 프로젝트에서 불러오기만 하면 됩니다:

```css
/* ./packages/brand/theme.css */
@theme {
  --*: initial;

  --spacing: 4px;

  --font-body: Inter, sans-serif;

  --color-lagoon: oklch(0.72 0.11 221.19);
  --color-coral: oklch(0.74 0.17 40.24);
  --color-driftwood: oklch(0.79 0.06 74.59);
  --color-tide: oklch(0.49 0.08 205.88);
  --color-dusk: oklch(0.82 0.15 72.09);
}
```

그리고 다음과 같이 프로젝트 내 CSS에서 `@import`로 불러옵니다:

```css
/* ./packages/admin/app.css */
@import "tailwindcss";
@import "../brand/theme.css";
```

이 방식은 **모노레포(monorepo)** 환경에서 하나의 패키지로 테마를 공유할 때 매우 유용하며, NPM에 별도 CSS 패키지로 배포해서 제3자 CSS처럼 불러오는 것도 가능합니다.

## 테마 변수 사용하기

컴파일된 CSS에서는 여러분이 정의한 모든 테마 변수가 일반적인 CSS 변수 형태로 변환됩니다:

```css
:root {
  --font-sans: ui-sans-serif, system-ui, sans-serif, "Apple Color Emoji",
    "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji";
  --font-serif: ui-serif, Georgia, Cambria, "Times New Roman", Times, serif;
  --font-mono: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas,
    "Liberation Mono", "Courier New", monospace;

  --color-red-50: oklch(0.971 0.013 17.38);
  --color-red-100: oklch(0.936 0.032 17.717);
  --color-red-200: oklch(0.885 0.062 18.334);
  /* ... */

  --shadow-2xs: 0 1px rgb(0 0 0 / 0.05);
  --shadow-xs: 0 1px 2px 0 rgb(0 0 0 / 0.05);
  --shadow-sm: 0 1px 3px 0 rgb(0 0 0 / 0.1), 0 1px 2px -1px rgb(0 0 0 / 0.1);
  /* ... */
}
```

이렇게 변환되므로, 여러분의 **커스텀 CSS**나 **인라인 스타일**에서도 손쉽게 디자인 토큰을 참조할 수 있습니다.

### 커스텀 CSS에서 사용하기

Tailwind의 테마 변수를 사용하면 커스텀 CSS에서도 동일한 디자인 토큰을 활용할 수 있습니다:

```css
@import "tailwindcss";

@layer components {
  .typography {
    p {
      font-size: var(--text-base);
      color: var(--color-gray-700);
    }

    h1 {
      font-size: var(--text-2xl--line-height);
      font-weight: var(--font-weight-semibold);
      color: var(--color-gray-950);
    }

    h2 {
      font-size: var(--text-xl);
      font-weight: var(--font-weight-semibold);
      color: var(--color-gray-950);
    }
  }
}
```

이 방법은 특히 **데이터베이스나 API에서 가져온 Markdown 콘텐츠**처럼, 직접 제어할 수 없는 HTML을 스타일링할 때 유용합니다.

### 임의 값(arbitrary values)과 함께 사용하기

`calc()` 함수와 함께 테마 변수를 사용하는 것은 매우 유용하며, Tailwind의 임의 값 문법을 활용해 다음과 같이 사용할 수 있습니다:

```html
<div class="relative rounded-xl">
  <div class="absolute inset-px rounded-[calc(var(--radius-xl)-1px)]">
    <!-- ... -->
  </div>
</div>
```

이 예제에서는 내부 요소의 `border-radius` 값을 바깥 요소보다 1px 작게 설정함으로써 **동심 형태(concentric)**를 유지합니다.

### 자바스크립트에서 참조하기

대부분의 경우 자바스크립트에서 테마 변수를 참조할 때는 일반 CSS 변수처럼 직접 사용할 수 있습니다.

예를 들어, React에서 애니메이션을 구현하는 [Motion](https://motion.dev/docs/react-quick-start) 라이브러리에서는 다음과 같이 CSS 변수 값을 직접 사용할 수 있습니다:

```jsx
<motion.div animate={{ backgroundColor: "var(--color-blue-500)" }} />
```

만약 CSS 변수의 실제 계산된 값을 JS에서 가져와야 한다면, `getComputedStyle`을 사용하여 `:root` 요소에서 값을 조회할 수 있습니다:

```tsx
let styles = getComputedStyle(document.documentElement);
let shadow = styles.getPropertyValue("--shadow-xl");
```

이렇게 하면 테마 변수의 **실제 값**을 런타임에 사용할 수 있습니다.

## 기본 테마 변수 참조

Tailwind CSS를 프로젝트에 import 하면, 아래와 같은 기본 테마 변수들이 함께 포함됩니다. 이 변수들은 모든 유틸리티 클래스의 기반이 되는 디자인 토큰입니다.

전체 변수는 다음과 같은 카테고리로 구성되어 있습니다:

- 색상 (Color)
- 글꼴 (Font family)
- 글자 크기 (Font size)
- 자간 및 줄간격 (Letter spacing, Line height)
- 여백 및 패딩 (Spacing)
- 테두리 및 둥글기 (Border, Border radius)
- 그림자 (Box shadow)
- 너비 및 높이 (Width, Height)
- Z-Index
- 애니메이션 (Animation)
- 브레이크포인트 (Breakpoints)
- 기타 유틸리티 (기울기, 불투명도 등)

이러한 변수들은 `--color-*`, `--font-*`, `--shadow-*` 등과 같은 형식으로 CSS 변수로 정의되며, 다음과 같이 사용됩니다:

```css
font-family: var(--font-sans);
background-color: var(--color-blue-500);
box-shadow: var(--shadow-md);
```

아래는 이 변수들의 전체 목록입니다.

```css
/* [!code filename:tailwindcss/theme.css] */
@theme {
  /* prettier-ignore */
  --font-sans: ui-sans-serif, system-ui, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji";
  --font-serif: ui-serif, Georgia, Cambria, "Times New Roman", Times, serif;
  --font-mono: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas,
    "Liberation Mono", "Courier New", monospace;

  --color-red-50: oklch(0.971 0.013 17.38);
  --color-red-100: oklch(0.936 0.032 17.717);
  --color-red-200: oklch(0.885 0.062 18.334);
  --color-red-300: oklch(0.808 0.114 19.571);
  --color-red-400: oklch(0.704 0.191 22.216);
  --color-red-500: oklch(0.637 0.237 25.331);
  --color-red-600: oklch(0.577 0.245 27.325);
  --color-red-700: oklch(0.505 0.213 27.518);
  --color-red-800: oklch(0.444 0.177 26.899);
  --color-red-900: oklch(0.396 0.141 25.723);
  --color-red-950: oklch(0.258 0.092 26.042);

  --color-orange-50: oklch(0.98 0.016 73.684);
  --color-orange-100: oklch(0.954 0.038 75.164);
  --color-orange-200: oklch(0.901 0.076 70.697);
  --color-orange-300: oklch(0.837 0.128 66.29);
  --color-orange-400: oklch(0.75 0.183 55.934);
  --color-orange-500: oklch(0.705 0.213 47.604);
  --color-orange-600: oklch(0.646 0.222 41.116);
  --color-orange-700: oklch(0.553 0.195 38.402);
  --color-orange-800: oklch(0.47 0.157 37.304);
  --color-orange-900: oklch(0.408 0.123 38.172);
  --color-orange-950: oklch(0.266 0.079 36.259);

  --color-amber-50: oklch(0.987 0.022 95.277);
  --color-amber-100: oklch(0.962 0.059 95.617);
  --color-amber-200: oklch(0.924 0.12 95.746);
  --color-amber-300: oklch(0.879 0.169 91.605);
  --color-amber-400: oklch(0.828 0.189 84.429);
  --color-amber-500: oklch(0.769 0.188 70.08);
  --color-amber-600: oklch(0.666 0.179 58.318);
  --color-amber-700: oklch(0.555 0.163 48.998);
  --color-amber-800: oklch(0.473 0.137 46.201);
  --color-amber-900: oklch(0.414 0.112 45.904);
  --color-amber-950: oklch(0.279 0.077 45.635);

  --color-yellow-50: oklch(0.987 0.026 102.212);
  --color-yellow-100: oklch(0.973 0.071 103.193);
  --color-yellow-200: oklch(0.945 0.129 101.54);
  --color-yellow-300: oklch(0.905 0.182 98.111);
  --color-yellow-400: oklch(0.852 0.199 91.936);
  --color-yellow-500: oklch(0.795 0.184 86.047);
  --color-yellow-600: oklch(0.681 0.162 75.834);
  --color-yellow-700: oklch(0.554 0.135 66.442);
  --color-yellow-800: oklch(0.476 0.114 61.907);
  --color-yellow-900: oklch(0.421 0.095 57.708);
  --color-yellow-950: oklch(0.286 0.066 53.813);

  --color-lime-50: oklch(0.986 0.031 120.757);
  --color-lime-100: oklch(0.967 0.067 122.328);
  --color-lime-200: oklch(0.938 0.127 124.321);
  --color-lime-300: oklch(0.897 0.196 126.665);
  --color-lime-400: oklch(0.841 0.238 128.85);
  --color-lime-500: oklch(0.768 0.233 130.85);
  --color-lime-600: oklch(0.648 0.2 131.684);
  --color-lime-700: oklch(0.532 0.157 131.589);
  --color-lime-800: oklch(0.453 0.124 130.933);
  --color-lime-900: oklch(0.405 0.101 131.063);
  --color-lime-950: oklch(0.274 0.072 132.109);

  --color-green-50: oklch(0.982 0.018 155.826);
  --color-green-100: oklch(0.962 0.044 156.743);
  --color-green-200: oklch(0.925 0.084 155.995);
  --color-green-300: oklch(0.871 0.15 154.449);
  --color-green-400: oklch(0.792 0.209 151.711);
  --color-green-500: oklch(0.723 0.219 149.579);
  --color-green-600: oklch(0.627 0.194 149.214);
  --color-green-700: oklch(0.527 0.154 150.069);
  --color-green-800: oklch(0.448 0.119 151.328);
  --color-green-900: oklch(0.393 0.095 152.535);
  --color-green-950: oklch(0.266 0.065 152.934);

  --color-emerald-50: oklch(0.979 0.021 166.113);
  --color-emerald-100: oklch(0.95 0.052 163.051);
  --color-emerald-200: oklch(0.905 0.093 164.15);
  --color-emerald-300: oklch(0.845 0.143 164.978);
  --color-emerald-400: oklch(0.765 0.177 163.223);
  --color-emerald-500: oklch(0.696 0.17 162.48);
  --color-emerald-600: oklch(0.596 0.145 163.225);
  --color-emerald-700: oklch(0.508 0.118 165.612);
  --color-emerald-800: oklch(0.432 0.095 166.913);
  --color-emerald-900: oklch(0.378 0.077 168.94);
  --color-emerald-950: oklch(0.262 0.051 172.552);

  --color-teal-50: oklch(0.984 0.014 180.72);
  --color-teal-100: oklch(0.953 0.051 180.801);
  --color-teal-200: oklch(0.91 0.096 180.426);
  --color-teal-300: oklch(0.855 0.138 181.071);
  --color-teal-400: oklch(0.777 0.152 181.912);
  --color-teal-500: oklch(0.704 0.14 182.503);
  --color-teal-600: oklch(0.6 0.118 184.704);
  --color-teal-700: oklch(0.511 0.096 186.391);
  --color-teal-800: oklch(0.437 0.078 188.216);
  --color-teal-900: oklch(0.386 0.063 188.416);
  --color-teal-950: oklch(0.277 0.046 192.524);

  --color-cyan-50: oklch(0.984 0.019 200.873);
  --color-cyan-100: oklch(0.956 0.045 203.388);
  --color-cyan-200: oklch(0.917 0.08 205.041);
  --color-cyan-300: oklch(0.865 0.127 207.078);
  --color-cyan-400: oklch(0.789 0.154 211.53);
  --color-cyan-500: oklch(0.715 0.143 215.221);
  --color-cyan-600: oklch(0.609 0.126 221.723);
  --color-cyan-700: oklch(0.52 0.105 223.128);
  --color-cyan-800: oklch(0.45 0.085 224.283);
  --color-cyan-900: oklch(0.398 0.07 227.392);
  --color-cyan-950: oklch(0.302 0.056 229.695);

  --color-sky-50: oklch(0.977 0.013 236.62);
  --color-sky-100: oklch(0.951 0.026 236.824);
  --color-sky-200: oklch(0.901 0.058 230.902);
  --color-sky-300: oklch(0.828 0.111 230.318);
  --color-sky-400: oklch(0.746 0.16 232.661);
  --color-sky-500: oklch(0.685 0.169 237.323);
  --color-sky-600: oklch(0.588 0.158 241.966);
  --color-sky-700: oklch(0.5 0.134 242.749);
  --color-sky-800: oklch(0.443 0.11 240.79);
  --color-sky-900: oklch(0.391 0.09 240.876);
  --color-sky-950: oklch(0.293 0.066 243.157);

  --color-blue-50: oklch(0.97 0.014 254.604);
  --color-blue-100: oklch(0.932 0.032 255.585);
  --color-blue-200: oklch(0.882 0.059 254.128);
  --color-blue-300: oklch(0.809 0.105 251.813);
  --color-blue-400: oklch(0.707 0.165 254.624);
  --color-blue-500: oklch(0.623 0.214 259.815);
  --color-blue-600: oklch(0.546 0.245 262.881);
  --color-blue-700: oklch(0.488 0.243 264.376);
  --color-blue-800: oklch(0.424 0.199 265.638);
  --color-blue-900: oklch(0.379 0.146 265.522);
  --color-blue-950: oklch(0.282 0.091 267.935);

  --color-indigo-50: oklch(0.962 0.018 272.314);
  --color-indigo-100: oklch(0.93 0.034 272.788);
  --color-indigo-200: oklch(0.87 0.065 274.039);
  --color-indigo-300: oklch(0.785 0.115 274.713);
  --color-indigo-400: oklch(0.673 0.182 276.935);
  --color-indigo-500: oklch(0.585 0.233 277.117);
  --color-indigo-600: oklch(0.511 0.262 276.966);
  --color-indigo-700: oklch(0.457 0.24 277.023);
  --color-indigo-800: oklch(0.398 0.195 277.366);
  --color-indigo-900: oklch(0.359 0.144 278.697);
  --color-indigo-950: oklch(0.257 0.09 281.288);

  --color-violet-50: oklch(0.969 0.016 293.756);
  --color-violet-100: oklch(0.943 0.029 294.588);
  --color-violet-200: oklch(0.894 0.057 293.283);
  --color-violet-300: oklch(0.811 0.111 293.571);
  --color-violet-400: oklch(0.702 0.183 293.541);
  --color-violet-500: oklch(0.606 0.25 292.717);
  --color-violet-600: oklch(0.541 0.281 293.009);
  --color-violet-700: oklch(0.491 0.27 292.581);
  --color-violet-800: oklch(0.432 0.232 292.759);
  --color-violet-900: oklch(0.38 0.189 293.745);
  --color-violet-950: oklch(0.283 0.141 291.089);

  --color-purple-50: oklch(0.977 0.014 308.299);
  --color-purple-100: oklch(0.946 0.033 307.174);
  --color-purple-200: oklch(0.902 0.063 306.703);
  --color-purple-300: oklch(0.827 0.119 306.383);
  --color-purple-400: oklch(0.714 0.203 305.504);
  --color-purple-500: oklch(0.627 0.265 303.9);
  --color-purple-600: oklch(0.558 0.288 302.321);
  --color-purple-700: oklch(0.496 0.265 301.924);
  --color-purple-800: oklch(0.438 0.218 303.724);
  --color-purple-900: oklch(0.381 0.176 304.987);
  --color-purple-950: oklch(0.291 0.149 302.717);

  --color-fuchsia-50: oklch(0.977 0.017 320.058);
  --color-fuchsia-100: oklch(0.952 0.037 318.852);
  --color-fuchsia-200: oklch(0.903 0.076 319.62);
  --color-fuchsia-300: oklch(0.833 0.145 321.434);
  --color-fuchsia-400: oklch(0.74 0.238 322.16);
  --color-fuchsia-500: oklch(0.667 0.295 322.15);
  --color-fuchsia-600: oklch(0.591 0.293 322.896);
  --color-fuchsia-700: oklch(0.518 0.253 323.949);
  --color-fuchsia-800: oklch(0.452 0.211 324.591);
  --color-fuchsia-900: oklch(0.401 0.17 325.612);
  --color-fuchsia-950: oklch(0.293 0.136 325.661);

  --color-pink-50: oklch(0.971 0.014 343.198);
  --color-pink-100: oklch(0.948 0.028 342.258);
  --color-pink-200: oklch(0.899 0.061 343.231);
  --color-pink-300: oklch(0.823 0.12 346.018);
  --color-pink-400: oklch(0.718 0.202 349.761);
  --color-pink-500: oklch(0.656 0.241 354.308);
  --color-pink-600: oklch(0.592 0.249 0.584);
  --color-pink-700: oklch(0.525 0.223 3.958);
  --color-pink-800: oklch(0.459 0.187 3.815);
  --color-pink-900: oklch(0.408 0.153 2.432);
  --color-pink-950: oklch(0.284 0.109 3.907);

  --color-rose-50: oklch(0.969 0.015 12.422);
  --color-rose-100: oklch(0.941 0.03 12.58);
  --color-rose-200: oklch(0.892 0.058 10.001);
  --color-rose-300: oklch(0.81 0.117 11.638);
  --color-rose-400: oklch(0.712 0.194 13.428);
  --color-rose-500: oklch(0.645 0.246 16.439);
  --color-rose-600: oklch(0.586 0.253 17.585);
  --color-rose-700: oklch(0.514 0.222 16.935);
  --color-rose-800: oklch(0.455 0.188 13.697);
  --color-rose-900: oklch(0.41 0.159 10.272);
  --color-rose-950: oklch(0.271 0.105 12.094);

  --color-slate-50: oklch(0.984 0.003 247.858);
  --color-slate-100: oklch(0.968 0.007 247.896);
  --color-slate-200: oklch(0.929 0.013 255.508);
  --color-slate-300: oklch(0.869 0.022 252.894);
  --color-slate-400: oklch(0.704 0.04 256.788);
  --color-slate-500: oklch(0.554 0.046 257.417);
  --color-slate-600: oklch(0.446 0.043 257.281);
  --color-slate-700: oklch(0.372 0.044 257.287);
  --color-slate-800: oklch(0.279 0.041 260.031);
  --color-slate-900: oklch(0.208 0.042 265.755);
  --color-slate-950: oklch(0.129 0.042 264.695);

  --color-gray-50: oklch(0.985 0.002 247.839);
  --color-gray-100: oklch(0.967 0.003 264.542);
  --color-gray-200: oklch(0.928 0.006 264.531);
  --color-gray-300: oklch(0.872 0.01 258.338);
  --color-gray-400: oklch(0.707 0.022 261.325);
  --color-gray-500: oklch(0.551 0.027 264.364);
  --color-gray-600: oklch(0.446 0.03 256.802);
  --color-gray-700: oklch(0.373 0.034 259.733);
  --color-gray-800: oklch(0.278 0.033 256.848);
  --color-gray-900: oklch(0.21 0.034 264.665);
  --color-gray-950: oklch(0.13 0.028 261.692);

  --color-zinc-50: oklch(0.985 0 0);
  --color-zinc-100: oklch(0.967 0.001 286.375);
  --color-zinc-200: oklch(0.92 0.004 286.32);
  --color-zinc-300: oklch(0.871 0.006 286.286);
  --color-zinc-400: oklch(0.705 0.015 286.067);
  --color-zinc-500: oklch(0.552 0.016 285.938);
  --color-zinc-600: oklch(0.442 0.017 285.786);
  --color-zinc-700: oklch(0.37 0.013 285.805);
  --color-zinc-800: oklch(0.274 0.006 286.033);
  --color-zinc-900: oklch(0.21 0.006 285.885);
  --color-zinc-950: oklch(0.141 0.005 285.823);

  --color-neutral-50: oklch(0.985 0 0);
  --color-neutral-100: oklch(0.97 0 0);
  --color-neutral-200: oklch(0.922 0 0);
  --color-neutral-300: oklch(0.87 0 0);
  --color-neutral-400: oklch(0.708 0 0);
  --color-neutral-500: oklch(0.556 0 0);
  --color-neutral-600: oklch(0.439 0 0);
  --color-neutral-700: oklch(0.371 0 0);
  --color-neutral-800: oklch(0.269 0 0);
  --color-neutral-900: oklch(0.205 0 0);
  --color-neutral-950: oklch(0.145 0 0);

  --color-stone-50: oklch(0.985 0.001 106.423);
  --color-stone-100: oklch(0.97 0.001 106.424);
  --color-stone-200: oklch(0.923 0.003 48.717);
  --color-stone-300: oklch(0.869 0.005 56.366);
  --color-stone-400: oklch(0.709 0.01 56.259);
  --color-stone-500: oklch(0.553 0.013 58.071);
  --color-stone-600: oklch(0.444 0.011 73.639);
  --color-stone-700: oklch(0.374 0.01 67.558);
  --color-stone-800: oklch(0.268 0.007 34.298);
  --color-stone-900: oklch(0.216 0.006 56.043);
  --color-stone-950: oklch(0.147 0.004 49.25);

  --color-black: #000;
  --color-white: #fff;

  --spacing: 0.25rem;

  --breakpoint-sm: 40rem;
  --breakpoint-md: 48rem;
  --breakpoint-lg: 64rem;
  --breakpoint-xl: 80rem;
  --breakpoint-2xl: 96rem;

  --container-3xs: 16rem;
  --container-2xs: 18rem;
  --container-xs: 20rem;
  --container-sm: 24rem;
  --container-md: 28rem;
  --container-lg: 32rem;
  --container-xl: 36rem;
  --container-2xl: 42rem;
  --container-3xl: 48rem;
  --container-4xl: 56rem;
  --container-5xl: 64rem;
  --container-6xl: 72rem;
  --container-7xl: 80rem;

  --text-xs: 0.75rem;
  --text-xs--line-height: calc(1 / 0.75);
  --text-sm: 0.875rem;
  --text-sm--line-height: calc(1.25 / 0.875);
  --text-base: 1rem;
  --text-base--line-height: calc(1.5 / 1);
  --text-lg: 1.125rem;
  --text-lg--line-height: calc(1.75 / 1.125);
  --text-xl: 1.25rem;
  --text-xl--line-height: calc(1.75 / 1.25);
  --text-2xl: 1.5rem;
  --text-2xl--line-height: calc(2 / 1.5);
  --text-3xl: 1.875rem;
  --text-3xl--line-height: calc(2.25 / 1.875);
  --text-4xl: 2.25rem;
  --text-4xl--line-height: calc(2.5 / 2.25);
  --text-5xl: 3rem;
  --text-5xl--line-height: 1;
  --text-6xl: 3.75rem;
  --text-6xl--line-height: 1;
  --text-7xl: 4.5rem;
  --text-7xl--line-height: 1;
  --text-8xl: 6rem;
  --text-8xl--line-height: 1;
  --text-9xl: 8rem;
  --text-9xl--line-height: 1;

  --font-weight-thin: 100;
  --font-weight-extralight: 200;
  --font-weight-light: 300;
  --font-weight-normal: 400;
  --font-weight-medium: 500;
  --font-weight-semibold: 600;
  --font-weight-bold: 700;
  --font-weight-extrabold: 800;
  --font-weight-black: 900;

  --tracking-tighter: -0.05em;
  --tracking-tight: -0.025em;
  --tracking-normal: 0em;
  --tracking-wide: 0.025em;
  --tracking-wider: 0.05em;
  --tracking-widest: 0.1em;

  --leading-tight: 1.25;
  --leading-snug: 1.375;
  --leading-normal: 1.5;
  --leading-relaxed: 1.625;
  --leading-loose: 2;

  --radius-xs: 0.125rem;
  --radius-sm: 0.25rem;
  --radius-md: 0.375rem;
  --radius-lg: 0.5rem;
  --radius-xl: 0.75rem;
  --radius-2xl: 1rem;
  --radius-3xl: 1.5rem;
  --radius-4xl: 2rem;

  --shadow-2xs: 0 1px rgb(0 0 0 / 0.05);
  --shadow-xs: 0 1px 2px 0 rgb(0 0 0 / 0.05);
  --shadow-sm: 0 1px 3px 0 rgb(0 0 0 / 0.1), 0 1px 2px -1px rgb(0 0 0 / 0.1);
  --shadow-md: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1);
  --shadow-lg: 0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -4px rgb(0 0 0 / 0.1);
  --shadow-xl: 0 20px 25px -5px rgb(0 0 0 / 0.1), 0 8px 10px -6px rgb(0 0 0 /
          0.1);
  --shadow-2xl: 0 25px 50px -12px rgb(0 0 0 / 0.25);

  --inset-shadow-2xs: inset 0 1px rgb(0 0 0 / 0.05);
  --inset-shadow-xs: inset 0 1px 1px rgb(0 0 0 / 0.05);
  --inset-shadow-sm: inset 0 2px 4px rgb(0 0 0 / 0.05);

  --drop-shadow-xs: 0 1px 1px rgb(0 0 0 / 0.05);
  --drop-shadow-sm: 0 1px 2px rgb(0 0 0 / 0.15);
  --drop-shadow-md: 0 3px 3px rgb(0 0 0 / 0.12);
  --drop-shadow-lg: 0 4px 4px rgb(0 0 0 / 0.15);
  --drop-shadow-xl: 0 9px 7px rgb(0 0 0 / 0.1);
  --drop-shadow-2xl: 0 25px 25px rgb(0 0 0 / 0.15);

  --blur-xs: 4px;
  --blur-sm: 8px;
  --blur-md: 12px;
  --blur-lg: 16px;
  --blur-xl: 24px;
  --blur-2xl: 40px;
  --blur-3xl: 64px;

  --perspective-dramatic: 100px;
  --perspective-near: 300px;
  --perspective-normal: 500px;
  --perspective-midrange: 800px;
  --perspective-distant: 1200px;

  --aspect-video: 16 / 9;

  --ease-in: cubic-bezier(0.4, 0, 1, 1);
  --ease-out: cubic-bezier(0, 0, 0.2, 1);
  --ease-in-out: cubic-bezier(0.4, 0, 0.2, 1);

  --animate-spin: spin 1s linear infinite;
  --animate-ping: ping 1s cubic-bezier(0, 0, 0.2, 1) infinite;
  --animate-pulse: pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
  --animate-bounce: bounce 1s infinite;

  @keyframes spin {
    to {
      transform: rotate(360deg);
    }
  }

  @keyframes ping {
    75%,
    100% {
      transform: scale(2);
      opacity: 0;
    }
  }

  @keyframes pulse {
    50% {
      opacity: 0.5;
    }
  }

  @keyframes bounce {
    0%,
    100% {
      transform: translateY(-25%);
      animation-timing-function: cubic-bezier(0.8, 0, 1, 1);
    }

    50% {
      transform: none;
      animation-timing-function: cubic-bezier(0, 0, 0.2, 1);
    }
  }
}
```
