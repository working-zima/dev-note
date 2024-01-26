# 빠르게 시작하는 세팅 가이드

## npm

```bash
# package.json 파일 생성
npm init -y
```

## gitignore

```bash
# .gitignore 파일 생성
touch .gitignore
```

```json
// .gitignore에 추가

# dependencies
/node_modules/

# testing
/coverage/

# production
/build/
/dist/

# misc
.parcel-cache
.DS_Store

npm-debug.log*
```

## TypeScript

```bash
# 타입스크립트 설치
npm i -D typescript

# 타입스크립트 컴파일러(tsc)를 실행해 초기화하고 tsconfig.json 파일 생성
npx tsc --init
```

```json
// tsconfig.json에서 아래의 항목 주석 제거

"jsx": "preserve",
```

## ESLint

```bash
# Airbnb style guide를 지원하는 eslint v7 설치
npm i -D eslint@7

# eslint 초기화
npx eslint --init
```

```bash
# eslint 선택지에서 아래의 항목 선택

To check syntax, find problems, and enforce code style
JavaScript modules (import/export)
React
Yes
Browser
Use a popular style guide
Airbnb: https://github.com/airbnb/javascript
JavaScript
Yes
```

```bash
npm i -D eslint-import-resolver-typescript

npm i -D @typescript-eslint/parser
```

```bash
# Airbnb에서 제공하는 ESLint 구성 파일 설치

npm i -D eslint-config-airbnb

npm i -D eslint-config-airbnb-typescript
```

### 아직 대기

```javascript
extends: [
  'airbnb',
  'plugin:@typescript-eslint/recommended',
  'plugin:react/recommended',
],
```

```javascript
settings: {
  'import/resolver': {
    node: {
      extensions: ['.js', '.jsx', '.ts', '.tsx'],
    },
  },
},
```

```javascript
rules: {
  'no-use-before-define': 'off',
  "no-shadow": "off",
  "@typescript-eslint/no-shadow": ["error"],
  '@typescript-eslint/no-use-before-define': ['error'],
  'react/jsx-filename-extension': ['warn', { extensions: ['.tsx'] }],
  'import/extensions': [
    'error',
    'ignorePackages',
    {
      js: 'never',
      jsx: 'never',
      ts: 'never',
      tsx: 'never',
    },
  ],
},
```

```json
// .eslintignore 추가

# dependencies
/node_modules/

# testing
/coverage/

# production
/build/
/dist/

# misc
.parcel-cache
.DS_Store

npm-debug.log*

*.css
*.svg
```

## Prettier

```bash
npm i -D prettier
```

```json
// prettierrc.json에서 아래의 항목 추가

{
  "trailingComma": "es5",
  "tabWidth": 4,
  "semi": false,
  "singleQuote": true
}
```

```json
// .prettierignore

# dependencies
/node_modules/

# testing
/coverage/

# production
/build/
/dist/

# misc
.parcel-cache
.DS_Store

# testing
/coverage/
```

## ESLint, Prettier 호환

```bash
npm i -D eslint-config-prettier
```

```javascript
// .eslintrc.js에 아래의 항목 추가

extends: [
  ...
  'prettier',
  'prettier/prettier',
],
```

### ESLint가 Prettier 규칙을 사용

```bash
npm i -D eslint-plugin-prettier
```

```javascript
extends: [
  ...
  "plugin:prettier/recommended"
]
```
