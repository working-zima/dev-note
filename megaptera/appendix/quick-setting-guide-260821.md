# 빠르게 시작하는 개발 환경 가이드 (2026 개정판)

> **검증일: 2026-08-21** · Windows 11 / PowerShell 7 / Node 24.19.0
>
> 이 문서의 모든 명령과 코드는 실제로 실행해 `dev` · `check` · `lint` · `format` · `test` · `build`와
> Playwright E2E까지 전부 통과하는 것을 확인한 것이다. 코드 블록은 그 동작하는 프로젝트와 바이트 단위로 일치한다.
>
> **2026-08-21 개정.** 아래 항목은 재검증 과정에서 사실이 다른 것으로 확인돼 고쳤다.
> 스캐폴딩 명령의 대화형 플래그(2-1), `index.html`의 `lang`(5장),
> 프로덕션 빌드에 남는 `mockServiceWorker.js`(6장), `strictPort`와 E2E 서버 재사용(5·11장),
> `e2e/`가 `tsc -b` 대상에서 빠지는 문제(11장), npm 11의 `allow-scripts` 동작(16장).
>
> 개정 후 이 문서를 1장부터 13장까지 그대로 따라가 빈 폴더에서 프로젝트를 다시 만들고
> `check` · `lint` · `format` · `test` · `coverage` · `build` · Playwright E2E를 전부 통과시켰다.
> `npm install` 결과도 **224개 패키지, peer 충돌 0건**으로 아래 2-3장의 서술과 일치한다.

**핵심 변화 한 줄:** 예전에는 `index.html`부터 `tsconfig`, 번들러, 린터, 테스트 러너까지 전부 손으로 조립했지만,
지금은 `npm create vite`가 그 대부분을 만들어준다. 이 가이드는 **템플릿이 안 주는 것**만 다룬다.

---

## 0. 이 가이드는 버전을 고정한다

**`@latest`를 쓰지 않는다.** 같은 명령이 시점에 따라 다른 버전을 설치하기 때문이다.
패키지마다 움직이는 속도가 달라서, "각자의 최신"을 모아놓으면 언제든 서로 어긋날 수 있다.

지금도 같은 함정이 있다. `typescript@latest`를 받으면 **7.0.2**가 깔리는데,
`typescript-eslint@8`의 peer는 `typescript >=4.8.4 <6.1.0`이라 린트 툴체인이 안 붙는다.
Vite 템플릿이 `typescript: ~6.0.2`로 묶어둔 것도 같은 이유다.

