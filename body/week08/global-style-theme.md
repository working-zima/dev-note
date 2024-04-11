# 6. Global Style & Theme

## 학습 키워드

- Reset CSS
- box-sizing 속성
- word-break 속성
- Theme
- ThemeProvider

## Reset CSS

브라우저마다 스타일이 적용되는 부분이 달랐기 때문에 그런 부분을 초기화 해주는 코드를 Reset CSS라고 합니다.\
최근에 추가해야 하는 부분도 있겠지만 가장 유명한 Reset CSS은 아래와 같습니다.

```css
html, body, div, span, applet, object, iframe,
h1, h2, h3, h4, h5, h6, p, blockquote, pre,
a, abbr, acronym, address, big, cite, code,
del, dfn, em, img, ins, kbd, q, s, samp,
small, strike, strong, sub, sup, tt, var,
b, u, i, center,
dl, dt, dd, ol, ul, li,
fieldset, form, label, legend,
table, caption, tbody, tfoot, thead, tr, th, td,
article, aside, canvas, details, embed,
figure, figcaption, footer, header, hgroup,
menu, nav, output, ruby, section, summary,
time, mark, audio, video {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
/* HTML5 display-role reset for older browsers */
article, aside, details, figcaption, figure,
footer, header, hgroup, menu, nav, section {
  display: block;
}
body {
  line-height: 1;
}
ol, ul {
  list-style: none;
}
blockquote, q {
  quotes: none;
}
blockquote:before, blockquote:after,
q:before, q:after {
  content: '';
  content: none;
}
table {
  border-collapse: collapse;
  border-spacing: 0;
}
```

## styled-reset

Reset CSS를 styled-components 라이브러리 도구에서 사용할 수 있도록 해놓은 라이브러리입니다.

### 패키지 설치

```bash
npm i styled-reset
```

### App 컴포넌트에서 styled-reset 사용

```tsx
import { Reset } from 'styled-reset';

export default function App() {
  return (
    <>
      <Reset />
      <Greeting />
    </>
  );
}
```

## Global Style

스타일에 관련된 편의를 위한 세팅을 전역 스타일로 지정합니다.

```tsx
// src/styles/GlobalStyle.ts

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

  // 1rem = 10px
  html {
    font-size: 62.5%;
  }

  body {
    font-size: 1.6rem;
  }

  :lang(ko) {
    h1, h2, h3 {
      word-break: keep-all;
    }
  }
`;

export default GlobalStyle;
```

### box-sizing 속성

전역 스타일을 지정할 때 `box-sizing`을 `border-box`로 하여 `border`를 가로, 세로 크기에 포함하도록 바꿉니다.

### font-size 속성

디자인 단계에서 사용하는 `px`과 다르게 `rem`은 최상위 요소에 지정된 `font size`의 값을 기준으로 변하기 때문에 `1px`과 `1rem`은 크기의 차이가 있을 수 있습니다.\
단위의 차이는 개발하는데 불편함을 가져올 수 있기 때문에 설정을 통해 통일해주는 방법이 있습니다.

일반적으로 브라우저의 `1rem`은 `16px`이기 때문에 `font-size`를 `62.5%`로 지정하여 `16px -> 10px`이 되도록 지정합니다.\
`1rem`이 `10px`이 되었기 때문에 `rem`으로 다시 브라우저의 기본크기인 `16px`을 표현하려면 `1.6rem`이 되어야 합니다.\
따라서 `body`의 `font-size`를 `1.6rem`으로 지정해 줍니다.

### word-break 속성

한국어는 띄어쓰기가 발달되어, 잘못된 띄어쓰기로 인해 기괴해 보이는 경우가 많습니다.\
이를 막기위해 `word-break`를 `keep-all`로 하여 한글 텍스트에서는 줄을 바꿀 때 단어를 끊지 않게 합니다.

### App 컴포넌트에서 GlobalStyle 사용

해당 속성들은 Global Style로 가져와야 합니다.

```tsx
import { Reset } from 'styled-reset';

