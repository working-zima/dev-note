# Adding Custom Styles (커스텀 스타일 추가)

**설명**: Tailwind에 자신만의 커스텀 스타일을 추가하는 최선의 방법.

```jsx
import { TipGood, TipBad, TipCompat, TipInfo } from '@/components/Tip'
import { SnippetGroup } from '@/components/SnippetGroup'
```

프레임워크를 사용할 때 가장 큰 도전 과제 중 하나는, 프레임워크에서 처리하지 못하는 기능이 있을 때 무엇을 해야 할지 파악하는 것입니다.

Tailwind는 처음부터 끝까지 확장 가능하고 커스터마이즈가 가능하도록 설계되어, 당신이 무엇을 만들든 프레임워크에 싸우는 느낌을 받지 않도록 도와줍니다.

이 가이드는 디자인 토큰(custom design tokens) 커스터마이징, 필요할 때 이러한 제약에서 벗어나는 방법, 자신만의 커스텀 CSS 추가, 플러그인으로 프레임워크 확장하는 방법 등에 대해 다룹니다.

## 테마 커스터마이징

색상 팔레트, 간격(scale) 또는 타이포그래피 크기, 반응형 화면의 브레이크포인트 등을 변경하려면, `tailwind.config.js` 파일의 `theme` 섹션에 원하는 커스터마이징을 추가하세요:

```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  theme: {
    screens: {
      sm: '480px',
      md: '768px',
      lg: '976px',
      xl: '1440px',
    },
    colors: {
      'blue': '#1fb6ff',
      'pink': '#ff49db',
      'orange': '#ff7849',
      'green': '#13ce66',
      'gray-dark': '#273444',
      'gray': '#8492a6',
      'gray-light': '#d3dce6',
    },
    fontFamily: {
      sans: ['Graphik', 'sans-serif'],
      serif: ['Merriweather', 'serif'],
    },
    extend: {
      spacing: {
        '128': '32rem',
        '144': '36rem',
      },
      borderRadius: {
        '4xl': '2rem',
      }
    }
  }
}
```

테마 커스터마이징에 대한 자세한 내용은 [테마 구성](/docs/theme) 문서를 참조하세요.

## 임의 값 사용하기

일반적으로 제한된 디자인 토큰을 사용하여 대부분의 디자인을 만들 수 있지만, 때로는 완벽한 위치 조정을 위해 제약을 벗어날 필요가 있습니다.

예를 들어 배경 이미지를 특정 위치에 배치하기 위해 `top: 117px` 같은 값이 필요할 때는, Tailwind의 대괄호 표기법을 사용하여 임의의 값을 가진 클래스를 생성할 수 있습니다:

```html
<div class="top-[117px]">
  <!-- ... -->
</div>
```

이 방법은 인라인 스타일과 유사하지만, `hover`와 같은 인터랙티브 수정자나 `lg`와 같은 반응형 수정자와 결합할 수 있다는 큰 장점이 있습니다:

```html
<div class="top-[117px] lg:top-[344px]">
  <!-- ... -->
</div>
```

이 방식은 배경색, 폰트 크기, 가상 요소의 콘텐츠 등 프레임워크 내 모든 요소에 적용할 수 있습니다:

```html
<div class="bg-[#bada55] text-[22px] before:content-['Festivus']">
  <!-- ... -->
</div>
```

