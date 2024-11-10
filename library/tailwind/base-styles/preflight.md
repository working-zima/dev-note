# Preflight

Preflight는 Tailwind CSS에서 기본 제공하는 스타일 초기화 또는 리셋(reset) 도구입니다.

Preflight는 Tailwind 프로젝트를 위한 기본 스타일 세트로, 현대의 normalize를 기반으로 하여 브라우저 간 불일치를 줄이고 디자인 시스템의 제약 내에서 더 쉽게 작업할 수 있도록 설계되었습니다.

Tailwind는 CSS에 `@tailwind base`를 포함하면 이러한 스타일을 자동으로 주입합니다:

```css
@tailwind base; /* 여기서 Preflight가 주입됩니다 */

@tailwind components;

@tailwind utilities;
```

## 기본 여백이 제거됩니다

Preflight는 제목, 인용구, 단락 등과 같은 요소에서 모든 기본 여백을 제거합니다.

```css
코드 복사
blockquote,
dl,
dd,
h1,
h2,
h3,
h4,
h5,
h6,
hr,
figure,
p,
pre {
  margin: 0;
}
```

이를 통해 사용자 에이전트 스타일시트가 적용한 여백 값에 의존하지 않고, 여러분의 간격 스케일에서 설정한 값을 유지할 수 있습니다.

## 제목 스타일이 제거됩니다

모든 제목 요소는 기본적으로 완전히 스타일이 제거되어 일반 텍스트와 동일한 글꼴 크기와 글꼴 두께를 갖습니다.

```css
코드 복사
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

이렇게 하는 이유는 두 가지입니다:

- 실수로 타입 스케일에서 벗어나지 않도록 도와줍니다. 기본적으로 브라우저는 Tailwind의 기본 타입 스케일에는 없는 크기를 제목에 할당하며, 여러분의 타입 스케일에도 반드시 존재하는 것은 아닙니다.

- UI 개발에서 제목은 종종 시각적으로 강조되지 않게 해야 합니다. 기본적으로 제목을 스타일링하지 않으면, 제목에 적용하는 모든 스타일이 의도적이고 신중하게 이뤄집니다.

자신의 프로젝트에 기본 제목 스타일을 추가하려면 자체 기본 스타일을 추가하면 됩니다.

페이지의 기사 스타일 부분에 적절한 기본 제목 스타일을 선택적으로 도입하고 싶다면, `@tailwindcss/typography` 플러그인을 사용하는 것이 좋습니다.

## 목록 스타일이 제거됩니다

정렬된 목록과 정렬되지 않은 목록은 기본적으로 스타일이 제거되며, 숫자나 글머리 기호가 없고 여백이나 패딩도 없습니다.

```css
코드 복사
ol,
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
```

목록을 스타일링하고 싶다면 list-style-type 및 list-style-position 유틸리티를 사용할 수 있습니다:

```html
코드 복사
<ul class="list-disc list-inside">
  <li>One</li>
  <li>Two</li>
  <li>Three</li>
</ul>
```

프로젝트에 기본 목록 스타일을 추가하려면 자체 기본 스타일을 추가하면 됩니다.

페이지의 기사 스타일 부분에 기본 목록 스타일을 선택적으로 도입하고 싶다면, `@tailwindcss/typography` 플러그인을 사용하는 것이 좋습니다.

### 접근성 고려사항

스타일이 제거된 목록은 VoiceOver에서 목록으로 인식되지 않습니다. 목록 스타일을 유지하지 않으면서 콘텐츠가 실제 목록이라면, 해당 요소에 "list" 역할을 추가하세요:

```html
코드 복사
<ul role="list">
  <li>One</li>
  <li>Two</li>
  <li>Three</li>
</ul>
```

## 이미지는 block 레벨로 설정됩니다

이미지 및 기타 대체 요소(`svg`, `video`, `canvas` 등)는 기본적으로 `display: block`으로 설정됩니다.

```css
코드 복사
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

이는 브라우저의 기본 `display: inline`을 사용할 때 발생할 수 있는 예기치 않은 정렬 문제를 방지하는 데 도움이 됩니다.

이 요소 중 하나를 `block`이 아닌 `inline`으로 설정하려면 `inline` 유틸리티를 사용하세요:

```html
<img class="inline" src="..." alt="...">
```

## 이미지는 상위 너비에 맞춰집니다

이미지와 비디오는 본래의 비율을 유지하면서 상위 너비에 맞춰집니다.

```css
img,
video {
  max-width: 100%;
  height: auto;
}
```

이는 컨테이너를 넘치는 것을 방지하고 기본적으로 반응형이 되도록 합니다. 이 동작을 무시하려면 `max-w-none` 유틸리티를 사용하세요:

```html
<img class="max-w-none" src="..." alt="...">
```

## 테두리 스타일이 전역적으로 재설정됩니다

단순히 `border` 클래스를 추가하여 테두리를 쉽게 추가할 수 있도록, Tailwind는 모든 요소의 기본 테두리 스타일을 다음 규칙으로 재설정합니다:

```css
*,
::before,
::after {
  border-width: 0;
  border-style: solid;
  border-color: theme('borderColor.DEFAULT', currentColor);
}
```

`border` 클래스는 `border-width` 속성만 설정하기 때문에, 이 재설정을 통해 해당 클래스를 추가할 때 항상 설정한 기본 테두리 색상으로 고정된 1px 테두리가 추가됩니다.

Google 지도와 같은 일부 타사 라이브러리와 통합할 때 예기치 않은 결과가 발생할 수 있습니다.

이와 같은 상황에 직면할 때는 Preflight 스타일을 사용자 정의 CSS로 덮어써 해결할 수 있습니다:

```css
.google-map * {
  border-style: none;
}
```

## Preflight 확장하기

Preflight 위에 자신만의 기본 스타일을 추가하려면, `@layer base `지시어를 사용하여 CSS에 추가하면 됩니다:

```css
@tailwind base;

@layer base {
  h1 {
    @apply text-2xl;
  }
  h2 {
    @apply text-xl;
  }
  h3 {
    @apply text-lg;
  }
  a {
    @apply text-blue-600 underline;
  }
}

@tailwind components;

@tailwind utilities;
```

기본 스타일 추가에 대한 자세한 내용은 해당 문서를 참조하세요.

## Preflight 비활성화하기

Tailwind를 기존 프로젝트에 통합하거나 자체 기본 스타일을 제공하기 위해 Preflight를 완전히 비활성화하려면, `tailwind.config.js` 파일의 `corePlugins` 섹션에서 `preflight`를 `false`로 설정하면 됩니다:

```js
tailwind.config.js
module.exports = {
  corePlugins: {
    preflight: false,
  }
}
```
