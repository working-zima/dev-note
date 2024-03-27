# 빠르게 시작하는 개발 환경 가이드

## Fast Node Manager 설치

### Mac 사용자

```bash
brew install fnm

# zhsrc profile 열기

open ~/.zshrc

# zhsrc profile에 아래 코드 추가

eval "$(fnm env)"
```

### Window 사용자

Windows 사용자는 [Scoop](https://scoop.sh/) 또는 [Chocolatey](https://chocolatey.org/)를 사용해 `fnm`을 설치.

```bash
scoop install fnm

# 또는

choco install fnm
```

### Node 설치 및 설정

```bash
fnm ls-remote

fnm install --lts

fnm use lts-latest

fnm default $(fnm current)
```

## 프로젝트 생성

```bash
mkdir my-project

cd my-project
```

## 프로젝트 Node 버전 설정

### fnm 기본 버전 사용

```bash
fnm use default
```

### 프로젝트 Node 버전 `.nvmrc` 파일에 기록

```bash
echo "$(fnm current)" > .nvmrc
```

## npm 패키지 초기화(생성)

### `package.json` 파일 생성

```bash
npm init -y
```

## gitignore

### `.gitignore` 파일 생성

```bash
touch .gitignore
```

### .gitignore에 아래 설정 추가

[ignore-setting](../appendix/ignore-setting.md)

## TypeScript

### TypeScript 설치 및 초기화

```bash
npm i -D typescript

npx tsc --init
```

### `tsconfig.json` 파일에서 jsx 관련 항목 주석 제거 후 수정

```json
"jsx": "react-jsx",
```

## ESLint

ESLint v8 설정

```bash
npm i -D eslint

npx eslint --init
```

```bash
>
$ > To check syntax, find problems, and enforce code style
$ > JavaScript modules (import/export)
$ > React
$ > Yes(TypeScript)
$ > Browser
$ > Use a popular style guide
$ > XO
```

### 린트 스타일 세팅

타입스크립트의 경우 ESLint v8 에서 에어비엔비 스타일을 지원하지 않아 XO 스타일을 제거하고 에어비엔비 스타일로 변경

```bash
npm uninstall eslint-config-xo \
eslint-config-xo-typescript

npm i -D eslint-config-airbnb \
eslint-plugin-import \
eslint-plugin-react \
eslint-plugin-react-hooks \
eslint-plugin-jsx-a11y
```

### 설정 파일 세팅

.eslintrc.js 파일 수정

```javascript
module.exports = {
  env: {
    browser: true,
    es2021: true,
    jest: true,
  },
  extends: [
    'airbnb',
    'plugin:@typescript-eslint/recommended',
    'plugin:react/recommended',
    'plugin:react/jsx-runtime',
  ],
  parser: '@typescript-eslint/parser',
  parserOptions: {
    ecmaVersion: 'latest',
    sourceType: 'module',
  },
  plugins: [
    'react',
    'react-hooks',
    '@typescript-eslint',
  ],
  settings: {
    'import/resolver': {
      node: {
        extensions: ['.js', '.jsx', '.ts', '.tsx'],
      },
    },
  },
  rules: {
    indent: ['error', 2],
    'no-trailing-spaces': 'error',
    curly: 'error',
    'brace-style': 'error',
    'no-multi-spaces': 'error',
    'space-infix-ops': 'error',
    'space-unary-ops': 'error',
    'no-whitespace-before-property': 'error',
    'func-call-spacing': 'error',
    'space-before-blocks': 'error',
    'keyword-spacing': ['error', { before: true, after: true }],
    'comma-spacing': ['error', { before: false, after: true }],
    'comma-style': ['error', 'last'],
    'comma-dangle': ['error', 'always-multiline'],
    'space-in-parens': ['error', 'never'],
    'block-spacing': 'error',
    'array-bracket-spacing': ['error', 'never'],
    'object-curly-spacing': ['error', 'always'],
    'key-spacing': ['error', { mode: 'strict' }],
    'arrow-spacing': ['error', { before: true, after: true }],
    'import/no-extraneous-dependencies': ['error', {
      devDependencies: [
        '**/*.test.js',
        '**/*.test.jsx',
        '**/*.test.ts',
        '**/*.test.tsx',
      ],
    }],
    'import/extensions': ['error', 'ignorePackages', {
      js: 'never',
      jsx: 'never',
      ts: 'never',
      tsx: 'never',
    }],
    'react/jsx-filename-extension': [2, {
      extensions: ['.js', '.jsx', '.ts', '.tsx'],
    }],
    'jsx-a11y/label-has-associated-control': ['error', { assert: 'either' }],
  },
};
```

### ESLint v7 (앞의 ESLint v8을 설치하지 않은 경우 선택)

ESLint v7으로 Airbnb style guide를 바로 사용할 경우

```bash
npm i -D eslint@7

npx eslint --init
```

### eslint 선택지에서 아래의 항목 선택

```bash
? How would you like to use ESLint?
❯ To check syntax, find problems, and enforce code style

? What type of modules does your project use?
❯ JavaScript modules (import/export)

? Which framework does your project use?
❯ React

? Does your project use TypeScript?
› Yes

? Where does your code run?
✔ Browser

? How would you like to define a style for your project?
❯ Use a popular style guide

? Which style guide do you want to follow?
❯ XO: https://github.com/xojs/eslint-config-xo-typescript

? What format do you want your config file to be in?
❯ JavaScript

? Would you like to install them now?
› Yes

? Which package manager do you want to use?
❯ npm
```

### ESLint v7 용 `eslintrc`

```javascript
module.exports = {
  env: {
    browser: true,
    es2021: true,
    jest: true,
  },
  extends: [
    'airbnb',
    'plugin:@typescript-eslint/recommended',
    'plugin:react/recommended',
    'plugin:react/jsx-runtime',

  ],
  parser: '@typescript-eslint/parser',
  parserOptions: {
    ecmaFeatures: {
      jsx: true,
    },
    ecmaVersion: 'latest',
    sourceType: 'module',
  },
  plugins: [
    'react',
    '@typescript-eslint',
  ],
  settings: {
    'import/resolver': {
      node: {
        extensions: ['.js', '.jsx', '.ts', '.tsx'],
      },
    },
  },
  rules: {
    indent: ['error', 2],
    'no-trailing-spaces': 'error',
    curly: 'error',
    'brace-style': 'error',
    'no-multi-spaces': 'error',
    'space-infix-ops': 'error',
    'space-unary-ops': 'error',
    'no-whitespace-before-property': 'error',
    'func-call-spacing': 'error',
    'space-before-blocks': 'error',
    'keyword-spacing': ['error', { before: true, after: true }],
    'comma-spacing': ['error', { before: false, after: true }],
    'comma-style': ['error', 'last'],
    'comma-dangle': ['error', 'always-multiline'],
    'space-in-parens': ['error', 'never'],
    'block-spacing': 'error',
    'array-bracket-spacing': ['error', 'never'],
    'object-curly-spacing': ['error', 'always'],
    'key-spacing': ['error', { mode: 'strict' }],
    'arrow-spacing': ['error', { before: true, after: true }],
    'import/no-extraneous-dependencies': ['error', {
      devDependencies: [
        '**/*.test.js',
        '**/*.test.jsx',
        '**/*.test.ts',
        '**/*.test.tsx',
      ],
    }],
    'import/extensions': ['error', 'ignorePackages', {
      js: 'never',
      jsx: 'never',
      ts: 'never',
      tsx: 'never',
    }],
    'react/jsx-filename-extension': [2, {
      extensions: ['.js', '.jsx', '.ts', '.tsx'],
    }],
    'react/prop-types': [2, { skipUndeclared: true }],
    'react/require-default-props': 'off',
    'jsx-a11y/label-has-associated-control': ['error', { assert: 'either' }],
    "no-unused-vars": "off",
    "@typescript-eslint/no-unused-vars": "warn"
  },
};
```

### `.eslintignore` 파일 작성

```json
touch .eslintignore
```

### `.eslintignore` ignore 세팅

[ignore-setting](../appendix/ignore-setting.md)

## React

```bash
npm i react react-dom

npm i -D @types/react @types/react-dom
```

### React 기본 파일 생성

```bash
touch index.html src/main.tsx src/App.tsx
```

#### index.html

```html
<!DOCTYPE html>
<html lang="ko">
  <head>
    <meta charset="UTF-8">
    <title>React Demo App</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="./src/main.tsx"></script>
  </body>
</html>
```

#### main.tsx

```tsx
import ReactDOM from 'react-dom/client';

import App from './App';

function main() {
  const container = document.getElementById('root');
  if (!container) {
    return;
  }

  const root = ReactDOM.createRoot(container);
  root.render(<App />);
}

main();
```

## Parcel

```bash
npm i -D parcel
```

### 정적 파일을 복사하도록 도와주는 Parcel 리포터(reporter) 설치

```bash
npm i -D parcel-reporter-static-files-copy
```

#### 정적 파일을 위한 static 폴더 생성

```bash
mkdir -p static

### .parcelrc 파일 작성

```bash
touch .parcelrc
```

```json
{
  "extends": ["@parcel/config-default"],
  "reporters": ["...", "parcel-reporter-static-files-copy"]
}
```

## Jest

### React Testing Library 설치

```bash
npm i -D jest @types/jest @swc/core @swc/jest \
    jest-environment-jsdom \
    @testing-library/react @testing-library/jest-dom@5.16.4
```

### `jest.config.js`에 아래 설정 추가

```bash
touch jest.config.js
```

```javascript
module.exports = {
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: [
    '@testing-library/jest-dom/extend-expect',
  ],
  transform: {
    '^.+\\.(t|j)sx?$': ['@swc/jest', {
      jsc: {
        parser: {
          syntax: 'typescript',
          jsx: true,
          decorators: true,
        },
        transform: {
          react: {
            runtime: 'automatic',
          },
        },
      },
    }],
  },
  testPathIgnorePatterns: [
    '<rootDir>/node_modules/',
    '<rootDir>/dist/',
  ],
};
```

### 테스트 파일 생성

```bash
touch src/App.test.tsx
```

## MSW 패키지 설치

```bash
npm i -D msw@0.36.4
```

### MSW 관련 파일 생성

```bash
mkdir -p src/mocks

touch src/setupTests.ts, src/mocks/server.ts, src/mocks/handler.ts
```

### Jest 설정 변경

`jest.config.js` 에 `setupFilesAfterEnv` 의 속성에 `setupTests.ts` 파일 추가

```javascript
  setupFilesAfterEnv: [
    '<rootDir>/src/setupTests.ts',
  ],
```

## React Router

### 리액트 라우터 설치

```bash
npm install react-router-dom
```

### 라우터 객체 생성

```bash
touch src/routes.tsx
```

### 브라우저 라우터 내려주기

#### App.tsx

```typescript
import { RouterProvider, createBrowserRouter } from 'react-router-dom';

import routes from './routes';

// router 객체 생성
const router = createBrowserRouter(routes);

export default function App() {
  return (
    <RouterProvider router={router} />
  );
}
```

## styled-components

### styled-components 설치

```bash
npm i styled-components@5.3.10

npm i -D @types/styled-components @swc/plugin-styled-components
```

### styled-reset 설치

styled-components용 Reset CSS

```bash
npm i styled-reset
```

#### App 컴포넌트에서 styled-reset 사용

```typescript
import { Reset } from 'styled-reset';
```

### swc 세팅

`.swcrc` 파일 생성

```bash
touch src/.swcrc
```

```json
{
  "jsc": {
    "experimental": {
      "plugins": [
        [
          "@swc/plugin-styled-components",
          {
            "displayName": true,
            "ssr": true
          }
        ]
      ]
    }
  }
}
```

### Theme 세팅

```bash
mkdir -p src/styles

touch src/styles/defaultTheme.ts src/styles/styeld.d.ts src/styles/Theme.ts
```

#### 기본 Theme 정의

`defaultTheme.ts`

```typescript
const defaultTheme = {
  colors: {
    background: '#FFFFFF',
    text: '#000000',
    primary: '#F00000',
    secondary: '#00FFFF',
  },
};

export default defaultTheme;
```

#### 타입 문제 해결

`styeld.d.ts`

```typescript
import 'styled-components';

import Theme from './Theme';

declare module 'styled-components' {
  export interface DefaultTheme extends Theme {
  }
}
```

`Theme.ts`

```typescript
import defaultTheme from './defaultTheme';

type Theme = typeof defaultTheme;

export default Theme;
```

### Global Style

```bash
touch src/styles/GlobalStyle.ts
```

`GlobalStyle.ts`에 스타일에 관련된 편의를 위한 세팅을 전역 스타일로 지정

```typescript
import { createGlobalStyle } from 'styled-components';

const GlobalStyle = createGlobalStyle`
  html {
    box-sizing: border-box;
  }

  *,
  *::before,
  *::after {
    box-sizing: inherit;
  }

  html {
    font-size: 62.5%;
  }

  body {
    font-size: 1.6rem;
    background: ${(props) => props.theme.colors.background};
    color: ${(props) => props.theme.colors.text}
  }

  :lang(ko) {
    h1, h2, h3 {
      word-break: keep-all;
    }
  }
`;

export default GlobalStyle;
```

### App 컴포넌트에 적용

`App.tsx`

```typescript
import { createBrowserRouter, RouterProvider } from 'react-router-dom';

import { ThemeProvider } from 'styled-components';

import { Reset } from 'styled-reset';

import routes from './routes';

import GlobalStyle from './styles/GlobalStyle';
import DefaultTheme from './styles/defaultTheme';

const router = createBrowserRouter(routes);

export default function App() {
  return (
    <ThemeProvider theme={DefaultTheme}>
      <Reset />
      <GlobalStyle />
      <RouterProvider router={router} />
    </ThemeProvider>
  );
}
```

### window.matchMedia 문제 해결

`setupTests.ts`에 아래의 코드 추가

```typescript
Object.defineProperty(window, 'matchMedia', {
  writable: true,
  value: jest.fn().mockImplementation((query) => ({
    matches: false,
    media: query,
    onchange: null,
    addListener: jest.fn(), // deprecated
    removeListener: jest.fn(), // deprecated
    addEventListener: jest.fn(),
    removeEventListener: jest.fn(),
    dispatchEvent: jest.fn(),
  })),
});
```

`jest.config`

```typescript
module.exports = {
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: [
    '@testing-library/jest-dom/extend-expect',
    '<rootDir>/src/setupTests.ts',
  ],
  transform: {
    '^.+\\.(t|j)sx?$': ['@swc/jest', {
      jsc: {
        parser: {
          syntax: 'typescript',
          jsx: true,
          decorators: true,
        },
        transform: {
          react: {
            runtime: 'automatic',
          },
        },
      },
    }],
  },
  testPathIgnorePatterns: [
    '<rootDir>/node_modules/',
    '<rootDir>/dist/',
  ],
};
```

## Axios

Axios 설치

```bash
npm i axios
```

## `package.json` 수정

### `"main": "index.js"` 변경

```json
"source": "./index.html",
```

### `"scripts"` 변경

```json
"scripts": {
  "start": "parcel --port 8080",
  "build": "parcel build",
  "check": "tsc --noEmit",
  "lint": "eslint --fix --ext .js,.jsx,.ts,.tsx .",
  "test": "jest",
  "coverage": "jest --coverage --coverage-reporters html",
  "watch:test": "jest --watchAll"
},
```

## 선택

### usehooks-ts 설치

```bash
npm i usehooks-ts
```

#### 의존성 설치

```bash
npm i tsyringe reflect-metadata usestore-ts
```

- `src/main.tsx` 와 `src/setupTests.ts` 에 `import 'reflect-metadata';` 추가

- `tsconfig.json` 에 `experimentalDecorators` 와 `emitDecoratorMetadata` 속성 `true` 로 변경

- `jest.config.js` 세팅 수정하여 `reflect-metadata` 적용

```javascript
setupFilesAfterEnv: [
  '@testing-library/jest-dom/extend-expect',
  '<rootDir>/src/setupTests.ts', // 이 부분 없다면 추가
],
```

### 프로젝트에 알 수 없는 오류가 있을 때

```bash
rm -rf .parcel-cache
rm -rf dist
```

### CodeceptJS

#### CodeceptJS 설치

```bash
npx codeceptjs init

# > Where are your tests located? ./tests/**/*_test.js
# > What helpers do you want to use? Playwright
# > where should logs, screenshots, and reports to be stored? (./output)
# > Do you want localization for tests? English (no localization)
# > [Playwright] Base url of site to be tested (http://localhost)
# > [Playwright] Show browser window n
# > [Playwright] Browser in which testing will be performed. Possible options: chromium, firefox or webkit (chromium)
# > Feature which is being tested (ex: account, login, etc)
# > Filename of a test (_test.js)
```

### VSC

#### `.vscode/settings.json` 파일 작성

```bash
mkdir .vscode
touch .vscode/settings.json
```

```json
{
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}
```

## 부가적인 세팅

2023년 5월 기준 CodeceptJS 문제로 에러가 발생할 수 있음. 그래서 부가적인 세팅이 필요

1. 의존성 설치 npm i -D eslint-plugin-codeceptjs playwright @codeceptjs/configure

2. codecept.conf.ts 에서 config의 타입 제거 및 수정.

3. tests/steps.d.ts 파일 생성

4. steps_files.js 파일 tests 폴더로 이동

5. CodeceptJS가 내부적으로 ts-node를 쓰기 때문에 tsconfig.json 파일에 ts-node 설정을 추가

6. /tests/.eslintrc.js 파일 생성

7. .gitignore 에 output/ 추가

## 참고 링크

- [megaptera-kr/textbook/start-megaptera-shop-client/](https://github.com/megaptera-kr/textbook/tree/main/start-megaptera-shop-client)