그래서 이 가이드는 **스캐폴더 버전, `.npmrc`, `package.json`을 전부 정확한 버전으로 박는다.**
단, 고정된 숫자만 있고 기준일과 올리는 방법이 없으면 함정을 미루는 것에 불과하므로
맨 위에 **검증일**을 적어두고 [15장 버전 올리기](#15-버전-올리기)를 뒀다.

> **주의: 스캐폴더만 고정해서는 부족하다.**
> `npm create vite@9.1.2`로 버전을 박아도, 생성된 `package.json`은 여전히 캐럿 범위다.
>
> ```json
> "oxlint": "^1.75.0",
> "@vitejs/plugin-react": "^6.0.4",
> "typescript": "~6.0.2"
> ```
>
> 이 상태로 `npm install`을 하면 오늘은 oxlint **1.79.0**, plugin-react **6.1.0**이 깔린다.
> 즉 같은 명령이 시점에 따라 다른 결과를 낸다. **범위를 정확한 버전으로 바꿔야 재현된다.**

---

## 1. 사전 준비 — fnm과 Node

프로젝트마다 Node 버전이 다를 수 있으므로 버전 매니저를 쓴다.

### Windows

```powershell
winget install Schniz.fnm
# 또는
scoop install fnm
```

설치만으로는 부족하다. **PowerShell 프로필에 아래를 추가해야** `fnm use`가 현재 셸에 반영된다.

```powershell
# 프로필 파일 열기 (없으면 먼저 생성)
if (-not (Test-Path $PROFILE)) { New-Item -ItemType File -Path $PROFILE -Force }
notepad $PROFILE
```

프로필에 추가:

```powershell
fnm env --use-on-cd --shell powershell | Out-String | Invoke-Expression
```

`--use-on-cd`를 넣으면 `.nvmrc`가 있는 폴더로 이동할 때 Node 버전이 자동 전환된다.

**프로필을 저장했다고 현재 셸에 바로 반영되지는 않는다.** PowerShell을 닫았다 새로 열거나,
같은 창에서 계속하려면 프로필을 다시 읽어야 한다.

```powershell
. $PROFILE
fnm --version   # 여기서 인식되면 다음 단계로
```

> `winget install` 직후라면 PATH 갱신 자체가 현재 세션에 안 붙어 `fnm`을 아예 못 찾을 수 있다.
> 그때는 `. $PROFILE`로도 해결되지 않으므로 창을 새로 여는 쪽이 확실하다.

### macOS

```bash
brew install fnm

# ~/.zshrc 에 추가
eval "$(fnm env --use-on-cd)"
```

### Node 설치

버전을 명시해서 설치한다. `--lts`는 시점에 따라 결과가 달라진다.

```powershell
fnm install 24.19.0
fnm use 24.19.0
fnm default 24.19.0

node -v   # v24.19.0
```

> **버전 메모:** Node 24 (Krypton)이 현재 LTS다. Node 26은 나와 있지만 아직 Current라 LTS가 아니다.
> Vite 8은 `^20.19.0 || >=22.12.0`, Vitest 4는 `^20 || ^22 || >=24`,
> jsdom 30은 `^22.22.2 || ^24.15.0 || >=26`을 요구한다. **Node 24가 전부 만족한다.**

---

## 2. 프로젝트 생성

### 2-1. 스캐폴딩 (버전 고정)

```powershell
npm create vite@9.1.2 my-project -- --template react-ts --no-interactive --no-eslint --no-immediate
cd my-project
```

> **뒤의 세 플래그를 빼면 안 된다.** create-vite 9는 TTY에서 대화형으로 진입한다
> (`--help`에도 "When running in TTY, the CLI will start in interactive mode"라고 적혀 있다).
> `--template`만 준 상태로 실행하면 린터 선택과 즉시 설치 여부를 되묻기 때문에
> 같은 명령이 사람마다 다른 결과를 낸다.
>
> - `--no-interactive` — 프롬프트 없이 플래그대로 진행한다.
> - `--no-eslint` — oxlint를 유지한다. ESLint를 고르면 `.oxlintrc.json`이 생기지 않아
>   [3장](#3-린트--oxlint-템플릿-기본-제공)의 전제가 통째로 깨진다.
> - `--no-immediate` — 여기서 `npm install`이 돌아버리면 캐럿 범위 그대로 설치된다.
>   아래 2-2 · 2-3의 순서가 무의미해진다.

**아직 `npm install`을 하지 않는다.** 먼저 버전을 고정한다.

### 2-2. `.npmrc` — 앞으로의 설치도 고정

```powershell
Set-Content -Path .npmrc -Value 'save-exact=true'
```

이걸 만들어두면 이후 `npm i <패키지>`가 캐럿 없이 정확한 버전으로 기록된다.

### 2-3. `package.json` 전체 교체

템플릿이 만든 캐럿 범위를 검증된 정확한 버전으로 바꾼다.
**본문(3~10장, 12~13장)에서 쓰는 패키지가 전부 들어 있다.**
선택 항목인 타입 기반 룰(3장), Playwright(11장), 상태 관리(14장)만 필요할 때 따로 설치한다.

```json
{
  "name": "my-project",
  "private": true,
  "version": "0.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc -b && vite build",
    "preview": "vite preview",
    "check": "tsc -b",
    "lint": "oxlint --fix",
    "format": "prettier --write .",
    "test": "vitest run",
    "test:watch": "vitest",
    "coverage": "vitest run --coverage"
  },
  "dependencies": {
    "axios": "1.19.0",
    "react": "19.2.8",
    "react-dom": "19.2.8",
    "react-router": "8.3.0",
    "tailwindcss": "4.3.3"
  },
  "devDependencies": {
    "@tailwindcss/vite": "4.3.3",
    "@testing-library/dom": "10.4.1",
    "@testing-library/jest-dom": "7.0.1",
    "@testing-library/react": "16.3.2",
    "@types/node": "24.13.3",
    "@types/react": "19.2.18",
    "@types/react-dom": "19.2.4",
    "@vitejs/plugin-react": "6.1.0",
    "@vitest/coverage-v8": "4.1.11",
    "jsdom": "30.0.1",
    "msw": "2.15.0",
    "oxlint": "1.79.0",
    "prettier": "3.9.6",
    "typescript": "6.0.3",
    "vite": "8.2.2",
    "vitest": "4.1.11"
  }
}
```

```powershell
npm install
```

224개 패키지가 설치되고 peer 충돌은 없다.

`package-lock.json`은 **반드시 커밋한다.** `.npmrc`와 정확한 버전이 고정하는 것은 **직접 의존성뿐**이고,
하위 의존성은 설치 시점의 레지스트리 상태로 다시 계산된다. 트리 전체를 고정하는 건 lock 파일이다.
따라서 위 패키지 수(224개)도 최초 설치 시점에 따라 조금 달라질 수 있다.
한 번 lock을 만들어 커밋한 뒤부터가 진짜 재현 가능한 상태다.

### 프로젝트 Node 버전 기록

```powershell
node -v > .nvmrc
```

### 폴더 만들기

앞으로 쓸 폴더를 미리 만들어둔다.

```powershell
New-Item -ItemType Directory -Force `
  src/components, src/hooks, src/mocks, src/pages, src/services, src/stores, src/styles, src/utils | Out-Null
```

### 템플릿이 만들어주는 것

아래는 이미 들어 있으므로 따로 만들 필요가 없다.

- `index.html`, `src/main.tsx`, `src/App.tsx`
- `tsconfig.json` + `tsconfig.app.json` + `tsconfig.node.json`
- `vite.config.ts` (+ `@vitejs/plugin-react`)
- `.oxlintrc.json` + `lint` 스크립트
- `.gitignore`
- `public/` (정적 파일 폴더 — `favicon.svg`, `icons.svg`)

템플릿은 데모용 자산도 같이 만든다: `src/assets/{react.svg, vite.svg, hero.png}`, `src/App.css`,
`src/index.css`, `README.md`. 전부 템플릿 `App.tsx`가 쓰던 것이라
[5장에서 `App.tsx`를 교체할 때](#화면에-붙이기) 같이 정리한다.

### `tsconfig.app.json`은 손댈 필요 없다

템플릿이 이미 `"jsx": "react-jsx"`, `"moduleResolution": "bundler"`, `"verbatimModuleSyntax": true`,
`"noUnusedLocals": true` 등을 켜둔 상태다.

---

## 3. 린트 — oxlint (템플릿 기본 제공)

**ESLint + Airbnb 조합은 새로 시작할 스택이 못 된다.** 이유는 두 가지다.

- ESLint v8은 2024-10에 EOL. 현재 latest는 **10.8.1**이고, `.eslintrc.js` 방식은 v10에서 완전히 제거됐다.
- `eslint-config-airbnb@19.0.4`의 peer는 `eslint ^7.32.0 || ^8.2.0`이라 **ESLint 9/10에는 설치 자체가 안 된다.**
  후속 프로젝트인 `eslint-config-airbnb-extended`도 peer가 `^9.0.0`이라 v10을 지원하지 않는다.

ESLint 8에 함께 고정하면 지금도 돌기는 한다. 다만 EOL 버전에 묶이는 선택이다.

그래서 Vite 템플릿은 ESLint를 아예 빼고 **oxlint**를 넣는다. 추가 설치 없이 바로 동작한다.

```powershell
npm run lint
```

템플릿이 만들어주는 `.oxlintrc.json`:

```json
{
  "$schema": "./node_modules/oxlint/configuration_schema.json",
  "plugins": ["react", "typescript", "oxc"],
  "rules": {
    "react/rules-of-hooks": "error",
    "react/only-export-components": ["warn", { "allowConstantExport": true }]
  }
}
```

**oxlint를 기본으로 쓰는 이유:** 플러그인 peer dependency 체인이 없다.
바로 위의 airbnb ↔ ESLint처럼 설정 패키지가 린터 버전에 묶여 통째로 못 쓰게 되는 일이 생기지 않는다.

### 접근성 룰 (jsx-a11y)

`plugins`에 `"jsx-a11y"`를 더하면 된다. 추가 설치는 없다 — oxlint에 내장돼 있다.

```json
{
  "$schema": "./node_modules/oxlint/configuration_schema.json",
  "plugins": ["react", "typescript", "oxc", "jsx-a11y"],
  "rules": {
    "react/rules-of-hooks": "error",
    "react/only-export-components": ["warn", { "allowConstantExport": true }]
  }
}
```

`<img>`에 `alt`를 빠뜨리면 `jsx-a11y(alt-text)` 경고가 뜬다.

### 타입 기반 룰이 필요하면

```powershell
npm i -D oxlint-tsgolint@7.0.2001
```

`.oxlintrc.json`에 `options`를 추가한다.

```json
{
  "$schema": "./node_modules/oxlint/configuration_schema.json",
  "plugins": ["react", "typescript", "oxc"],
  "options": {
    "typeAware": true
  },
  "rules": {
    "react/rules-of-hooks": "error",
    "react/only-export-components": ["warn", { "allowConstantExport": true }]
  }
}
```

`typeAware`를 켜면 `no-floating-promises` 같은 룰이 붙는다.
이 가이드의 예제 코드는 그 상태에서 경고 0개가 되도록 맞춰져 있다 —
[6장 `main.tsx`](#3-srcmaintsx에서-워커-켜기)의 `void enableMocking()`과
[7장 `HomePage.tsx`](#페이지--srcpageshomepagetsx)의 `.catch(...)`가 그래서 붙어 있다.
둘 중 하나라도 빼면 경고가 뜬다.

> **첫 실행이 `Error running tsgolint`로 죽으면 한 번 더 실행해 볼 것.**
> 설치 직후 첫 실행에서 `spawnSync tsgolint.exe` … `code: 'UNKNOWN'`으로 실패한 적이 있다
> (22MB 바이너리를 막 받은 직후라 Windows Defender 검사와 겹친 것으로 보인다).
> 재현되지 않았고 이후 실행은 전부 정상이었다.

### ESLint가 꼭 필요하다면

oxlint에 없는 특정 룰이 꼭 필요할 때만 ESLint 10을 병행한다. Airbnb는 포기해야 한다.

> **접근성 룰이 목적이라면 ESLint를 쓸 이유가 없다.** 아래 [jsx-a11y 절](#접근성-룰-jsx-a11y)을 볼 것.
> `eslint-plugin-jsx-a11y`는 최신 6.10.2의 peer가 `eslint ^3 … || ^9`라 **ESLint 10에는 설치조차 안 된다.**

```powershell
npm i -D eslint@10.8.1 @eslint/js@10.0.1 typescript-eslint@8.67.0 globals@17.11.0
```

`.eslintrc.js`는 이제 인식되지 않는다. `eslint.config.js`(flat config)로 작성한다.

```javascript
import js from '@eslint/js'
import globals from 'globals'
import tseslint from 'typescript-eslint'

export default tseslint.config(
  { ignores: ['dist', 'coverage', 'e2e', 'public'] },
  js.configs.recommended,
  tseslint.configs.recommended,
  {
    files: ['**/*.{ts,tsx}'],
    languageOptions: {
      ecmaVersion: 2023,
      globals: globals.browser,
    },
  },
)
```

```powershell
npx eslint .
```

> `e2e`를 `ignores`에 넣는 이유는 [8장의 `test.exclude`](#8-테스트--vitest--testing-library)와 같다.
> Playwright 스펙 실행은 Playwright가 알아서 한다
> (단 타입 검사는 별개다 — [11장](#e2e-코드는-npm-run-check가-검사하지-않는다)).
>
> `public`도 빼야 한다. 넣지 않으면 MSW가 생성한 `public/mockServiceWorker.js`까지
> 린트 대상에 들어간다. 우리가 작성한 코드가 아니고 MSW 업그레이드 때마다 다시 생성되는 파일이다.
>
> `typescript-eslint@8`은 `typescript >=4.8.4 <6.1.0`을 요구하는데,
> 이 가이드가 고정한 **6.0.3이 그 범위 안에 들어가므로 충돌 없이 설치된다.**
> TypeScript를 7로 올리면 이 조합이 깨진다 ([15장](#15-버전-올리기)).

---

## 4. 포매터 — Prettier

oxlint는 린터일 뿐 포매팅은 하지 않는다.
ESLint의 포매팅 룰(`indent`, `comma-spacing`, `object-curly-spacing` …)은 v9에서 deprecated 됐으므로
포매팅은 Prettier에 맡긴다.

`.prettierrc`:

```json
{
  "semi": false,
  "singleQuote": true
}
```

`.prettierignore` — Prettier 3은 `.gitignore`도 함께 참고하므로 `dist`는 이미 제외된다.
하지만 `coverage`(13장에서 `.gitignore`에 추가하기 전까지)와 커밋 대상인 `package-lock.json`은 걸리지 않는다.

```gitignore
dist
coverage
package-lock.json
```

---

## 5. 스타일링 — Tailwind CSS v4

**Tailwind v4는 `tailwind.config.js`도 PostCSS 설정도 필요 없다.** Vite 플러그인 하나로 끝난다.

### 플러그인 등록 — `vite.config.ts`

```typescript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [react(), tailwindcss()],
  server: {
    port: 8080,
    strictPort: true,
  },
})
```

> `strictPort: true`가 필요한 이유는 [11장](#11-e2e-테스트--playwright)에 있다.
> 기본값(`false`)이면 8080이 이미 쓰이고 있을 때 Vite가 말없이 8081로 옮겨간다.
>
> 테스트 설정(`test` 블록)은 [8장](#8-테스트--vitest--testing-library)에서 이 파일에 더한다.

### 테마 — `src/styles/theme.css`

v4에서는 **CSS의 `@theme` 블록이 곧 테마 파일**이다.

```css
@import 'tailwindcss';

