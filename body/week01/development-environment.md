# 1. 개발 환경

글을 읽기 전에 개발 환경 세팅은 언제든 바뀔 수 있다는 것을 알고 계셔야 합니다.\
그렇기 때문에 앞으로 나오게 될 내용이 정상적으로 작동하지 않거나 효율적인 내용이 아닐 수 있습니다.\
따라서 구체적인 내용보다는 전체적인 흐름을 파악한다는 느낌으로 읽으시는 것을 추천드립니다.

우리는 `Node.js`를 설치하고, 프로젝트를 진행할 수 있는 `Node.js` 패키지를 만듭니다.\
코드 퀄리티를 일정 수준 이상으로 유지할 수 있도록 `lint`와 `test`를 실행할 수 있는 상태를 만듭니다.

## JavaScript 개발 환경 (Node.js) 세팅

[Node.js 홈페이지](https://nodejs.org/en)에는 크게 `LTS`와 `Current` 버전을 지원합니다.\
`LTS`버전은 앞의 숫자가 짝수이며 안정적이고 장기적으로 지원합니다.\
`Current`버전은 앞의 숫자가 홀수이며 가장 최신의 업데이트를 반영합니다.\
특별한 목적이 없다면 주로 이전`LTS`와 최신 `LTS` 버전을 사용하는 것이 무난하다고 알고 있습니다.

### 노드 버전을 관리해야하는 이유

개발자마다 서로 다른 노드 버전을 사용할 수 있기 때문에 버전을 변경하며 사용하야 하는 경우가 있습니다.\
노드 설치 또는 버전 관리는 `nvm`과 `fnm` 등에서 기능을 제공하고 있습니다. 여기서는`fnm`으로 노드 버전을 관리해보겠습니다.

### fnm 설치

#### Mac, Linux 사용자

[Homebrew](https://brew.sh/)를 이용해 `fnm`을 설치할 수 있습니다.

```bash
brew install fnm
```

`~/.bashrc` 또는 `~/.zshrc`에 다음을 추가합니다.

현재 터미널에서 바로 사용하고 싶다면 아래의 명령을 그대로 입력합니다.

```bash
eval "$(fnm env)"
```

#### Windows 사용자

Windows 사용자는 [Scoop](https://scoop.sh/) 또는 [Chocolatey](https://chocolatey.org/)를 사용해 `fnm`을 설치할 수 있습니다.

```bash
scoop install fnm

# 또는

choco install fnm
```

### Node.js 설치

설치 가능한 버전 확인.

```bash
fnm ls-remote
```

LTS(Long Term Support) 버전을 설치.

```bash
fnm install --lts
```

Node.js의 최신의 LTS 버전 버전을 현재 터미널 세션에서 사용할 수 있도록 설정.

```bash
fnm use lts-latest
```

현재 사용 중인 Node.js 버전을 `fnm`의 기본 버전으로 설정.

```bash
fnm default $(fnm current)
```

설치된 상태 확인.

```bash
fnm list

fnm current
```

### NPM 업그레이드

```bash
npm install -g npm
```

### 프로젝트 폴더 생성

프로젝트 이름을 `my-project`라고 했을 때 다음과 같이 폴더를 만들고 사용할 Node.js 버전을 잡아줍니다.\
fnm이 설정된 기본 버전으로 설정된 Node.js 버전을 현재 터미널 세션에서 사용하도록 지시합니다.

```bash
mkdir my-project

cd my-project

fnm use default
```

현재 사용 중인 Node.js 버전을 확인하고 그 버전 정보를 `.nvmrc` 파일에 저장.

```bash
echo "$(fnm current)" > .nvmrc
```

나중에 시스템에 설치된 Node.js 버전과 프로젝트에서 사용하는 Node.js 버전이 다른 상황이 오더라도 `fnm use` 명령을 통해 프로젝트에서 사용하고 있는 버전을 쉽게 사용할 수 있습니다.

또는 `.nvmrc` 파일을 확인함으로써 어떤 버전으로 개발했는지 알 수 있습니다.

```bash
cat .nvmrc
```

## TypeScript + React + Jest + ESLint + Parcel(번들러, 빌드 도구, 만능 도구) 개발 환경 세팅

오래된 참고 자료:

- [React + TypeScript + Parcel](https://github.com/ahastudio/CodingLife/tree/main/20211008/react)
- [usestore-ts 세팅 예제](https://github.com/ahastudio/CodingLife/tree/main/20220726/react)

1. 먼저, 적절한 작업 폴더를 준비합니다.

   ```bash
   # mkdir 생성할폴더명

   mkdir my-app

   cd my-app
   ```

2. 터미널에서 바로 Visual Studio Code를 열수 있습니다.

   ```bash
   code .
   ```

3. npm 패키지를 준비하는 게 첫 번째 작업.

   ```bash
   npm init -y
   ```

4. 잊지 말고 `.gitignore` 파일을 작성합니다.
    `node_modules`를 커밋하는 일을 미연에 방지합니다.

   ```bash
   touch .gitignore
   ```

   `.gitignore` 파일에 Parcel 캐시 추가:

   ```json
   /node_modules/
   /dist/
   /.parcel-cache/
   ```

   또는 [여러 웹 서비스](https://www.toptal.com/developers/gitignore/api/node) 또는 [깃헙 템플릿](https://github.com/github/gitignore/blob/main/Node.gitignore)를 통해 `.gitignore`에 작성할 여러 항목을 가져오는 방법도 있습니다.

5. 타입스크립트 설정
   배포가 아닌 개발에만 사용하기 때문에 -D(devDependencies)로 사용

   ```bash
   npm i -D typescript

   npx tsc --init
   ```

6. `tsconfig.json` 파일의 jsx 속성 변경합니다.
   (주석 풀기)

   ```json
   "jsx": "react-jsx"
   ```

7. ESLint 설정

   ```bash
   npm i -D eslint

   npx eslint --init
   ```

   ```bash
   ? Ok to proceed? (y)
   ❯ y

   ? How would you like to use ESLint? …
   ❯ To check syntax, find problems, and enforce code style

   ? What type of modules does your project use? …
   ❯ JavaScript modules (import/export)

   ? Which framework does your project use? …
   ❯ React

   ? Does your project use TypeScript?
   › Yes

   ? Where does your code run? …
   ✔ Browser

   ? How would you like to define a style for your project? …
   ❯ Use a popular style guide

   ? Which style guide do you want to follow? …
   ❯ Airbnb: https://github.com/airbnb/javascript
   # 또는
   XO: https://github.com/xojs/eslint-config-xo-typescript

   ? What format do you want your config file to be in? …
   ❯ JavaScript

   ? Would you like to install them now?
   ❯ Yes

   ? Which package manager do you want to use?
   › npm
   ```

   브라우저 환경에서 돌아가는 JavaScript 모듈 사용한 TypeScript 통합 React 프로젝트에 JavaScript 설정 파일로 작성된 XO 스타일 가이드를 npm으로 패키지 설치하게 됩니다.

8. `.eslintrc.js` 파일을 적절히 수정한다.\
   아직 Jest를 설치하지 않았지만, 여기서 미리 `jest: true`를 잡아주면 좋다.

   ```json
   env: {
      browser: true,
      es2021: true,
      jest: true,
   },
   ```

9. 잊지 않고 `.eslintignore` 파일을 작성한다.

   ```json
   touch .eslintignore
   ```

   이 후 `.eslintignore` 파일에 Parcel 캐시 추가:

   ```json
   /node_modules/
   /dist/
   /.parcel-cache/
   ```

   만약 `eslint` 규칙에 맞춰 파일을 수정하고 싶다면

   ```bash
   npx eslint --fix .
   ```

   을 터미널에 입력하여 전체 파일을 수정할 수 있습니다.

10. 리액트 설치

    ```bash
    npm i react react-dom

    npm i -D @types/react @types/react-dom
    ```

11. 테스팅 도구 설치

    ```bash
    npm i -D jest @types/jest @swc/core @swc/jest \
        jest-environment-jsdom \
        @testing-library/react @testing-library/jest-dom@5.16.4
    ```

12. `jest.config.js` 파일을 작성해 테스트에서 SWC를 사용하자.

    ```bash
    touch jest.config.js
    ```

    1. [jest.config.js에 작성할 내용 참고](https://github.com/ahastudio/CodingLife/blob/main/20220726/react/jest.config.js)

    💡 **_주의_**
    \*\*\*\*`@testing-library/jest-dom` 6.0.0 버전부터 변경된 내용이 있습니다.

    최신 버전을 사용하는 경우,
    참고 문서 중 `setupFilesAfterEnv`의
    `'@testing-library/jest-dom/extend-expect'` 설정 대신
    `jest-setup.js`파일에 `import '@testing-library/jest-dom'`를 추가해주세요.

    ```javascript
    module.exports = {
      testEnvironment: 'jsdom',
      setupFilesAfterEnv: ['@testing-library/jest-dom/extend-expect'],
      transform: {
        '^.+\\.(t|j)sx?$': [
          '@swc/jest',
          {
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
          },
        ],
      },
      testPathIgnorePatterns: ['<rootDir>/node_modules/', '<rootDir>/dist/'],
    };
    ```

    [최신 버전 참고 문서](https://github.com/testing-library/jest-dom#usage)

13. Parcel 설치

    ```bash
    npm i -D parcel
    ```

14. `package.json` 파일의 scripts를 적절히 수정한다.

    1. [scripts 참고](https://github.com/ahastudio/CodingLife/blob/main/20220726/react/package.json)

    `"main": "index.js"`를 아래와 같이 변경합니다.

    ```json
    "source": "./index.html",
    ```

    `"scripts"`를 아래와 같이 변경합니다.

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

15. 기본 코드 작성
    - `index.html`
    - `src/main.tsx`
    - `src/App.tsx`
    - `src/App.test.tsx`
    - `src/components/Greeting.test.tsx`
    - `src/components/Greeting.tsx`
