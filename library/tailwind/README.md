---
description: 유틸리티 우선 CSS 프레임워크인 Tailwind CSS에 관한 정보입니다.
---

# Tailwind CSS

## VSC 확장 프로그램

### **Tailwind CSS IntelliSense**

Visual Studio Code 사용자에게 자동 완성, 구문 강조, 린팅과 같은 고급 기능을 제공하여 Tailwind 개발 경험을 향상시켜 줍니다.

![IntelliSense](./img/readme/inteli.png)

#### `.vscode/settings.json`

- VSCode가 `.css`, `.scss` 파일을 열었을 때 `"tailwindcss"` 언어 모드로 인식합니다.

```bash
  "files.associations": {
    "*.css": "tailwindcss",
    "*.scss": "tailwindcss"
  }
```

## **Tailwind Docs**

VSCode 내에서 Tailwind 문서 페이지에 쉽게 접근할 수 있습니다.

![Docs](./img/readme/docs.png)

## Vite 사용하여 React 프로젝트를 진행할 때

[Get started with Tailwind CSS](https://tailwindcss.com/docs/installation/using-vite)

### 1. **Tailwind CSS 설치**

`tailwindcss`와 `@tailwindcss/vite`를 npm으로 설치합니다.

```bash
npm install tailwindcss @tailwindcss/vite
```

### 2. **Vite 플러그인 구성**

`vite.config.ts` 파일에 `@tailwindcss/vite` 플러그인을 추가합니다.

#### `vite.config.ts`

```tsx
import { defineConfig } from "vite";
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
  plugins: [tailwindcss()],
});
```

### 3. **Tailwind CSS 가져오기**

Tailwind CSS를 가져오기 위해 CSS 파일에 `@import`를 추가합니다.

#### `styles/tailwind.css`

```css
@import "tailwindcss";
```

#### `main.tsx`

```tsx
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";

import "./styles/tailwind.css";
import App from "./App.tsx";

createRoot(document.getElementById("root")!).render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

### 4. `.prettierrc` 설정 (Tailwind class 정렬 자동화)

`prettier-plugin-tailwindcss`는 Tailwind 클래스들을 **논리적인 순서**로 자동 정렬해줍니다.

#### `설치 명령어`

```bash
npm install -D prettier prettier-plugin-tailwindcss
```

#### `.prettierrc`

```tsx
{
  "plugins": ["prettier-plugin-tailwindcss"]
}
```

### 5. theme 변수 및 글로벌 스타일 지정

`styles/` 디렉토리 아래에 다음과 같이 구성합니다.

```plain
styles/
    ├── tailwind.css   ← Tailwind 불러오기 (핵심)
    ├── theme.css      ← 변수 기반 테마 정의 (colors, spacing 등)
    └── globals.css    ← reset, base 스타일, custom 공통 스타일 등

```

#### `5-1. tailwind.css`

이 파일은 `main.tsx` 같은 최상단 파일에서 한 번만 import하면 됩니다.

```css
@import "tailwindcss";
@import "./theme.css";
@import "./globals.css";
```

#### `5-2. tailwind.css`

Tailwind 유틸리티에서 바로 사용 가능합니다. (예: `bg-[--color-main]`)

```css
/* 예시입니다 */

@theme {
  --color-main: #070f26;
  --color-sub: #050a18;
  --color-card-bg-light: #f5faff;
  --color-card-bg-dark: #1e2330;

  --spacing-section: 4rem;

  --font-heading: "Pretendard", sans-serif;
}
```

#### `5-3. globals.css`

`preflight`가 normalize를 담당하므로 겹치지 않게 조심해서 작성하면 됩니다.

```scss
/* 예시입니다 */
body {
  font-family: var(--font-heading);
  background-color: var(--color-sub);
  color: white;
  margin: 0;
  padding: 0;
}

::selection {
  background-color: var(--color-main);
  color: white;
}
```
