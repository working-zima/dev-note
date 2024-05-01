# 3. router

## 학습 키워드

ReactRouter - RouterProvider

### Router

라우팅을 관리하는 주요 컴포넌트입니다.\
Router는 React 애플리케이션 내에서 라우팅을 처리하는 주체입니다.\
라우터는 URL의 변화를 감지하고, 해당 URL에 맞는 컴포넌트를 렌더링합니다.\
React Router에서 기본적으로 제공하는 라우터 종류로는 `<BrowserRouter>`, `<HashRouter>`, `<MemoryRouter>`, 버전 6.4부터 지원하는 `<Router>` 등이 있습니다.

#### Outlet

`<Outlet>` 컴포넌트는 자녀 라우트 요소들이 렌더링되어야 할 장소를 표시합니다.

#### Layout & routes 예시

React Router 버전 6.4부터 지원하는, 라우터 객체를 만들어서 쓰는 방법입니다.\
라우팅 정보만 별도의 파일로 관리합니다.

```tsx
// components/Layout.tsx

import { Outlet } from 'react-router-dom';

function Layout() {
  return (
    <div>
      <Header />
      <Outlet />
      <Footer />
    </div>
  );
}
```

```tsx
// routes.tsx

const routes = [
  {
    element: <Layout />,
    children: [
      { path: '/', element: <HomePage /> },
      { path: '/about', element: <AboutPage /> },
    ],
  },
];

export default routes;
```

#### createBrowserRouter

DOM History API를 사용하여 URL을 업데이트하고 history 스택을 관리합니다.

```tsx
const router = createBrowserRouter(routes, {opts})
```

- `routes`\
: 라우트 오브젝트들의 배열로, `children` 속성에 중첩된 라우트를 가질 수 있습니다.

- `opts`
  - `basename`\
  : 앱의 basename으로, 도메인의 루트에 배포할 수 없는 상황에서 하위 디렉터리에 앱을 배포할 때 유용합니다.

  - `future`\
  : 이 라우터에 대해 활성화할 Future Flags의 선택적 집합입니다.\
  v7로의 이전을 용이하게 하기 위해 새로운 Future Flags에 빨리 적응하는 것을 권장합니다.

  - `hydrationData`\
  : 서버 렌더링 시 자동 하이드레이션을 비활성화하고, 서버 렌더링에서 생성된 하이드레이션 데이터를 전달할 때 사용됩니다.\
  또한, 특정 라우트에 대한 추가 정보와 선택적 데이터를 제공하기 위해 을 사용할 수 있습니다.

  - `window`\
  : 브라우저 개발 도구 플러그인이나 테스트와 같은 환경에서 전역 window와 다른 창을 사용할 때 유용합니다.

#### RouterProvider

모든 데이터 라우터 객체를 이 컴포넌트에 전달하여 앱을 렌더링하고 나머지 데이터 API를 활성화합니다.

```tsx
<RouterProvider
  router={router}
  fallbackElement={}
  future={}
/>
```

- `fallbackElement`\
: 앱을 서버 렌더링하지 않을 경우, createBrowserRouter는 마운트될 때 모든 일치하는 라우트 로더를 시작합니다.\
이 시간 동안 사용자에게 앱이 작동 중임을 알리기 위해 fallbackElement를 제공할 수 있습니다.\
정적 호스팅 시 Time To First Byte(TTFB)를 고려하세요.

`future`
: 이 라우터에 대해 활성화할 Future Flags의 선택적 집합입니다.\
새로운 Future Flags에 빨리 적응하는 것을 권장하여 나중에 v7로의 이전을 용이하게 합니다.

#### router 예시

App 컴포넌트를 거치지 않고 바로 브라우저 라우터 만들어서 사용합니다.

```tsx
// main.tsx

import { createBrowserRouter, RouterProvider } from 'react-router-dom';

import routes from './routes';

const router = createBrowserRouter(routes);

root.render((
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>
));
```

또는 App 컴포넌트를 살리는 방법으로도 할 수 있습니다.

```tsx
// App.tsx

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

#### createMemoryRouter

```tsx
createMemoryRouter(routes, {opts})
```

브라우저의 history 대신에 메모리 내에서 자체적으로 history 스택을 관리합니다.\
따라서 브라우저가 아닌 환경에서도 React Router를 실행하는 데 사용할 수 있습니다.

- `routes`\
: 라우트 오브젝트들의 배열로, 각각의 라우트는 경로와 해당 경로에 대한 컴포넌트, 그리고 필요에 따라 로더(loader)를 포함합니다.

- `opts`\
: 선택적 매개변수로, 다양한 설정을 제어합니다.
  - `basename`\
  : 앱의 basename으로, URL 경로를 완전히 설정하기 위한 옵션입니다.

  - `future`\
  : Future Flags를 설정하는데 사용되며, 나중에 v7로의 이전을 용이하게 합니다.

  - `hydrationData`\
  : 서버 렌더링 시 사용되며, 자동 하이드레이션을 비활성화하고 서버 렌더링에서 생성된 하이드레이션 데이터를 전달합니다.

  - `initialEntries`\
  : history 스택의 초기 엔트리를 설정합니다.\
  테스트나 앱을 여러 위치로 시작하거나 (뒤로 이동 테스트 등을 위해) 사용됩니다.

  - `initialIndex`\
  : 렌더링할 history 스택의 초기 인덱스를 설정합니다.\
  특정 엔트리에서 테스트를 시작할 때 사용됩니다.\
  이 값은 초기 엔트리 중 마지막 엔트리로 기본 설정됩니다.

#### test 예시

메모리 라우터 만들어서 테스트.

```tsx
// routes.test.tsx

describe('routes', () => {
  function renderRouter(path: string) {
    const router = createMemoryRouter(routes, { initialEntries: [path] });
    render(<RouterProvider router={router} />);
  }

  context('when the current path is “/”', () => {
    it('renders the home page', () => {
      renderRouter('/');

      screen.getByText(/Hello/);
    });
  });

  context('when the current path is “/about”', () => {
    it('renders the about page', () => {
      renderRouter('/about');

      screen.getByText(/About/);
    });
  });
});
```

## 참고 자료

- [createBrowserRouter](https://reactrouter.com/en/main/routers/create-browser-router)
- [RouterProvider](https://reactrouter.com/en/main/routers/router-provider)
- [createMemoryRouter](https://reactrouter.com/en/main/routers/create-memory-router)
