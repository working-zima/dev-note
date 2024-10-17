# Rendering

Next.js는 리액트 서버 컴포넌트 그리고 클라이언트 컴포넌트가 있습니다.

creat React app이나 Vite의 도움을 받아 만드는 모든 바닐라 리액트 앱들에서 기본적으로 클라이언트 컴포넌트를 사용하고 있습니다.\
React.js는 순수한 클라이언트 사이드 라이브러리로 브라우저에서 클라이언트 측에서 코드를 실행합니다.

Next.js는 풀스택 프레임워크이기 때문에 프론트엔드 뿐 아니라 백엔드도 있습니다.\
기본적으로 Next.js에서 모든 컴포넌트는 서버 컴포넌트입니다.\
따라서 Next.js와 작업할 때 코드는 백엔드에서도 실행됩니다.

## 리액트 서버 컴포넌트

React Server Components는 UI를 서버에서 렌더링하고, 캐시를 통해 성능을 최적화하는 기능입니다.\
모든 리액트 컴포넌트들은 그것들이 페이지인지, 레이아웃인지, 기본 컴포넌트인지에 상관없이 브라우저가 아닌 오직 서버에서만 렌더링됩니다.\
이것을 리액트 서버 컴포넌트라 부릅니다.

예시로 `console.log()`를 사용해도 브라우저 개발자 툴에서 실행 로그를 확인할 수 없으며 터미널에서만 확인할 수 있습니다.

서버 렌더링에는 정적 렌더링(Static), 동적 렌더링(Dynamic), 스트리밍(Streaming)이라는 세 가지 방식이 있습니다.

### 리액트 서버 컴포넌트 사용 이유

- 데이터 페칭: 서버에서 데이터를 가져와 클라이언트 요청을 줄여 성능을 개선합니다.

- 보안: 토큰이나 API 키와 같은 민감한 데이터와 로직을 서버에만 두고, 클라이언트에 노출시키지 않아 보안을 강화합니다.

- 캐싱: 렌더링된 결과를 저장해, 이후 같은 데이터를 재사용해 성능을 높입니다.

- 성능: 다운로드해야 하는 클라이언트 측의 자바스크립트 코드가 줄어들 수 있어 웹사이트의 성능을 향상시킬 수 있습니다.

- SEO: 웹 검색 크롤러들은 완성 컨텐츠를 포함하는 페이지들을 볼 수 있기 때문에 검색엔진 최적화에도 좋습니다.

- 스트리밍: 렌더링 작업을 청크로 분할하고 준비되는 대로 클라이언트에 스트리밍하여 페이지의 일부가 준비되면 바로 보여주고, 나머지는 준비되는 대로 표시해 사용자 경험을 향상시킵니다.

### 특징

- `Event Listener` 사용 불가
- `React Hooks`사용 불가
- 컴포넌트를 `async` 함수로 정의 가능

### Static Rendering (기본 설정)

Static Rendering은 빌드 타임에 미리 경로를 렌더링하는 방식입니다. 즉, 페이지가 사용자에게 요청되기 전에 이미 서버에서 HTML 파일을 만들어둡니다. 이 방식은 주로 변경이 거의 없고 사용자마다 동일한 콘텐츠를 보여줄 때 적합합니다. 예를 들어, 블로그 게시물이나 제품 페이지처럼 자주 변하지 않는 페이지가 이에 해당합니다.

Next.js는 Static Rendering된 결과를 캐시하고, 이를 CDN(Content Delivery Network)에 저장하여 사용자와 서버 간의 응답 속도를 높일 수 있습니다.

### Dynamic Rendering

Dynamic Rendering은 요청 시 경로를 각 사용자마다 새로 렌더링하는 방식입니다.\
사용자가 요청할 때마다 서버에서 실시간으로 데이터를 가져오고 페이지를 렌더링합니다.\
주로 사용자에 따라 다른 데이터를 보여줘야 할 때나, 요청 시점에만 알 수 있는 정보가 있을 때 사용합니다.\
예를 들어, 사용자별 맞춤화된 대시보드나 검색 결과 페이지가 이에 해당됩니다.

Next.js는 동적으로 렌더링할 때에도, 일부 데이터는 캐시하고 일부는 실시간으로 가져오는 방식으로 혼합된 처리가 가능합니다.\
이는 성능을 최적화하면서도 동적 콘텐츠를 지원합니다.

