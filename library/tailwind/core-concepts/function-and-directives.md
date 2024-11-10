# Functions & Directives (함수 및 지시어)

Tailwind에서 제공하는 사용자 정의 함수 및 지시어에 대한 참조입니다.

## 지시어

지시어는 Tailwind 프로젝트에서 특별한 기능을 제공하는 Tailwind 고유의 [at-rule](https://developer.mozilla.org/en-US/docs/Web/CSS/At-rule)입니다. 이들을 CSS에 사용하여 Tailwind의 기능을 활용할 수 있습니다.

### @tailwind

`@tailwind` 지시어를 사용하여 Tailwind의 `base`, `components`, `utilities`, `variants` 스타일을 CSS에 삽입할 수 있습니다.

```css
/**
 * Tailwind의 base 스타일과 플러그인에 의해 등록된 base 스타일을 삽입합니다.
 */
@tailwind base;

/**
 * Tailwind의 컴포넌트 클래스를 삽입하며, 플러그인에 의해 등록된 컴포넌트 클래스도 포함됩니다.
 */
@tailwind components;

/**
 * Tailwind의 유틸리티 클래스를 삽입하며, 플러그인에 의해 등록된 유틸리티 클래스도 포함됩니다.
 */
@tailwind utilities;

/**
 * 이 지시어는 Tailwind가 각 클래스의 hover, focus, responsive, dark mode 등 다양한 변형을 삽입하는 위치를 제어합니다.
 *
 * 생략하면 기본적으로 Tailwind는 이 클래스들을 스타일시트의 끝에 추가합니다.
 */
@tailwind variants;
```

### @layer

`@layer` 지시어를 사용하여 Tailwind에게 사용자 정의 스타일이 어느 "버킷"에 속하는지 알려줄 수 있습니다. 유효한 레이어는 `base`, `components`, `utilities`입니다.

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  h1 {
    @apply text-2xl;
  }
  h2 {
    @apply text-xl;
  }
}

@layer components {
  .btn-blue {
    @apply bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded;
  }
}

@layer utilities {
  .filter-none {
    filter: none;
  }
  .filter-grayscale {
    filter: grayscale(100%);
  }
}
```

Tailwind은 `@layer` 지시어 안의 CSS를 자동으로 해당 `@tailwind` 규칙과 동일한 위치로 이동시키므로, CSS를 특정 순서대로 작성할 필요 없이 명시성 문제를 피할 수 있습니다.

어떤 레이어에 추가한 사용자 정의 CSS는 해당 CSS가 HTML에서 실제로 사용될 때만 최종 빌드에 포함됩니다. 이는 기본적으로 Tailwind에서 제공하는 모든 클래스와 동일합니다.

`@layer`로 사용자 정의 CSS를 감싸면 `hover:`, `focus:`와 같은 수정자나 `md:`, `lg:`와 같은 반응형 수정자도 사용할 수 있습니다.

### @apply

`@apply`를 사용하여 기존의 유틸리티 클래스를 사용자 정의 CSS에 인라인으로 삽입할 수 있습니다.

이는 사용자 정의 CSS를 작성할 때 (예: 서드파티 라이브러리의 스타일을 오버라이드할 때) 여전히 디자인 토큰을 사용하고, HTML에서 사용하던 것과 동일한 문법을 사용하고자 할 때 유용합니다.

```css
.select2-dropdown {
  @apply rounded-b-lg shadow-md;
}
.select2-search {
  @apply border border-gray-300 rounded;
}
.select2-results__group {
  @apply text-lg font-bold text-gray-900;
}
```

`@apply`로 인라인된 규칙은 명시성 문제를 피하기 위해 기본적으로 `!important`가 **제거**됩니다:

```css
/* Input */
.foo {
  color: blue !important;
}

.bar {
  @apply foo;
}

/* Output */
.foo {
  color: blue !important;
}

.bar {
  color: blue;
}
```

기존 클래스를 `@apply`하면서 `!important`를 적용하고 싶다면, 선언 끝에 `!important`를 추가하면 됩니다:

```css
/* Input */
.btn {
  @apply font-bold py-2 px-4 rounded !important;
}

/* Output */
.btn {
  font-weight: 700 !important;
  padding-top: .5rem !important;
  padding-bottom: .5rem !important;
  padding-right: 1rem !important;
  padding-left: 1rem !important;
  border-radius: .25rem !important;
}
```

Sass/SCSS를 사용하는 경우, 이 기능을 작동시키기 위해서는 Sass의 보간 기능을 사용해야 합니다:

```css
.btn {
  @apply font-bold py-2 px-4 rounded #{!important};
}
```

#### Using @apply with per-component CSS

Vue와 Svelte와 같은 컴포넌트 프레임워크는 각 컴포넌트 파일 내에 `<style>` 블록을 추가하는 방식으로 컴포넌트별 스타일을 정의할 수 있습니다.

만약 전역 CSS에서 정의한 사용자 정의 클래스를 이러한 컴포넌트별 `<style>` 블록에서 `@apply`하려고 하면, 해당 클래스가 존재하지 않는다는 오류가 발생합니다:

```css
/* main.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer components {
  .card {
    background-color: theme(colors.white);
    border-radius: theme(borderRadius.lg);
    padding: theme(spacing.6);
    box-shadow: theme(boxShadow.xl);
  }
}
```

```html
<!-- Card.svelte -->
<div>
  <slot></slot>
</div>

<style>
  div {
    /* 이 파일과 main.css는 별도로 처리되기 때문에 작동하지 않음 */
    @apply card;
  }
