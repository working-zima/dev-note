# 야샬님의 eslintrc 설정 분석

10기 영상 강의에서의 설정을 기반으로  
오래된 React + TypeScript + Parcel 참고 자료  
[20211008](https://github.com/ahastudio/CodingLife/tree/main/20211008/react), [20220726](https://github.com/ahastudio/CodingLife/tree/main/20220726/react)  
에 나온 설정을 추가한 설정입니다.

```bash
npm install -D eslint-config-airbnb
```

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
  overrides: [
    {
      env: {
        node: true,
      },
      files: ['.eslintrc.{js,cjs}'],
      parserOptions: {
        sourceType: 'script',
      },
    },
  ],
  parserOptions: {
    ecmaVersion: 'latest',
    sourceType: 'module',
  },
  plugins: ['react'],
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
    'import/no-extraneous-dependencies': [
      'error',
      {
        devDependencies: [
          '**/*.test.js',
          '**/*.test.jsx',
          '**/*.test.ts',
          '**/*.test.tsx',
        ],
      },
    ],
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
    'react/jsx-filename-extension': [
      2,
      {
        extensions: ['.js', '.jsx', '.ts', '.tsx'],
      },
    ],
  },
};
```

## 설명

### env

코드가 실행되는 환경을 정의합니다.  
ESLint는 기본적으로 미리 선언하지 않고 접근하는 변수에 대해서는 오류를 냅니다.  
실행 환경(runtime)에서 기본적으로 제공되는 전역 객체에 대해서 설정을 통해 알려줍니다.

- browser  
  : 브라우저 환경에서 실행되는 코드를 지정합니다.  
  브라우저 환경에서 사용되는 전역 변수(`windows`) 및 API에 관련된 ESLint 규칙을 활성화합니다.

- es2021  
  : ECMAScript 2021 문법 및 기능을 사용할 수 있도록 ESLint 규칙을 활성화합니다.

- jest  
  : Jest 관련 규칙을 활성화하여 테스트 코드에 대한 특정 규칙을 검사할 수 있습니다.

### extends

ESLint 구성에서 다른 설정 파일이나 플러그인의 설정을 현재의 ESLint 설정에 추가하여 상속할 때 사용됩니다.  
Google, Facebook, Airbnb 등 수많은 세계적인 기업들이 ESLint로 자바스크립트 코드를 린트(lint)합니다.  
확장이 가능한 ESLint 설정은 npm 패키지 이름이 `eslint-config-`로 시작하며 extends 옵션에 명시할 때는 위와 같이 앞 부분을 생략해도 무방합니다.

- airbnb  
  : Airbnb의 JavaScript 스타일 가이드를 따릅니다.  
   [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)

- plugin:@typescript-eslint/recommended  
  : TypeScript용 ESLint 플러그인인 `@typescript-eslint`에서 제공하는 권장 설정을 사용합니다.

- plugin:react/jsx-runtime  
  : React JSX runtime 플러그인을 사용하여 JSX 코드를 검사합니다.

- plugin:react/jsx-runtime: React JSX runtime 플러그인을 사용합니다.

### overrides

프로젝트 내에서 일부 파일에 대해서만 살짝 다른 설정을 적용해줘야 할 때 사용합니다.  
프로젝트에 자바스크립트 파일과 타입스크립트 파일이 공존한다면 자바스크립트 파일을 기준으로 기본 설정을 하고, 타입스크립트 파일을 위한 설정은 `overrides` 옵션에 명시할 수 있습니다.

- 첫 번째 override

  - env: { node: true }
    : Node.js 환경에서 코드가 실행되는 것으로 간주합니다.  
    Node.js 전역 객체(`global`) 및 API에 관련된 ESLint 규칙을 활성화합니다.

  - files: ['.eslintrc.{js,cjs}']
    : 이(첫 번째) 오버라이드는`.eslintrc.js` 또는 `.eslintrc.cjs` 파일에서만 적용됩니다.

  - parserOptions: { sourceType: 'script' }  
    : 해당 파일이 스크립트 파일임을 나타냅니다.

### parserOptions

개발자가 작성하는 자바스크립트 코드는 실제로 브라우저와 같은 실행 환경에서 실제로 돌아가는 코드와 다른 경우가 많습니다.  
대표적인 예로 타입스크립트나 JSX와 같은 자바스크립트의 확장 문법으로 개발하거나 Babel과 같은 트랜스파일러(transpiler)를 통해 최신 문법으로 개발하는 경우를 들 수 있습니다.  
ESLint는 기본적으로 순수한 자바스크립트 코드만 이해할 수 있기 때문에 자바스크립트의 확장 문법이나 최신 문법으로 작성한 코드를 린트(lint)하기 위해서는 그에 상응하는 파서(parser)를 사용하도록 설정해줘야 합니다.

- `ecmaVersion`: 사용할 ECMAScript 버전을 설정합니다.  
  `latest`는 최신 버전을 의미합니다.

- `sourceType`: 코드의 모듈 시스템을 지정합니다.
  parser의 import 및 export 문의 형태를 올바르게 처리하는 데 도움이 됩니다.

### plugins

ESLint에는 기본으로 제공되는 규칙(rule) 외에도 추가적인 규칙(rule)을 사용할 수 있도록 만들어주는 플러그인(plugin)을 사용합니다.  
`eslint-plugin`으로 시작하는 의존성을 설차하여 사용하며 플러그인은 새로운 규칙을 단순히 설정이 가능한 상태로 만들어주기만 합니다.  
규칙을 위반하면 오류(error)를 낼지 경고(warn)를 낼지 아니면 해당 규칙을 끌지(off)에 대해서는 다음에 설명드릴 extends 옵션이나 rules 옵션을 통해서 추가 설정을 해줘야 합니다.

- `plugins`: ESLint는 React 플러그인에서 제공하는 규칙을 사용할 수 있도록 활성화됩니다.

### settings

일부 ESLint 플러그인은 추가적인 설정이 가능한데, 이런 경우에는 설정 파일의 settings 옵션을 사용합니다.

- 'import/resolver'
  : 이 속성은 `eslint-plugin-import`에서 제공하는 설정 중 하나로, 모듈 해석(resolver)에 대한 설정을 지정합니다.
  - node
    : 이 설정은 Node.js 환경에서 모듈을 해석하는 방법을 지정합니다.
    - extensions
      : 지정된 확장자에 대해 모듈을 찾을 수 있도록 합니다.  
      `.js, .jsx, .ts, .tsx` 확장자를 가진 파일을 모듈로 인식합니다.  
      이는 import 문에서 확장자를 생략할 수 있게 하거나, 해당 확장자의 파일을 찾을 때 사용됩니다.

### rules