#### Dynamic Functions

Next.js에서 동적 함수를 사용하면 페이지가 항상 요청 시 렌더링됩니다.\
동적 함수는 쿠키나 요청 헤더, 검색 매개변수 같은 요청에 따라 달라지는 데이터를 처리할 때 유용합니다.

동적 API는 다음과 같으며, 이 함수들 중 하나를 사용하면 전체 경로가 요청 시 동적으로 렌더링됩니다.

- `cookies()`

- `headers()`

- `unstable_noStore()`

- `unstable_after()`

- `searchParams` prop

### Streaming

![sequential-parallel-data-fetching](./img/sequential-parallel-data-fetching.png)

![server-rendering-with-streaming](./img/server-rendering-with-streaming.png)

Streaming은 서버에서 콘텐츠를 청크 단위로 나누어 순차적으로 렌더링하고, 준비되는 대로 클라이언트에 스트리밍하는 방식입니다.\
이를 통해 사용자는 페이지의 일부를 빠르게 볼 수 있고, 전체 콘텐츠가 렌더링될 때까지 기다릴 필요가 없습니다.

Streaming은 초기 페이지 로드 성능을 크게 개선할 수 있는 기술로, 특히 느린 데이터 소스를 처리해야 할 때 유용합니다.\
예를 들어, 제품 페이지에서 리뷰 데이터를 가져오는 경우, 리뷰가 모두 로드되기 전에 페이지의 다른 콘텐츠를 먼저 보여줄 수 있습니다.

Next.js는 기본적으로 Streaming을 지원하며, `loading.js` 파일과 React `Suspense`를 사용 통해 특정 UI 컴포넌트를 로딩 중임을 표시하면서도 스트리밍을 적용할 수 있습니다.

이렇게 각 렌더링 방식은 사용자의 요구와 페이지 특성에 맞게 선택될 수 있으며, 성능과 사용자 경험을 동시에 고려한 전략입니다.

## 클라이언트 컴포넌트

어떤 사용자 상호작용을 기다리고 있는 부분은 클라이언트에서 실행되는 코드가 필요하므로 클라이언트 컴포넌트여야 합니다.\
상호작용에 사용하는 eventHandler, 리액트 훅들은 서버 측에서는 사용 불가하기 때문에 클라이언트 컴포넌트를 사용해야합니다.

### 클라이언트 컴포넌트 사용 이유

- 상호작용: Client Components는 상태 관리, 이벤트 처리, 효과 적용이 가능하여 사용자와의 실시간 상호작용을 지원합니다.

- 브라우저 API 접근: 브라우저에 있는 API, 예를 들어 `Geolocation` API, `localStorage` 등을 사용할 수 있습니다.

### 클라이언트 컴포넌트 사용 방법

클라이언트 컴포넌트를 만들고 싶다면 Next.js에게 컴포넌트를 잡고 있는 파일 위에 특별한 지시어를 사용하여 드러나게 알려야 합니다.\
파일 상단에 `"use client"` 지시어를 import 위에 추가하면 됩니다.

이 지시어를 선언하면 해당 파일과 그 안의 모든 자식 컴포넌트는 클라이언트 측에서 실행됩니다.\
이를 통해 컴포넌트가 클라이언트 번들에 포함되어 인터랙티브한 기능을 제공합니다.

```jsx
'use client'
import { useState } from 'react'

export default function Counter() {
  const [count, setCount] = useState(0)

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  )
}
```

### Hydration

`'use client'`는 client에서만 렌더한다는 의미가 아니라 client에서도 렌더를 한다는 의미입니다.
`'use client'`를 사용해도 일단 서버에서 렌더링(브라우저가 이해할 수 있는 HTML로 변환)됩니다.

사용자가 요청을 보내면 Next.js는 요청에 맞는 components를 HTML로 변환하여 응답합니다.\
HTML이 사용자에게 전달되면 React를 로드하고 React에 HTML을 초기화 합니다.(`init(HTML)`)\
그럼 HTML은 interactive한 React component가 됩니다.

Next.js에서 렌더링한 HTML은 React application으로 초기화되기 전까지는 하드 코딩된 HTML과 같으며 서버 컴포넌트를 사용한 경우가 이에 해당됩니다.\
`'use client'`는 HTML을 interactive한 React component로 만들어 달라는 표시이며 클라이언트 컴포넌트가 이에 해당합니다.

