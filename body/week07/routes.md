# 2. routes

## 학습 키워드

- 라우터란?
- React Router
  - Route
  - Browser Router
  - Memory Router

## React Router

```bash
npm i react-router-dom
```

### Route

간단히 코드 옮기기.

```jsx
// App.tsx

import { Routes, Route } from 'react-router-dom';

function App() {
  return (
    <div>
      <Header />
      <main>
        <Routes>
          <Route path="/" element={<HomePage />} />
          <Route path="/about" element={<AboutPage />} />
        </Routes>
      </main>
      <Footer />
    </div>
  );
  }
```

### Browser Router

브라우저 라우터 내려주기.

```jsx
// main.tsx

import { BrowserRouter } from 'react-router-dom';

root.render((
  <BrowserRouter>
    <App />
  </BrowserRouter>
));
```

### Memory Router

#### Type declaration

```tsx
declare function MemoryRouter(
  props: MemoryRouterProps
): React.ReactElement;

interface MemoryRouterProps {
  basename?: string;
  children?: React.ReactNode;
  initialEntries?: InitialEntry[];
  initialIndex?: number;
  future?: FutureConfig;
}
```

`<MemoryRouter>`는 내부적으로 위치를 배열로 저장합니다.\
`<BrowserHistory>`나 `<HashHistory>`와 달리 브라우저의 history 스택과 같은 외부 소스에 묶여 있지 않으므로, 테스트와 같이 history 스택에 완전한 제어가 필요한 시나리오에 적합합니다.

- `initialEntries`: 초기 진입점(엔트리 포인트)을 설정합니다.\
기본값은 ["/"]로 루트(/) URL에 대한 단일 진입점이 설정합니다.

- `initialIndex`: 초기 인덱스를 설정합니다.\
기본값은 initialEntries의 마지막 인덱스입니다.

테스트 코드에선 메모리 라우터 사용.

```jsx
// App.test.tsx

const context = describe;

describe('App', () => {
  function renderApp(path: string) {
    render((
      <MemoryRouter initialEntries={[path]}>
        <App />
      </MemoryRouter>
    ));
  }

  context('when the current path is “/”', () => {
    it('renders the home page', () => {
      renderApp('/');

      screen.getByText(/Hello/);
    });
  });

  context('when the current path is “/about”', () => {
    it('renders the about page', () => {
      renderApp('/about');

      screen.getByText(/This is test/);
    });
  });
});
```

## 참고 자료

- [React Router](https://reactrouter.com/)
- [Routes](https://reactrouter.com/en/main/components/routes)
- [Route](https://reactrouter.com/en/main/route/route)
- [BrowserRouter](https://reactrouter.com/en/main/router-components/browser-router)
- [MemoryRouter](https://reactrouter.com/en/main/router-components/memory-router)