@theme {
  --color-background: #ffffff;
  --color-foreground: #000000;
  --color-primary: #f00000;
  --color-secondary: #00ffff;
}

@layer base {
  @media (prefers-color-scheme: dark) {
    :root {
      --color-background: #111111;
      --color-foreground: #f5f5f5;
    }
  }

  body {
    background-color: var(--color-background);
    color: var(--color-foreground);
  }

  :lang(ko) :is(h1, h2, h3) {
    word-break: keep-all;
  }
}
```

> **`index.html`의 `lang`을 `ko`로 바꿔야 한다.** 템플릿 기본값이 `<html lang="en">`이라
> 그대로 두면 위의 `:lang(ko)` 규칙이 아무것도 매치하지 않아 `word-break: keep-all`이 적용되지 않는다.
> 화면 낭독기가 한국어 문서를 영어로 읽는 문제도 함께 생긴다.
>
> ```html
> <html lang="ko">
> ```

`@theme`에 선언한 토큰은 **대응하는 유틸리티 클래스를 쓸 수 있게 해준다.**
`--color-primary`를 선언하면 `text-primary` / `bg-primary` 같은 클래스가 유효해지고,
다크모드에서 `:root` 변수를 덮어쓰면 그 유틸리티들이 전부 따라 바뀐다.

단, **빌드 결과에는 소스에서 실제로 쓴 것만 들어간다.**
`text-primary`를 한 번이라도 쓰면 `.text-primary{color:var(--color-primary)}`가 생성되지만,
어디서도 쓰지 않은 `bg-background`는 토큰이 선언돼 있어도 CSS에 나오지 않는다.

> **루트에 `font-size: 62.5%`를 넣지 말 것.**
> rem 계산을 편하게 하려고 흔히 쓰는 기법인데 Tailwind와 충돌한다.
> Tailwind의 스케일은 전부 rem 기반이라(`--spacing: .25rem`, `.p-6 → calc(var(--spacing) * 6)`)
> 루트 폰트를 62.5%(=10px)로 낮추면 **모든 유틸리티가 37.5% 축소된다.** `p-6`이 24px가 아니라 15px가 된다.
> 루트는 기본값(16px) 그대로 둘 것.
>
> 별도 리셋 CSS도 필요 없다. Tailwind의 preflight가 리셋을 포함한다.

### 화면에 붙이기

템플릿이 만든 `src/App.tsx`를 아래로 바꾼다. 라우터는 [7장](#7-페이지와-라우팅)에서 붙인다.

```tsx
export default function App() {
  return (
    <div className="mx-auto max-w-2xl p-6">
      <h1 className="text-2xl font-bold text-primary">Hello Tailwind</h1>
    </div>
  )
}
```

`src/main.tsx`의 CSS import를 템플릿 기본값에서 방금 만든 테마로 바꾼다.

```tsx
import './styles/theme.css'
```

이제 필요 없어진 파일을 지운다.

```powershell
Remove-Item src/App.css, src/index.css -ErrorAction SilentlyContinue
Remove-Item -Recurse src/assets, public/icons.svg -ErrorAction SilentlyContinue
```

> `src/assets/{react.svg, vite.svg, hero.png}`와 `public/icons.svg`는 방금 지운 템플릿 `App.tsx`만
> 참조하던 것이라 지금 전부 고아가 된다. `public/favicon.svg`는 `index.html`이 쓰고 있으므로 남긴다.

> **여기까지 확인:** `npm run dev` → 빨간 "Hello Tailwind"가 보이면 된다.
> `npm run check`, `npm run lint`, `npm run build`도 모두 통과한다.

스타일링 방식에 대한 참고:

> **대안:** 유틸리티로 표현하기 번거로운 복잡한 컴포넌트는 CSS Modules를 섞어 쓸 수 있다.
> Vite에 내장이라 설치할 패키지가 0개다. `Foo.module.css`를 만들고 `import styles from './Foo.module.css'` 하면 된다.
> Vitest에서도 그대로 동작한다 — 테스트 안에서 클래스명이 `_badge_590927` 처럼
> 해시된 실제 값으로 들어오는 것까지 확인했다.
>
> **이 클래스명 매핑은 [8장](#8-테스트--vitest--testing-library)의 `test.css: true`와 무관하다.**
> `css: false`(Vitest 기본값)로 돌려도 해시된 이름은 똑같이 들어온다.
> `test.css: true`가 하는 일은 CSS **내용**까지 실제로 처리해 주입하는 것이라,
> 계산된 스타일(`toHaveStyle` 등)을 검사할 때 필요하다.

### VS Code 확장

Tailwind 클래스 자동완성을 쓰려면 `bradlc.vscode-tailwindcss`를 설치한다.

---

## 6. API 모킹 — MSW 2

다음 장에서 만들 페이지가 `/api/products`를 호출한다. **백엔드 없이 개발하려면 모킹을 먼저 붙여야 한다.**

> 인터넷 예제 상당수가 MSW **0.x/1.x** 문법(`rest.get`, `res(ctx.json(...))`)이다.
> v2에서 API가 전면 개편됐으므로 아래의 `http` / `HttpResponse` 형태를 써야 한다.

### `src/mocks/handlers.ts`

핸들러는 브라우저와 테스트가 함께 쓴다.

```typescript
import { http, HttpResponse } from 'msw'

