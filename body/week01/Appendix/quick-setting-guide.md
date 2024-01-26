# 빠르게 시작하는 개발 환경 가이드

## fnm 설치

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

## npm 패키지 초기화

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

### `tsconfig.json`에서 아래 항목 주석 제거

```json
"jsx": "react-jsx",
```

## ESLint

```bash
npm i -D eslint

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

### `eslintrc` 수정

```json
env: {
  browser: true,
  es2021: true,
  jest: true,
},
```

### `.eslintignore` 파일 작성

```json
touch .eslintignore
```

### `.eslintignore`에 아래 설정 추가

[ignore-setting](../appendix/ignore-setting.md)

## React

```bash
npm i react react-dom

npm i -D @types/react @types/react-dom
```

## Jest

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
    './jest.setup',
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

## Parcel

```bash
npm i -D parcel
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

## 기본 코드 작성

```bash
mkdir -p src/components
touch index.html src/main.tsx src/App.tsx src/App.test.tsx src/components/Greeting.test.tsx src/components/Greeting.tsx
```

## 기타

### 프로젝트 버전 확인

```bash
cat .nvmrc
```

### VSC

#### `.vscode/settings.json` 파일 작성

```json
{
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}