import GlobalStyle from './styles/GlobalStyle';

export default function App() {
  return (
    <>
      <Reset />
      <GlobalStyle />
      <Greeting />
    </>
  );
}
```

## Theme

`ThemeProvider`로 하위 컴포넌트에게 `theme prop`을 내려줍니다.\
컴포넌트에 내려진 `theme prop`을 통해 일관된 디자인 시스템의 근간을 마련하는데 활용할 수 있습니다.\
잘 정의하면 다크 모드 등에 대응하기 쉽습니다.\
눈에 보이는 단편적인 정보를 넘어서, “의미”에 집중할 수 있게 됩니다.

### Theme 정의

일단 기본 `Theme`부터 정의합니다.

```tsx
// src/styles/defaultTheme.ts

const defaultTheme = {
  colors: {
    background: '#FFF',
    text: '#000',
    primary: '#F00',
    secondary: '#00F',
  },
};

export default defaultTheme;
```

### App 컴포넌트에서 ThemeProvider 사용

마찬가지로 App 컴포넌트에서 사용.

```tsx
import { ThemeProvider } from 'styled-components';

import { Reset } from 'styled-reset';

import defaultTheme from './styles/defaultTheme';

import GlobalStyle from './styles/GlobalStyle';

export default function App() {
  return (
    <ThemeProvider theme={defaultTheme}>
      <Reset />
      <GlobalStyle />
      <Greeting />
    </ThemeProvider>
  );
}
```

이제 “props.theme”을 쓸 수 있습니다.

```tsx
// src/styles/GlobalStyle.ts

import { createGlobalStyle } from 'styled-components';

const GlobalStyle = createGlobalStyle`
  body {
    background: ${(props) => props.theme.colors.background};
    color: ${(props) => props.theme.colors.text};
  }

  a {
    color: ${(props) => props.theme.colors.text};
  }

  button,
  input,
  select,
  textarea {
    background: ${(props) => props.theme.colors.background};
    color: ${(props) => props.theme.colors.text};
  }
`;

export default GlobalStyle;
```

하지만 타입 문제가 발생합니다.

### styled.d.ts

타입 문제를 해결하기 위해 styled.d.ts 파일을 작성합니다.

```tsx
// src/styles/styled.d.ts

import 'styled-components';

declare module 'styled-components' {
  export interface DefaultTheme extends Theme {
    colors: {
      background: string;
      text: string;
      primary: string;
      secondary: string;
    }
  }
}
```

타입을 정의하고 `defaultTheme`을 맞추는 게 불편하니, 반대로 `defaultTheme`에서 타입을 추출하는 방법도 있습니다.

```tsx
// src/styles/Theme.ts

import defaultTheme from './defaultTheme';

type Theme = typeof defaultTheme;

export default Theme;
```

`defaultTheme`의 타입을 가져와서 타입 파일을 변경합니다.

```tsx
// src/styles/styled.d.ts

import 'styled-components';

import Theme from './Theme';

declare module 'styled-components' {
  export interface DefaultTheme extends Theme {}
}
```

다른 theme을 추가할 때 Theme 타입을 사용합니다.\
항상 `defaultTheme`에 먼저 항목을 추가/삭제하고, 나머지를 여기에 맞추면 됩니다.

```tsx
import Theme from './Theme';

const darkTheme: Theme = {
  colors: {
    background: '#000',
    text: '#FFF',
    primary: '#F00',
    secondary: '#00F',
  },
};

export default darkTheme;
```

### DarkMode 예시

의미를 명확히 드러냈다면, 다크 모드를 지원하는 것도 쉽습니다.

```tsx
import { useDarkMode } from 'usehooks-ts';

import { ThemeProvider } from 'styled-components';

import { Reset } from 'styled-reset';

import defaultTheme from './styles/defaultTheme';
import darkTheme from './styles/darkTheme';

import GlobalStyle from './styles/GlobalStyle';

import Greeting from './components/Greeting';
import Button from './components/Button';

