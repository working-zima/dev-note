# 2주차 과제 ESLint 세팅 공부

## package.json

```json
{
  "name": "frontend-survival-week02-answer",
  "version": "1.0.0",
  "description": "",
  "source": "index.html",
  "scripts": {
    "start": "parcel --port 8080",
    "build": "parcel build",
    "serve": "servor dist index.html 8080 --reload",
    "check": "tsc --noEmit",
    "lint": "eslint --fix --ext .js,.jsx,.ts,.tsx .",
    "test": "jest",
    "coverage": "jest --coverage --coverage-reporters html",
    "watch:test": "jest --watchAll",
    "codeceptjs": "codeceptjs run --steps",
    "codeceptjs:headless": "HEADLESS=true codeceptjs run --steps",
    "codeceptjs:ui": "codecept-ui --app",
    "ci": "concurrently -s first -k 'npm:serve' 'npm:codeceptjs'"
  },
  "keywords": [],
  "author": "wholeman",
  "license": "ISC",
  "devDependencies": {
    "@codeceptjs/configure": "^0.10.0",
    "@codeceptjs/ui": "^0.4.7",
    "@swc/core": "^1.3.35",
    "@swc/jest": "^0.2.24",
    "@testing-library/jest-dom": "^5.16.5",
    "@testing-library/react": "^13.4.0",
    "@types/jest": "^29.4.0",
    "@types/react": "^18.0.27",
    "@types/react-dom": "^18.0.10",
    "@typescript-eslint/eslint-plugin": "^5.51.0",
    "@typescript-eslint/parser": "^5.51.0",
    "codeceptjs": "^3.3.7",
    "concurrently": "^7.6.0",
    "eslint": "^8.33.0",
    "eslint-config-airbnb": "^19.0.4",
    "eslint-plugin-codeceptjs": "^1.3.0",
    "eslint-plugin-import": "^2.27.5",
    "eslint-plugin-jsx-a11y": "^6.7.1",
    "eslint-plugin-react": "^7.32.2",
    "eslint-plugin-react-hooks": "^4.6.0",
    "jest": "^29.4.2",
    "jest-environment-jsdom": "^29.4.2",
    "parcel": "^2.8.3",
    "playwright": "^1.30.0",
    "process": "^0.11.10",
    "servor": "^4.0.2",
    "typescript": "^4.9.5"
  },
  "dependencies": {
    "parcel-reporter-static-files-copy": "^1.5.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0"
  }
}
```

## .eslintrc.js

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
  },
};
```

## ESLint 옵션

### env

코드가 실행되는 환경을 설정하는 데 사용됩니다.\
환경마다 전역(global) 변수를 통해 접근이 가능한 고유한 객체들을 인식하고 코드를 검사합니다.

```javascript
env: {
  browser: true,
  es2021: true,
  jest: true,
},
```

### parser, parserOptions

ESLint는 순수한 자바스크립트 코드만 이해할 수 있습니다.
따라서 자바스크립트의 확장 문법이나 최신 문법으로 작성한 코드는 그에 상응하는 파서(parser)를 사용하도록 설정해야합니다.
기본값은 `espree`라는 parser을 사용하지만 몇 가지 새로운 문법이나 확장 문법은 충분히 해석되지 않을 수 있습니다.
따라서 일반적으로 `JavaScript`의 경우 `@babel/eslint-parser`를 사용하고 `TypeScript` 의 경우 `@typescript-eslint/parser`를 사용합니다.

```json
// package.json

"devDependencies": {
  "@typescript-eslint/parser": "^5.51.0",
}
```

```javascript
// .eslintrc.js

parser: '@typescript-eslint/parser',
```

### plugins

ESLint에서 기본으로 제공되는 규칙(rule) 외에도 추가적인 규칙(rule)을 사용할 수 있는 상태가 되도록 만들어주는 다양한 플러그인(plugin)을 사용할 수 있게 해줍니다.\
`eslint-plugin-*`의 형태의 플러그인 패키지를 설치하여 사용합니다.\
새로운 규칙을 단순히 설정이 가능한 상태로 만들어주기만 하기 때문에 `extends` 옵션이나 `rules` 옵션을 통해서 추가 설정을 해줘야 합니다.

```json
// package.json