const handlers = [
  http.get('/api/products', () =>
    HttpResponse.json({
      products: [
        { id: 'p1', name: '맥주' },
        { id: 'p2', name: '치킨' },
      ],
    }),
  ),
]

export default handlers
```

### 브라우저용 — dev 서버에서 쓴다

모킹을 안 붙이면 dev 서버의 SPA fallback이 `/api/products` 요청에 **`index.html`을 200으로** 돌려준다.
그러면 `response.data.products`가 `undefined`가 되어 `products.map()`에서 화면 전체가 크래시한다.

#### 1) 워커 파일 복사

워커 파일은 처음 한 번 직접 만들어야 한다. (postinstall 허용 여부와 무관하게,
워커를 놓을 위치를 아직 모르는 상태이므로 최초 1회는 수동 실행이 필요하다.)

```powershell
npx msw init public/ --save
```

`--save`는 `public/mockServiceWorker.js`를 만들고 `package.json`에 아래를 추가한다.
MSW가 버전 업그레이드 시 이 워커 파일을 갱신해야 하는지 판단하는 데 쓴다.

```json
"msw": {
  "workerDirectory": ["public"]
}
```

#### 2) `src/mocks/browser.ts`

파일만 복사해서는 아무 일도 일어나지 않는다. `setupWorker`로 핸들러를 등록해야 한다.

```typescript
import { setupWorker } from 'msw/browser'