</style>
```

이 문제는 Vue와 Svelte와 같은 프레임워크가 각 `<style>` 블록을 독립적으로 처리하고, 각 블록에 대해 PostCSS 플러그인 체인을 개별적으로 실행하기 때문입니다.

즉, 10개의 컴포넌트가 각각 `<style>` 블록을 가지면, Tailwind는 각 컴포넌트를 별도로 처리하며, 각 실행은 다른 실행에 대해 아무런 정보를 알지 못합니다. 그래서 `Card.svelte`에서 `@apply card`를 시도하면 실패하는데, 이는 Tailwind가 `card` 클래스가 존재하는지 알 수 없기 때문입니다. Svelte는 `Card.svelte`와 `main.css`를 서로 독립적으로 처리했기 때문입니다.

이 문제를 해결하는 방법은, 컴포넌트 내에서 `@apply`를 사용하려는 사용자 정의 스타일을 [플러그인 시스템](/docs/plugins)을 사용하여 정의하는 것입니다:

```js
/* tailwind.config.js */
const plugin = require('tailwindcss/plugin')

module.exports = {
  // ...
  plugins: [
    plugin(function ({ addComponents, theme }) {
      addComponents({
        '.card': {
          backgroundColor: theme('colors.white'),
          borderRadius: theme('borderRadius.lg'),
          padding: theme('spacing.6'),
          boxShadow: theme('boxShadow.xl'),
        }
      })
    })
  ]
}
```

이렇게 하면, 이 설정 파일을 사용하는 Tailwind가 처리하는 모든 파일에서 해당 스타일에 접근할 수 있습니다.

사실, 가장 좋은 해결책은 이런 방식으로 이상한 작업을 하지 않는 것입니다. Tailwind의 유틸리티를 의도된 대로 HTML에서 직접 사용하는 방식으로 사용하는 것이 가장 좋은 경험을 제공할 것입니다. `@apply` 기능을 남용하지 않도록 하세요.

### @config

`@config` 지시어를 사용하여 Tailwind가 해당 CSS 파일을 컴파일할 때 어떤 설정 파일을 사용할지 지정할 수 있습니다. 이는 다른 CSS 진입점에 대해 서로 다른 설정 파일을 사용해야 하는 프로젝트에 유용합니다.

<SnippetGroup>

```css
/* site.css */
@config "./tailwind.site.config.js";

@tailwind base;
@tailwind components;
@tailwind utilities;
```

```css
/* admin.css */
@config "./tailwind.admin.config.js";

@tailwind base;
@tailwind components;
@tailwind utilities;
```

</SnippetGroup>

`@config` 지시어에 제공하는 경로는 해당 CSS 파일을 기준으로 상대 경로로 지정하며, PostCSS 설정이나 Tailwind CLI에서 정의한 경로보다 우선적으로 적용됩니다.

단, `postcss-import`를 사용하는 경우, `@import` 문이 `@config` 지시어 앞에 와야 제대로 작동합니다. `postcss-import`는 CSS 명세를 엄격히 따르므로, `@import` 문이 파일 내에서 다른 규칙들보다 먼저 와야 합니다.

#### <code>@config</code>을 <code>@import</code> 문보다 앞에 두지 마세요

```css
/* admin.css */
@config "./tailwind.admin.config.js";

@import "tailwindcss/base";
@import "./custom-base.css";
@import "tailwindcss/components";
@import "./custom-components.css";
@import "tailwindcss/utilities";
```

#### <code>@import</code> 문을 <code>@config</code> 지시어 앞에 배치하세요

```css
/* admin.css */
@import "tailwindcss/base";
@import "./custom-base.css";
@import "tailwindcss/components";
@import "./custom-components.css";
@import "tailwindcss/utilities";

@config "./tailwind.admin.config.js";
```

## Functions

Tailwind은 CSS에서 Tailwind-specific 값을 접근할 수 있는 몇 가지 사용자 정의 함수들을 제공합니다. 이 함수들은 빌드 타임에 평가되며, 최종 CSS에서 정적 값으로 대체됩니다.

### theme()

`theme()` 함수는 점 표기법을 사용하여 Tailwind 설정 값에 접근할 수 있게 해줍니다.

```css
.content-area {
  height: calc(100vh - theme(spacing.12));
}
```

값에 점(`.`)이 포함된 값을 접근해야 할 경우(예: `spacing` 스케일의 `2.5` 값)는 대괄호 표기법을 사용할 수 있습니다.

```css
.content-area {
  height: calc(100vh - theme(spacing[2.5]));
}
```

Tailwind은 기본 색상 팔레트를 정의할 때 [중첩 객체 구문](/docs/customizing-colors#color-object-syntax)을 사용하므로, 중첩된 색상 값을 접근할 때는 점 표기법을 사용해야 합니다.

#### 중첩된 색상 값을 접근할 때 대시 표기법을 사용하지 마세요

```css
.btn-blue {
  background-color: theme(colors.blue-500);
}
```

#### 중첩된 색상 값을 접근할 때 점 표기법을 사용하세요

```css
.btn-blue {
  background-color: theme(colors.blue.500);
}
```

`theme`로 가져온 색상의 불투명도를 조정하려면, 색상 뒤에 슬래시(`/`)와 함께 원하는 불투명도 값을 지정할 수 있습니다.

```css
.btn-blue {
  background-color: theme(colors.blue.500 / 75%);
}
```

### screen()

`screen` 함수는 미디어 쿼리를 생성할 때, 고유의 CSS에서 값들을 중복 작성하지 않고, 이름을 통해 브레이크포인트를 참조할 수 있게 해줍니다.

```css
@media screen(sm) {
  /* ... */
}
```

이는 빌드 타임에 해당하는 화면 크기 값으로 해결되어, 지정된 브레이크포인트에 맞는 일반적인 미디어 쿼리를 생성합니다:

```css
@media (min-width: 640px) {
  /* ... */
}
```