또한 `tailwind.config.js` 파일의 디자인 토큰을 참조하기 위해 [`theme` 함수](/docs/functions-and-directives#theme)를 사용할 수도 있습니다:

```html
<div class="grid grid-cols-[fit-content(theme(spacing.32))]">
  <!-- ... -->
</div>
```

CSS 변수를 임의 값으로 사용할 때는 `var(...)`로 변수를 감쌀 필요 없이 변수명만 제공하면 됩니다:

```html
<div class="bg-[--my-color]">
  <!-- ... -->
</div>
```
### 임의의 속성 사용하기

Tailwind에 기본으로 포함되지 않은 CSS 속성을 사용해야 할 때, 대괄호 표기법을 사용하여 완전히 임의의 CSS 속성을 작성할 수 있습니다:

```html
<div class="[mask-type:luminance]">
  <!-- ... -->
</div>
```

이 방식은 _정말로_ 인라인 스타일과 비슷하지만, 여기서도 수정자를 사용할 수 있다는 이점이 있습니다:

```html
<div class="[mask-type:luminance] hover:[mask-type:alpha]">
  <!-- ... -->
</div>
```

이 방법은 특히 CSS 변수를 사용해야 하고, 조건에 따라 변수가 변경되어야 하는 경우에 유용할 수 있습니다:

```html
<div class="[--scroll-offset:56px] lg:[--scroll-offset:44px]">
  <!-- ... -->
</div>
```

### 임의의 변형 사용하기

임의의 _변형(variants)_은 임의의 값과 유사하지만, HTML 내에서 직접 대괄호 표기법을 사용하여 선택자를 즉석에서 수정할 수 있도록 해줍니다. 이는 `hover:{utility}`나 `md:{utility}`와 같은 내장된 의사 클래스 변형이나 반응형 변형을 사용하는 것과 유사합니다.

```html
<ul role="list">
  {#each items as item}
    <li class="lg:[&:nth-child(3)]:hover:underline">{item}</li>
  {/each}
</ul>
```

```css
@media (min-width: 1024px) {
  .lg\:\[\&\:nth-child\(3\)\]\:hover\:underline:hover:nth-child(3) {
    text-decoration-line: underline;
  }
}
```

자세한 내용은 [임의의 변형](docs/hover-focus-and-other-states#using-arbitrary-variants) 문서를 참고하세요.

### 공백 처리

임의의 값에 공백이 필요한 경우 언더스코어(`_`)를 대신 사용하면 Tailwind가 빌드 시 자동으로 이를 공백으로 변환합니다:

```html
<div class="grid grid-cols-[1fr_500px_2fr]">
  <!-- ... -->
</div>
```

언더스코어가 자주 사용되지만 공백이 허용되지 않는 경우(예: URL)에는, Tailwind가 언더스코어를 공백으로 변환하지 않고 그대로 유지합니다:

```html
<div class="bg-[url('/what_a_rush.png')]">
  <!-- ... -->
</div>
```

드물게 언더스코어가 꼭 필요하고 공백 또한 허용되어 애매한 상황에서는, 언더스코어 앞에 백슬래시를 추가하여 공백으로 변환되지 않게 할 수 있습니다:

```html
<div class="before:content-['hello\_world']">
  <!-- ... -->
</div>
```

JSX와 같이 렌더링된 HTML에서 백슬래시가 제거되는 경우, [String.raw()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/raw)를 사용하여 백슬래시가 JavaScript 이스케이프 문자로 처리되지 않도록 합니다:

```jsx
<div className={String.raw`before:content-['hello\_world']`}>
  <!-- ... -->
</div>
```

### 모호성 해결하기

Tailwind의 많은 유틸리티는 공통 네임스페이스를 공유하지만 서로 다른 CSS 속성에 매핑됩니다. 예를 들어, `text-lg`와 `text-black` 모두 `text-` 네임스페이스를 공유하지만 하나는 `font-size`에, 다른 하나는 `color`에 적용됩니다.

임의의 값을 사용할 때는 일반적으로 전달한 값에 따라 Tailwind가 이 모호성을 자동으로 처리합니다:

```html
<!-- 폰트 크기 유틸리티로 생성 -->
<div class="text-[22px]">...</div>

<!-- 색상 유틸리티로 생성 -->
<div class="text-[#bada55]">...</div>
```

그러나 CSS 변수처럼 정말로 애매한 상황도 발생할 수 있습니다:

```html
<div class="text-[var(--my-var)]">...</div>
```

이 경우, [CSS 데이터 타입](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Types)을 값 앞에 추가하여 Tailwind에 기본 유형을 "힌트"할 수 있습니다:

```html
<!-- 폰트 크기 유틸리티로 생성 -->
<div class="text-[length:var(--my-var)]">...</div>

<!-- 색상 유틸리티로 생성 -->
<div class="text-[color:var(--my-var)]">...</div>
```

## CSS 및 @layer 사용하기

Tailwind 프로젝트에 완전히 사용자 지정 CSS 규칙을 추가해야 할 때 가장 쉬운 방법은 스타일시트에 직접 CSS를 추가하는 것입니다:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

.my-custom-style {
  /* ... */
}
```

더 강력한 방법으로는 `@layer` 지시어를 사용하여 Tailwind의 `base`, `components`, `utilities` 계층에 스타일을 추가할 수 있습니다:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer components {
  .my-custom-style {
    /* ... */
  }
}
```

<details class="-mt-0 mb-6 rounded-xl border px-6 py-3 prose prose-slate open:pb-5 dark:prose-dark dark:border-slate-800">
  <summary class="font-medium cursor-default select-none text-slate-900 dark:text-slate-200">Tailwind는 왜 스타일을 "레이어"로 구분하나요?</summary>

CSS에서는 동일한 특이성을 가진 두 선택자가 있을 때 스타일시트에 규칙이 나열된 순서에 따라 최종 스타일이 결정됩니다:

```css
.btn {
  background: blue;
  /* ... */
}

.bg-black {
  background: black;
}
```

여기서 `.bg-black`이 `.btn` 뒤에 있기 때문에, 버튼은 모두 검정색 배경을 가지게 됩니다:

```html
<button class="btn bg-black">...</button>
<button class="bg-black btn">...</button>
```

이를 관리하기 위해, Tailwind는 [ITCSS](https://www.xfive.co/blog/itcss-scalable-maintainable-css-architecture/#what-is-itcss)에서 영감을 받아 생성하는 스타일을 세 가지 "레이어"로 나눕니다.

- `base` 레이어는 리셋 규칙이나 기본 HTML 요소에 적용되는 기본 스타일을 포함합니다.
- `components` 레이어는 나중에 유틸리티로 덮어쓸 수 있도록 설정된 클래스 기반 스타일을 포함합니다.
- `utilities` 레이어는 항상 다른 스타일보다 우선시되는 작은 단일 목적의 클래스를 포함합니다.

이렇게 구체적으로 정의하면 스타일 간의 상호작용을 이해하기 쉽고, `@layer` 지시어를 사용하면 선언 순서를 제어하면서도 실제 코드 정리를 원하는 방식으로 유지할 수 있습니다.

</details>

`@layer` 지시어는 선언 순서를 제어하고, 관련 `@tailwind` 지시어로 스타일을 자동으로 재배치하여, [수정자](#using-modifiers-with-custom-css) 및 [사용하지 않는 사용자 지정 CSS 제거](#removing-unused-custom-css) 같은 기능을 사용자 지정 CSS에도 적용할 수 있도록 도와줍니다.

### 기본 스타일 추가하기

페이지의 기본 설정 (예: 텍스트 색상, 배경 색상, 폰트 등)을 설정하고자 한다면, `html` 또는 `body` 요소에 클래스를 추가하는 것이 가장 간단한 방법입니다:

```html
<!doctype html>
<html lang="en" class="text-gray-900 bg-gray-100 font-serif">
  <!-- ... -->
</html>
```

이 방법은 별도의 파일에 숨기지 않고, 기본 스타일 설정을 다른 스타일과 함께 마크업에 유지할 수 있습니다.

특정 HTML 요소에 기본 스타일을 추가하고 싶다면, `@layer` 지시어를 사용하여 Tailwind의 `base` 레이어에 스타일을 추가할 수 있습니다:

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
  /* ... */
}
```

기본 스타일에 사용자 지정 값을 추가할 때, [테마](/docs/theme)에 정의된 값을 참조하려면 [`theme`](/docs/functions-and-directives#theme) 함수 또는 [`@apply`](/docs/functions-and-direc

### 컴포넌트 클래스 추가하기

`components` 레이어는 프로젝트에 추가하고 싶은 복잡한 클래스를 위한 영역으로, 유틸리티 클래스로 덮어쓰기가 가능해야 할 때 사용됩니다.

일반적으로 `card`, `btn`, `badge`와 같은 클래스들이 이곳에 포함됩니다.

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer components {
  .card {
    background-color: theme('colors.white');
    border-radius: theme('borderRadius.lg');
    padding: theme('spacing.6');
    box-shadow: theme('boxShadow.xl');
  }
  /* ... */
}
```

`components` 레이어에 컴포넌트 클래스를 정의함으로써 필요할 때 유틸리티 클래스를 사용해 덮어쓸 수 있습니다:

```html
<!-- 카드 모양을 가지지만 모서리는 네모로 나타납니다 -->
<div class="card rounded-none">
  <!-- ... -->
</div>
```

Tailwind를 사용할 때 이러한 타입의 클래스가 생각만큼 자주 필요하지 않을 수 있습니다. [스타일 재사용](/docs/reusing-styles)에 관한 가이드를 참고하세요.

`components` 레이어는 사용하는 타사 컴포넌트에 커스텀 스타일을 추가하기에도 좋은 위치입니다:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer components {
  .select2-dropdown {
    @apply rounded-b-lg shadow-md;
  }
  .select2-search {
    @apply border border-gray-300 rounded;
  }
  .select2-results__group {
    @apply text-lg font-bold text-gray-900;
  }
  /* ... */
}
```

사용자 정의 컴포넌트 스타일을 추가할 때 [테마](/docs/theme)에 정의된 값을 참조하려면 [`theme`](/docs/functions-and-directives#theme) 함수 또는 [`@apply`](/docs/functions-and-directives#apply) 지시어를 사용하는 것이 좋습니다.

### 커스텀 유틸리티 추가하기

사용자 정의 유틸리티 클래스를 Tailwind의 `utilities` 레이어에 추가할 수 있습니다:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer utilities {
  .content-auto {
    content-visibility: auto;
  }
}
```

이 방법은 Tailwind에 기본적으로 제공되지 않는 CSS 기능을 프로젝트에 추가하고자 할 때 유용합니다.

### 커스텀 CSS에 수정자 사용하기

`@layer`로 Tailwind에 추가한 모든 사용자 정의 스타일은 Tailwind의 수정자 구문을 자동으로 지원하여, 호버 상태, 반응형 중단점, 다크 모드 등을 쉽게 처리할 수 있습니다.

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer utilities {
  .content-auto {
    content-visibility: auto;
  }
}
```

```html
<div class="lg:dark:content-auto">
  <!-- ... -->
</div>
```

수정자가 작동하는 방식에 대한 자세한 내용은 [호버, 포커스 및 기타 상태](/docs/hover-focus-and-other-states) 문서에서 확인하세요.

### 사용되지 않는 커스텀 CSS 제거하기

`base`, `components`, 또는 `utilities` 레이어에 추가한 커스텀 스타일은 해당 스타일이 실제로 HTML에서 사용되지 않으면 컴파일된 CSS에 포함되지 않습니다.

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer components {
  /* 실제로 사용하지 않으면 컴파일된 CSS에 포함되지 않음 */
  .card {
    /* ... */
  }
}
```

만약 항상 포함되어야 할 커스텀 CSS를 추가하고 싶다면, `@layer` 지시어 없이 스타일시트에 추가하면 됩니다:

```css
@tailwind base;
@tailwind components;

/* 이 코드는 항상 컴파일된 CSS에 포함됩니다 */
.card {
  /* ... */
}

@tailwind utilities;
```

커스텀 스타일의 우선 순위를 조정하려면 해당 스타일이 필요한 위치에 배치해야 합니다. 위 예제에서는 `.card` 클래스를 `@tailwind utilities` 앞에 추가하여 유틸리티가 이를 덮어쓸 수 있도록 했습니다.

### 여러 CSS 파일 사용하기

많은 CSS를 작성하고 여러 파일로 조직하는 경우, Tailwind로 처리하기 전에 해당 파일들이 하나의 스타일시트로 결합되어야 합니다. 그렇지 않으면 `@layer`를 사용할 때 오류가 발생할 수 있습니다.

가장 쉬운 방법은 [postcss-import](https://github.com/postcss/postcss-import) 플러그인을 사용하는 것입니다:

```diff-js
module.exports = {
  plugins: {
+   'postcss-import': {},
    tailwindcss: {},
    autoprefixer: {},
  }
}
```

자세한 내용은 [빌드 타임 임포트](/docs/using-with-preprocessors#build-time-imports) 문서에서 확인하세요.

### 레이어와 컴포넌트별 CSS

Vue나 Svelte와 같은 컴포넌트 프레임워크는 각 컴포넌트 파일 내에서 `<style>` 블록을 사용하여 컴포넌트별 스타일을 추가하는 기능을 지원합니다.

이 경우, `@apply`와 `theme`와 같은 기능을 사용할 수 있지만, `@layer` 지시어는 작동하지 않으며, `@tailwind` 지시어가 없어서 `@layer`를 사용할 수 없다는 오류가 발생합니다:

#### 컴포넌트 스타일 내에서 `@layer`를 사용하지 마세요

```html
<div>
  <slot></slot>
</div>

<style>
  /* 이 파일은 독립적으로 처리되므로 작동하지 않습니다 */
  @layer components {
    div {
      background-color: theme('colors.white');
      border-radius: theme('borderRadius.lg');
      padding: theme('spacing.6');
      box-shadow: theme('boxShadow.xl');
    }
  }
</style>
```

이유는 Vue나 Svelte와 같은 프레임워크가 각 `<style>` 블록을 독립적으로 처리하고, 각 블록에 대해 PostCSS 플러그인 체인을 별도로 실행하기 때문입니다.

즉, 10개의 컴포넌트에 각각 `<style>` 블록이 있으면 Tailwind가 10번 별도로 실행되며, 각 실행은 다른 실행에 대한 지식이 전혀 없습니다. 이로 인해 Tailwind는 `@layer`에 정의된 스타일을 해당 `@tailwind` 지시어로 옮길 수 없습니다. 왜냐하면 Tailwind는 다른 실행에 대한 `@tailwind` 지시어를 인식할 수 없기 때문입니다.

이 문제를 해결하려면 컴포넌트 스타일 내에서 `@layer`를 사용하지 않으면 됩니다:

#### `@layer` 없이 스타일을 추가하세요

```html
<div>
  <slot></slot>
</div>

<style>
  div {
    background-color: theme('colors.white');
    border-radius: theme('borderRadius.lg');
    padding: theme('spacing.6');
    box-shadow: theme('boxShadow.xl');
  }
</style>
```

이렇게 하면 스타일의 우선순위를 제어하는 기능을 잃게 되지만, 불행히도 이는 이러한 도구들이 작동하는 방식 때문에 어쩔 수 없는 부분입니다.

추천하는 방법은 컴포넌트 스타일을 사용하지 않고, Tailwind를 의도된 대로 글로벌 스타일시트로 사용하여 HTML 내에서 클래스를 직접 사용하는 것입니다:

#### 컴포넌트 스타일 대신 Tailwind 유틸리티를 사용하세요

```html
<div class="bg-white rounded-lg p-6 shadow-xl">
  <slot></slot>
</div>
```

## 플러그인 작성하기

CSS 파일을 사용하는 대신, Tailwind의 플러그인 시스템을 사용하여 프로젝트에 사용자 정의 스타일을 추가할 수도 있습니다:

```js
const plugin = require('tailwindcss/plugin')

module.exports = {
  // ...
  plugins: [
    plugin(function ({ addBase, addComponents, addUtilities, theme }) {
      addBase({
        'h1': {
          fontSize: theme('fontSize.2xl'),
        },
        'h2': {
          fontSize: theme('fontSize.xl'),
        },
      })
      addComponents({
        '.card': {
          backgroundColor: theme('colors.white'),
          borderRadius: theme('borderRadius.lg'),
          padding: theme('spacing.6'),
          boxShadow: theme('boxShadow.xl'),
        }
      })
      addUtilities({
        '.content-auto': {
          contentVisibility: 'auto',
        }
      })
    })
  ]
}
```

플러그인 작성에 대해 더 알아보려면 [플러그인 문서](/docs/plugins)를 참조하세요.