import handlers from './handlers'

const worker = setupWorker(...handlers)

export default worker
```

#### 3) `src/main.tsx`에서 워커 켜기

`import.meta.env.DEV` 가드를 두면 **메인 JS 번들에서** MSW 런타임이 빠진다
(동적 import가 통째로 tree-shaking된다 — 빌드 결과에서 확인됨).

> **워커 파일은 그래도 남는다.** Vite는 `public/`의 파일을 손대지 않고 `dist/`로 복사하므로
> `dist/mockServiceWorker.js`가 프로덕션에 그대로 배포된다.
> 가드가 없애는 건 번들에 들어가는 MSW 코드일 뿐이다.
> 워커 파일까지 빼려면 배포 단계에서 지우거나, `public/` 밖에 두고 dev에서만 서빙해야 한다.

```tsx
import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'

import App from './App'

import './styles/theme.css'

async function enableMocking() {
  if (!import.meta.env.DEV) {
    return
  }

  const { default: worker } = await import('./mocks/browser')
  await worker.start({ onUnhandledRequest: 'bypass' })
}

void enableMocking().then(() => {
  createRoot(document.getElementById('root')!).render(
    <StrictMode>
      <App />
    </StrictMode>,
  )
})
```

> `onUnhandledRequest: 'bypass'`는 핸들러가 없는 요청(정적 파일 등)을 그냥 통과시킨다.
> 테스트 쪽에서 `'error'`를 쓰는 것과 반대인데, 브라우저에서는 모킹 대상이 아닌 요청이 훨씬 많기 때문이다.

### 테스트용 — `src/mocks/server.ts`

[8장](#8-테스트--vitest--testing-library)에서 쓴다. 지금 함께 만들어두면 된다.

```typescript
import { setupServer } from 'msw/node'

import handlers from './handlers'

const server = setupServer(...handlers)

export default server
```

**jsdom에서 fetch 폴리필을 따로 넣을 필요 없다.** axios(XHR)든 fetch든 그대로 가로챈다.

> **여기까지 확인:** `npm run dev` → 브라우저 콘솔에 `[MSW] Mocking enabled.`가 찍히면 된다.
> `check` / `lint` / `build`도 모두 통과한다.

---

## 7. 페이지와 라우팅

### 페이지 — `src/pages/HomePage.tsx`

[6장](#6-api-모킹--msw-2)의 핸들러가 응답하는 `/api/products`를 호출한다.

```tsx
import { useEffect, useState } from 'react'
import axios from 'axios'

type Product = { id: string; name: string }

export default function HomePage() {
  const [products, setProducts] = useState<Product[]>([])

  useEffect(() => {
    axios
      .get<{ products: Product[] }>('/api/products')
      .then((response) => setProducts(response.data.products))
      .catch(() => setProducts([]))
  }, [])

  return (
    <div className="mx-auto max-w-2xl p-6">
      <h1 className="text-2xl font-bold text-primary">상품 목록</h1>
      <ul className="mt-4 list-disc pl-6 space-y-1">
        {products.map((product) => (
          <li key={product.id}>{product.name}</li>
        ))}
      </ul>
    </div>
  )
}
```

`text-primary`가 [5장](#5-스타일링--tailwind-css-v4)의 `@theme`에서 정의한 토큰으로 만들어진 유틸리티다.

### 라우터 — React Router v8

> **패키지 이름이 바뀌었다.** v8부터 `react-router-dom`은 없다 (7.18.2에서 멈춰 있음).
> 이제 `react-router` 하나만 설치하고 거기서 import 한다.
> ([2장](#2-3-packagejson-전체-교체)에 이미 포함돼 있다.)

`src/routes.tsx`:

```tsx
import HomePage from './pages/HomePage'

const routes = [{ path: '/', element: <HomePage /> }]

export default routes
```

`src/App.tsx` — [5장](#5-스타일링--tailwind-css-v4)에서 만든 임시 화면을 라우터로 교체한다.

```tsx
import { RouterProvider, createBrowserRouter } from 'react-router'

import routes from './routes'

const router = createBrowserRouter(routes)