"devDependencies": {
// TypeScript 코드베이스에 대한 Lint 규칙을 제공하는 ESLint 플러그인입니다.
  "@typescript-eslint/eslint-plugin": "^5.51.0",

  "eslint-plugin-codeceptjs": "^1.3.0",

// 모듈 가져오기를 관리하는 데 도움이 되는 플러그인입니다. ES2015+(ES6+) 가져오기/내보내기 구문 린트를 지원하고 파일 경로 및 가져오기 이름의 철자 오류 문제를 방지합니다.
  "eslint-plugin-import": "^2.27.5",

// eslint가 JSX 코드에 액세스할 수 있도록 도와주는 플러그인입니다. 코드의 구조를 계층적으로 표현하는 트리 구조(AST)로 변환하고, JSX 내의 구조를 분석하여 웹 접근성 관련 문제를 찾아냅니다.
  "eslint-plugin-jsx-a11y": "^6.7.1",

  "eslint-plugin-react": "^7.32.2",

// React용 Hooks API의 규칙을 시행합니다 .
  "eslint-plugin-react-hooks": "^4.6.0",
}
```

`.eslintrc`의 `plugins`에서 사용된 플러그인은 `"eslint-plugin-react"`와 `@typescript-eslint/eslint-plugin`입니다.\
`eslint-plugin-codeceptjs`을 제외한 나머지 플러그인은 아래에 나올 `extends`에서 사용됩니다.

```javascript
// .eslintrc.js

plugins: [
  'react',
  '@typescript-eslint',
],
```

### settings

일부 ESLint 플러그인은 추가적인 설정이 가능하며 이런 경우 `settings` 옵션을 사용합니다.

```javascript
// .eslintrc.js

settings: {
  'import/resolver': {
    node: {
      extensions: ['.js', '.jsx', '.ts', '.tsx'],
    },
  },
},
```

위의 코드는 `eslint-plugin-import`의 추가적인 설정이며 플러그인에서 모듈을 찾을 때 어떤 방식으로 찾을지를 설정합니다.\
기본값은 `['.js']`입니다.\
React 공유 구성을 사용하는 경우에는 `['.jsx']`를 추가합니다.\
TypeScript를 사용하는 경우에는 `[.ts]`를 추가합니다.\
TypeScript 및 React를 사용하는 경우 `['.tsx']`도 지정해야 합니다.

### extends

미리 정의된 규칙들을 ESLint 설정에 확장하는데 사용합니다.\
`eslint-config-*`의 형태인 패키지를 설치하고 사용할 수 있습니다.

```json
// package.json

// Airbnb의 .eslintrc를 확장 가능한 공유 구성으로 제공합니다.
"devDependencies": {
  "eslint-config-airbnb": "^19.0.4",
}
```

`eslint-config-*`로 시작하는 패키지는 `eslint-plugin-`로 시작하는 패키지와 `rule` 을 모아 만든 설정입니다.\
따라서 `eslint-config-*`를 설치할 때, 해당 설정에 필요한 플러그인들도 함께 설치됩니다.

예로 `eslint-config-airbnb`를 설치하게 되면 `eslint-plugin-import, eslint-plugin-jsx-a11y, eslint-plugin-react, eslint-plugin-react-hooks`가 함께 설치됩니다.

- 의존성 확인

```bash
npm info "eslint-config-airbnb@latest" peerDependencies
```

`extends`에 `*`에 해당하는 문자를 작성할 수 있습니다.

```javascript
// .eslintrc.js

// Airbnb가 공개해놓은 설정을 그대로 가져와 기반(base) 설정으로 활용합니다.
extends: [
  'airbnb',
],
```

또한 설치한 `plugins`의 규칙들을 사용하거나 `plugins`에서 제공하는 권장 설정을 사용할 수 있습니다.\
특정 설정을 사용하는 경우 `plugin:패키지네임/설정네임`, 권장 설정을 사용하는 경우 `plugin:패키지네임/recommended`와 같은 형태로 작성할 수 있습니다.

```javascript
// .eslintrc.js

extends: [
  'plugin:@typescript-eslint/recommended',
  'plugin:react/recommended',
  'plugin:react/jsx-runtime',
],
```

### rules

규칙을 세세하게 제어하기 위해서 사용되며 `extends` 옵션을 통해서 설정된 규칙을 덮어쓸 때 사용할 수 있습니다.\
규칙을 어겼을 때 경고 또는 오류를 낼 수 있도록 설정할 수 있으며 규칙을 비활성화 할 수 있습니다.\
ESLint는 `rules` 옵션으로 명시된 규칙을 `extends` 옵션을 통해서 가져온 규칙보다 우선하기 때문에 주의가 하며 사용해야 합니다.

- "off" : 비활성화
- "warn" : 경고
- "error" : 오류