export default function App() {
  const { isDarkMode, toggle } = useDarkMode();

  const theme = isDarkMode ? darkTheme : defaultTheme;

  return (
    <ThemeProvider theme={theme}>
      <Reset />
      <GlobalStyle />
      <Greeting />
      <Button onClick={toggle}>
        Dark Theme Toggle
      </Button>
    </ThemeProvider>
  );
}
```

### window.matchMedia 문제

Jest 테스트 쪽에서 “window.matchMedia” 문제 발생합니다.

Jest와 JSDOM을 사용하여 테스트할 때 `window.matchMedia()` 메서드 같은 일부 브라우저 API는 JSDOM에서 아직 구현되지 않은 경우가 있습니다.

`src/setupTests.ts` 파일에 [공식 문서](https://jestjs.io/docs/29.4/manual-mocks#mocking-methods-which-are-not-implemented-in-jsdom)에 나온 코드를 넣으면 해결됩니다.

`Object.defineProperty()`를 사용하여 `window.matchMedia`를 목(mock)으로 설정합니다.\
이 함수는 전달된 쿼리에 따라 적절한 객체를 반환하며, `matches` 속성은 항상 `false`로 설정됩니다.\

미디어 쿼리에 따라 동적으로 동작하는 코드를 테스트할 때, 특히 특정 미디어 쿼리에 따라 다른 UI나 동작을 실행하는 경우에는 테스트가 어려울 수 있습니다.\
예를 들어, 화면이 특정 너비 미만인 경우에는 다른 UI를 보여주고, 특정 너비 이상인 경우에는 다른 UI를 보여주는 등의 경우입니다.

이런 상황에서 `matches` 속성을 항상 `false`로 설정하여 해당 미디어 쿼리에 대한 테스트 목적으로 동작하는 것은 미디어 쿼리의 영향을 받지 않고 코드를 테스트하는 데 더욱 용이하게 만들어줍니다.\
미디어 쿼리에 따라 다른 동작을 테스트하기 위해 실제로 브라우저 창 크기를 조정하거나 다른 환경을 설정할 필요가 없으므로 테스트가 훨씬 간편해지며 테스트할 때 일관된 결과를 얻을 수 있습니다.

이외에도 `window.matchMedia`와 `matches` 속성 대한 설명은 [window.matchMedia() 메서드](../../appendix/window-matchMedia.md)에 있습니다.

```tsx
// src/setupTests.ts

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

`jest.config`의 `setupFilesAfterEnv`에 윗 코드를 실행할 `setupTests`가 추가 되었는지 확인합니다.

```javascript
// jest.config

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

## 참고 자료

- [Reset Css](https://meyerweb.com/eric/tools/css/reset/)
- [styled-reset](https://github.com/zacanger/styled-reset)
- [createGlobalStyle](https://styled-components.com/docs/api#createglobalstyle)
- [CSS box model](https://developer.mozilla.org/ko/docs/Learn/CSS/Building_blocks/The_box_model#%EB%8C%80%EC%B2%B4_css_box_model)
- [box-sizing](https://developer.mozilla.org/ko/docs/Web/CSS/box-sizing)
- [The 62.5% Font Size Trick](https://www.aleksandrhovhannisyan.com/blog/62-5-percent-font-size-trick/)
- [keep-all-villain](https://megaptera.notion.site/keep-all-villain-923a4b6f23eb45b99aef4d8bc0865f9c)
- [word-break](https://developer.mozilla.org/ko/docs/Web/CSS/word-break)
- [Theming](https://styled-components.com/docs/advanced#theming)
- [Create a declarations file](https://styled-components.com/docs/api#create-a-declarations-file)
- [VSCode Theme Color](https://code.visualstudio.com/api/references/theme-color)
- [Bootstrap Theme Color](https://getbootstrap.com/docs/5.3/customize/color/)
- [Mocking methods which are not implemented in JSDOM](https://jestjs.io/docs/29.4/manual-mocks#mocking-methods-which-are-not-implemented-in-jsdom)