export default function App() {
  return <RouterProvider router={router} />
}
```

> **여기까지 확인:** `npm run dev` → "상품 목록" 아래에 맥주 · 치킨이 보이면 된다.
> `check` / `lint` / `build`도 모두 통과한다.

---

## 8. 테스트 — Vitest + Testing Library

앞 장까지 만든 화면을 그대로 테스트한다.
`vite.config.ts`를 재사용하므로 `jest.config.js`나 별도 트랜스폼 설정이 필요 없다.

> `@testing-library/dom`이 `package.json`에 **명시적으로 들어 있어야 한다.**
> `@testing-library/react@16`부터 직접 의존성이 아니라 peer dependency(`^10.0.0`)로 빠졌다.
> npm 7+는 peer를 자동 설치하므로 안 적어도 터지지는 않는다. 다만 **그 경우 캐럿 범위로 들어와
> 버전 고정이 깨진다.** 정확한 버전을 박으려면 직접 적어야 한다.

### `vite.config.ts`에 `test` 블록 추가

`defineConfig`를 `vite`가 아니라 **`vitest/config`**에서 가져오도록 바꾼다.

```typescript
import { configDefaults, defineConfig } from 'vitest/config'
import react from '@vitejs/plugin-react'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [react(), tailwindcss()],
  server: {
    port: 8080,
    strictPort: true,
  },
  test: {
    environment: 'jsdom',
    setupFiles: ['./src/setupTests.ts'],
    css: true,
    exclude: [...configDefaults.exclude, 'e2e/**'],
  },
})
```

> **`exclude`를 빠뜨리면 [11장](#11-e2e-테스트--playwright)에서 터진다.**
> Vitest의 기본 `include`는 `**/*.{test,spec}.?(c|m)[jt]s?(x)`라서 Playwright의 `e2e/*.spec.ts`까지 집어삼킨다.
> 그러면 `npm test`가 Playwright 테스트를 Vitest로 실행하려다 실패한다.
> `configDefaults.exclude`(`node_modules`, `.git`)를 펼친 뒤 `e2e/**`를 더해야 기본값을 잃지 않는다.

### `src/setupTests.ts`

```typescript
import { afterAll, afterEach, beforeAll } from 'vitest'
import { cleanup } from '@testing-library/react'
import '@testing-library/jest-dom/vitest'

import server from './mocks/server'

beforeAll(() => server.listen({ onUnhandledRequest: 'error' }))

afterEach(() => {
  cleanup()
  server.resetHandlers()
})

afterAll(() => server.close())
```

> **여기가 가장 많이 틀리는 지점이다.** 반드시 `/vitest` 서브패스로 import 해야 한다.
>
> - 인터넷 예제에 흔한 `'@testing-library/jest-dom/extend-expect'`는 **v6에서 삭제됐다.**
>   v7의 exports에는 `.`, `./vitest`, `./matchers`, `./jest-globals`만 있다.
> - 그렇다고 `'@testing-library/jest-dom'`(루트)로 바꾸면 **여전히 실패한다.**
>   루트 진입점은 전역 `expect`를 기대하는데 Vitest는 기본값이 `globals: false`라서
>   `ReferenceError: expect is not defined`가 난다.

두 번째 함정:

> **`cleanup()`을 직접 불러야 한다.** `globals: false`에서는 `@testing-library/react`가
> 전역 `afterEach`를 찾지 못해 자동 cleanup을 등록하지 못한다.
> 빼먹으면 한 파일에 테스트를 두 개 이상 쓰는 순간 이전 렌더가 DOM에 남아
> 같은 요소가 2개로 잡히는 식으로 오염된다.

### 테스트 예시 `src/App.test.tsx`

`globals: false`이므로 `describe`/`it`/`expect`를 명시적으로 import 한다.

```tsx
import { describe, expect, it } from 'vitest'
import { render, screen } from '@testing-library/react'

import App from './App'

describe('App', () => {
  it('renders products from the API', async () => {
    render(<App />)

    expect(screen.getByText('상품 목록')).toBeInTheDocument()
    expect(await screen.findByText('맥주')).toBeInTheDocument()
  })
})
```

> **여기까지 확인:** `npm test` → 1 passed.
> `check` / `lint` / `format` / `build`도 모두 통과한다.

---

## 9. HTTP 클라이언트 — axios

**이 가이드는 axios를 쓴다.** [2장의 `package.json`](#2-3-packagejson-전체-교체)에 이미 들어 있고,
[7장 `HomePage.tsx`](#페이지--srcpageshomepagetsx)가 이걸로 `/api/products`를 호출한다.
**빼면 7장 코드가 그대로 깨지므로 지우지 말 것.**

`fetch`는 Node 24와 모든 최신 브라우저에 내장돼 있어 그것만으로도 된다.
그럼에도 axios를 기본으로 두는 이유는 두 가지다.

- **에러 처리** — `fetch`는 404/500에도 reject하지 않는다. 호출마다 `res.ok`를 직접 검사해야 하고,
  빼먹으면 에러 응답 본문을 정상 데이터로 취급하게 된다.
  axios는 4xx/5xx가 `.catch`로 떨어져서 7장의 `.then(...).catch(...)`만으로 끝난다.
- **인터셉터** — 토큰 주입, 401 재시도, 공통 에러 로깅을 한 곳에 모을 수 있다.
  프로젝트가 커질 때 `fetch` 래퍼를 직접 만드는 것과 같은 일을 미리 해둔 셈이다.

`response.data`로 파싱된 JSON이 바로 나오는 것(= `await res.json()` 생략)도 덤이다.

> MSW는 axios(XHR)든 `fetch`든 똑같이 가로채므로, 나중에 마음이 바뀌어도
> [6장](#6-api-모킹--msw-2)의 핸들러는 손댈 필요가 없다.

---

## 10. 환경 변수

**`dotenv`를 설치하지 않는다.** 프론트엔드 번들러 프로젝트에서는 불필요하다.
Vite가 `.env` 파일을 기본 지원한다.

`.env.local` (템플릿 `.gitignore`의 `*.local` 규칙에 걸려 자동으로 커밋 제외된다):

```dotenv
VITE_API_BASE_URL=http://localhost:3000
```

```typescript
const baseUrl = import.meta.env.VITE_API_BASE_URL
```

> `VITE_` 접두사가 붙은 변수만 클라이언트 번들에 노출된다. 접두사 없는 변수는 주입되지 않으므로
> 비밀 값이 실수로 번들에 섞이는 것을 막아준다.

---

## 11. E2E 테스트 — Playwright

```powershell
npm i -D @playwright/test@1.62.1
npx playwright install chromium
```

`playwright.config.ts`:

```typescript
import { defineConfig } from '@playwright/test'

export default defineConfig({
  testDir: './e2e',
  use: {
    baseURL: 'http://localhost:8080',
  },
  webServer: {
    command: 'npm run dev',
    url: 'http://localhost:8080',
    reuseExistingServer: !process.env.CI,
  },
})
```

`webServer` 설정 덕분에 로컬에서는 dev 서버가 자동으로 뜬다.
단 **이미 8080에 서버가 떠 있으면 그걸 재사용하고, 그 경우 테스트가 끝나도 내려가지 않는다.**
직접 띄워둔 `npm run dev`를 물고 도는 게 로컬에서는 편하므로 그대로 둔다.

> **`reuseExistingServer`를 `true`로 박지 않는 이유.** CI에서는 항상 새로 띄워야 하고,
> 무엇보다 [5장의 `strictPort: true`](#플러그인-등록--viteconfigts)와 짝이다.
> `strictPort`가 없으면 8080을 다른 프로그램이 점유했을 때 Vite는 조용히 8081로 옮겨가는데,
> `baseURL`과 `webServer.url`은 8080 고정이라 **E2E가 엉뚱한 서버를 테스트하게 된다.**
> `strictPort: true`면 그 상황에서 dev 서버가 즉시 실패해 원인이 바로 드러난다.

### 테스트 예시 `e2e/home.spec.ts`

```typescript
import { expect, test } from '@playwright/test'

test('상품 목록 페이지가 뜬다', async ({ page }) => {
  await page.goto('/')

  await expect(page.getByRole('heading', { name: '상품 목록' })).toBeVisible()
})

test('API 응답을 화면에 그린다', async ({ page }) => {
  await page.goto('/')

  await expect(page.getByText('맥주')).toBeVisible()
})
```

```powershell
npx playwright test
```

### 먼저 확인할 두 가지

이 두 가지를 빼먹으면 E2E가 통째로 실패한다. 둘 다 실제로 겪은 것이다.

1. **`vite.config.ts`의 `test.exclude`에 `e2e/**`가 있어야 한다** ([8장](#8-테스트--vitest--testing-library)).
   없으면 `npm test`가 Playwright 스펙을 Vitest로 실행하려다 실패한다.
2. **브라우저용 MSW 워커가 켜져 있어야 한다** ([6장](#브라우저용--dev-서버에서-쓴다)).
   없으면 API 테스트뿐 아니라 **heading 테스트까지 실패한다.**
   `/api/products`가 `index.html`을 받아 `products.map()`에서 크래시하면서 페이지 전체가 비기 때문이다.

### E2E 코드는 `npm run check`가 검사하지 않는다

알아두고 있어야 한다. 템플릿의 `tsconfig.app.json`은 `include: ["src"]`,
`tsconfig.node.json`은 `include: ["vite.config.ts"]`뿐이다.
따라서 `e2e/**/*.ts`와 `playwright.config.ts`는 **`tsc -b`(= `check`, `build`) 대상에서 빠진다.**
Playwright는 TS를 실행할 뿐 타입 검사를 하지 않으므로, E2E 파일의 타입 오류는 아무 데서도 안 잡힌다.

검사에 넣으려면 `tsconfig.e2e.json`을 만들고 루트 `tsconfig.json`의 `references`에 추가한다.

```json
{
  "compilerOptions": {
    "tsBuildInfoFile": "./node_modules/.tmp/tsconfig.e2e.tsbuildinfo",
    "target": "es2023",
    "lib": ["ES2023", "DOM"],
    "types": ["node"],
    "module": "nodenext",
    "moduleDetection": "force",
    "noEmit": true,
    "skipLibCheck": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true
  },
  "include": ["e2e", "playwright.config.ts"]
}
```

```json
{
  "files": [],
  "references": [
    { "path": "./tsconfig.app.json" },
    { "path": "./tsconfig.node.json" },
    { "path": "./tsconfig.e2e.json" }
  ]
}
```

`.gitignore`에 추가:

```gitignore
test-results/
playwright-report/
```

---

## 12. VS Code 설정

```powershell
New-Item -ItemType Directory -Force .vscode | Out-Null
```

`.vscode/settings.json`:

```json
{
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.oxc": "explicit"
  }
}
```

> `codeActionsOnSave`에 **boolean 값(`true`)을 쓰면 안 된다.** deprecated 됐고
> 이제 `"explicit"` / `"always"` / `"never"` 문자열을 받는다.
>
> 템플릿 `.gitignore`는 `.vscode/*`를 제외하고 있으니, 팀과 공유하려면 `!.vscode/settings.json` 예외를 추가한다.

확장: `esbenp.prettier-vscode`, `oxc.oxc-vscode`, `bradlc.vscode-tailwindcss`

---

## 13. 폴더 구조

여기까지 따라왔을 때의 실제 상태다. 템플릿이 만들었다가 [5장](#화면에-붙이기)에서 지운 것
(`src/App.css`, `src/index.css`, `src/assets/`, `public/icons.svg`)은 이미 빠져 있다.

```text
my-project/
├─ e2e/
│  └─ home.spec.ts       # 11장
├─ public/
│  ├─ favicon.svg        # 템플릿 (index.html이 참조)
│  └─ mockServiceWorker.js   # 6장 · npx msw init
├─ src/
│  ├─ components/        # 2장에서 미리 만든 빈 폴더
│  ├─ hooks/
│  ├─ mocks/
│  │  ├─ browser.ts      # 6장
│  │  ├─ handlers.ts
│  │  └─ server.ts
│  ├─ pages/
│  │  └─ HomePage.tsx    # 7장
│  ├─ services/
│  ├─ stores/
│  ├─ styles/
│  │  └─ theme.css       # 5장
│  ├─ utils/
│  ├─ App.test.tsx       # 8장
│  ├─ App.tsx
│  ├─ main.tsx
│  ├─ routes.tsx         # 7장
│  └─ setupTests.ts      # 8장
├─ .vscode/
│  └─ settings.json      # 12장
├─ .env.local            # 10장 (커밋 제외)
├─ .gitignore            # 템플릿 + 11·13장 추가분
├─ .npmrc                # 2장
├─ .nvmrc                # 2장
├─ .oxlintrc.json        # 템플릿
├─ .prettierignore       # 4장
├─ .prettierrc           # 4장
├─ index.html            # 템플릿 (5장에서 lang="ko"로 수정)
├─ package.json          # 2장에서 전체 교체
├─ package-lock.json     # 커밋한다
├─ playwright.config.ts  # 11장
├─ README.md             # 템플릿
├─ tsconfig.app.json     # 템플릿
├─ tsconfig.e2e.json     # 11장 (선택)
├─ tsconfig.json         # 템플릿 (11장 선택 시 references 추가)
├─ tsconfig.node.json    # 템플릿
└─ vite.config.ts        # 템플릿 + 5·8장 수정
```

### `.gitignore` 추가 항목

템플릿 기본값에 아래를 더한다.

```gitignore
coverage
test-results/
playwright-report/
```

`node_modules`, `dist`, `*.local`(→ `.env.local` 포함)은 템플릿에 이미 들어 있다.
`package-lock.json`은 **커밋한다** (무시하면 안 된다).

---

## 14. 상태 관리 (선택)

필요해지면 아래를 쓴다.

```powershell
npm i zustand@5.0.15                  # 클라이언트 전역 상태
npm i @tanstack/react-query@5.101.4   # 서버 상태 / 캐싱
npm i -D usehooks-ts@3.1.1            # 유틸 훅 모음
```

세 패키지 모두 React 19와 peer 충돌 없이 설치되고, Vitest에서 정상 동작하는 것을 확인했다
(zustand 스토어 갱신, react-query가 MSW 핸들러 응답을 받아오는 것, `useToggle` 포함).

대부분의 경우 `useState` + React Router의 loader만으로 충분하다. 필요해진 다음에 넣을 것.

---

## 15. 버전 올리기

버전을 고정했으므로 **올리는 건 의도적인 작업**이 된다. 그게 목적이다.

```powershell
npm outdated
```

올릴 때 지킬 것:

1. **한 번에 하나씩.** 여러 개를 같이 올리면 뭐가 깨뜨렸는지 알 수 없다.
   단 **버전이 묶인 패키지는 함께 올린다.** `vitest`와 `@vitest/coverage-v8`은 peer가
   정확히 같은 버전(`4.1.11`)으로 잠겨 있고, `react`/`react-dom`,
   `tailwindcss`/`@tailwindcss/vite`도 같은 단위로 움직인다.
2. 올린 직후 **`npm run check && npm run lint && npm run test && npm run build`를 전부 통과**시킨다.
3. 통과하면 이 문서 맨 위의 **검증일과 아래 버전 표를 갱신**한다.

특히 조심할 것:

- **`typescript`** — 린터가 지원하는 범위를 먼저 확인한다.
  현재 `typescript-eslint@8`은 `<6.1.0`까지만 지원하므로 **7.x로 올리면 안 된다.**
- **`vite`** — 메이저를 올리면 `@vitejs/plugin-react`, `@tailwindcss/vite`, `vitest`의 peer 범위를 같이 확인한다.
- **`@testing-library/*`** — 메이저 업그레이드에서 peer dependency가 자주 바뀐다 (v16에서 `@testing-library/dom`이 빠진 것처럼).

peer 범위를 확인하는 방법:

```powershell
npm view <패키지>@<버전> peerDependencies
```

---

## 16. 트러블슈팅

### 캐시 문제

```powershell
Remove-Item -Recurse -Force node_modules/.vite, dist -ErrorAction SilentlyContinue
```

### 의존성이 꼬였을 때

먼저 `node_modules`만 지우고 `npm ci`로 lock 그대로 복원해 본다.

```powershell
Remove-Item -Recurse -Force node_modules -ErrorAction SilentlyContinue
npm ci
```

그래도 안 되면 lock까지 지운다. **이때 하위 의존성이 다시 계산되므로 재현성이 끊긴다.**
이후 생성된 lock을 다시 커밋할 것.

```powershell
Remove-Item -Recurse -Force node_modules, package-lock.json -ErrorAction SilentlyContinue
npm install
```

### `ReferenceError: expect is not defined`

`setupTests.ts`의 import를 `@testing-library/jest-dom/vitest`로 바꿨는지 확인
([8장](#8-테스트--vitest--testing-library)).

### Tailwind 클래스가 적용되지 않을 때

`vite.config.ts`의 `plugins`에 `tailwindcss()`가 들어 있는지, 그리고 CSS 파일 맨 위에
`@import 'tailwindcss';`가 있는지 확인한다. v4는 `content` 배열 설정이 없으므로
"경로를 안 잡아줘서 안 나온다"는 v3식 원인은 해당하지 않는다.

### 여백/글자가 전부 작게 나올 때

CSS 어딘가에 `font-size: 62.5%`가 남아 있는지 확인한다 ([5장](#5-스타일링--tailwind-css-v4)).

### `npm test`가 Playwright 스펙을 실행할 때

Vitest의 기본 `include`가 `e2e/*.spec.ts`까지 잡은 것이다.
`vite.config.ts`의 `test.exclude`에 `e2e/**`를 넣는다 ([8장](#8-테스트--vitest--testing-library)).

### 브라우저에서 화면이 비어 보일 때

dev 서버는 알 수 없는 경로에 `index.html`을 200으로 돌려준다.
그래서 `/api/products` 요청이 HTML을 받고 `response.data.products`가 `undefined`가 되어
`products.map()`에서 크래시한다. **브라우저용 MSW 워커를 켰는지 확인한다**
([6장](#브라우저용--dev-서버에서-쓴다)).

콘솔에 `[MSW] Mocking enabled.`가 찍히면 정상이다.

### MSW 핸들러를 안 타고 실제 요청이 나갈 때

`server.listen({ onUnhandledRequest: 'error' })`를 켜두면
처리되지 않은 요청에서 바로 실패하므로 원인을 즉시 찾을 수 있다.

### `npm warn allow-scripts`

**차단된 게 아니다. 경고만 뜬 것이고 스크립트는 그대로 실행된다.**
npm 11(Node 24.19.0 동봉 11.17.0)은 아직 승인되지 않은 install script를 알려주기만 한다 —
postinstall이 있는 패키지를 실제로 설치해 스크립트가 실행되는 것을 확인했다.
승인 목록에 넣고 싶으면 안내대로 `npm approve-scripts <패키지>`를 쓴다.

이 가이드의 스택에서는 그냥 무시해도 된다. MSW 브라우저 워커는 postinstall이 아니라
`npx msw init public/ --save`로 직접 만들기 때문이다 ([6장](#1-워커-파일-복사)).

---

## 부록: 고정 버전 요약

검증일 2026-08-21 기준. 본문 스택은 [2장의 `package.json`](#2-3-packagejson-전체-교체)이 정본이고,
Playwright는 11장에서 따로 설치한다.

| | 버전 |
| --- | --- |
| Node | 24.19.0 (Krypton LTS) |
| create-vite (스캐폴더) | 9.1.2 |
| TypeScript | **6.0.3** (7.0.2는 아직 채택 금지) |
| Vite | 8.2.2 |
| React / React DOM | 19.2.8 |
| react-router | 8.3.0 |
| Tailwind CSS | 4.3.3 |
| Vitest | 4.1.11 |
| jsdom | 30.0.1 |
| @testing-library/react | 16.3.2 |
| @testing-library/jest-dom | 7.0.1 |
| MSW | 2.15.0 |
| oxlint | 1.79.0 |
| Prettier | 3.9.6 |
| Playwright | 1.62.1 |