## 언제 어떤 컴포넌트를 사용해야 할까?

| | Server Component | Client Component |
| - | - | - |
| 데이터 페칭 | O | X |
| 백엔드 리소스에 직접 접근 | O | X |
| 민감한 정보를 서버에 보관 (액세스 토큰, API 키 등) | O | X |
| 대형 종속성을 서버에 유지 / 클라이언트 측 JavaScript 줄이기 | O | X |
| 인터랙티비티 및 이벤트 리스너 추가 (`onClick()`, `onChange()` 등) | X | O |
| 상태 및 생명주기 효과 사용 (`useState()`, `useReducer()`, `useEffect()` 등) | X | O |
| 브라우저 전용 API 사용 | X | O |
| 상태, 효과 또는 브라우저 전용 API에 의존하는 커스텀 훅 사용 | X | O |
| [React 클래스 컴포넌트](https://react.dev/reference/react/Component) 사용 | X | O |

## 서버 컴포넌트와 클라이언트 컴포넌트 혼합하기

서버와 클라이언트 컴포넌트를 함께 사용할 때, UI 구조를 컴포넌트 트리로 시각화하는 것이 도움이 될 수 있습니다.\
트리는 루트 레이아웃(기본적으로 서버 컴포넌트)부터 시작하며, 이 트리 내에서 클라이언트 컴포넌트는 `"use client"` 지시어를 통해 명확히 정의된 부분에서만 동작하게 됩니다.

### 중요한 개념

- 클라이언트 컴포넌트 모듈 내에서는 서버 컴포넌트를 직접 `import`할 수 없습니다.\
클라이언트 컴포넌트는 서버 컴포넌트 이후에 렌더링되기 때문에 서버 컴포넌트를 가져오려면 새로운 요청이 필요합니다.

- 서버와 클라이언트 사이에서 코드를 왔다 갔다 하지 않고, 클라이언트가 서버의 데이터에 접근해야 할 때마다 새로운 요청을 서버로 보냅니다.

- 새로운 요청이 있을 때 모든 서버 컴포넌트가 먼저 렌더링됩니다.\
그 후, 클라이언트에서 React는 서버에서 받은 데이터(RSC Payload)를 사용해 클라이언트와 서버 컴포넌트를 통합하여 처리합니다.

### 불가능한 패턴: 서버 컴포넌트를 클라이언트 컴포넌트에 직접 import

서버 컴포넌트를 클라이언트 컴포넌트 안에 직접 import하는 것은 불가능합니다. 아래는 잘못된 예시입니다:

```tsx
'use client'
import ServerComponent from './Server-Component'

export default function ClientComponent() {
  const [count, setCount] = useState(0)
  return (
    <>
      <button onClick={() => setCount(count + 1)}>{count}</button>
      <ServerComponent />  // 서버 컴포넌트는 직접 import할 수 없습니다.
    </>
  )
}
```

### 가능한 패턴: 서버 컴포넌트를 props로 전달

서버 컴포넌트를 클라이언트 컴포넌트로 전달할 때는 props, 특히 children을 사용해 클라이언트 컴포넌트에 서버 컴포넌트를 넘길 수 있습니다. 아래는 지원되는 패턴의 예시입니다:

```tsx
'use client'
import { useState } from 'react'

export default function ClientComponent({ children }) {
  const [count, setCount] = useState(0)
  return (
    <>
      <button onClick={() => setCount(count + 1)}>{count}</button>
      {children}  // 서버 컴포넌트가 이 위치에 렌더링됩니다.
    </>
  )
}
```

부모 컴포넌트에서 서버 컴포넌트를 클라이언트 컴포넌트의 자식으로 전달할 수 있습니다:

```tsx
import ClientComponent from './client-component'
import ServerComponent from './server-component'

export default function Page() {
  return (
    <ClientComponent>
      <ServerComponent />  // 서버 컴포넌트를 자식으로 전달합니다.
    </ClientComponent>
  )
}
```

이렇게 하면 클라이언트 컴포넌트와 서버 컴포넌트가 서로 독립적으로 렌더링될 수 있습니다.

## 자료

[NEXT.js](https://nextjs.org/learn/dashboard-app)
