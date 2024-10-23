# Routing

## 페이지간 이동 (Link)

Next.js에서는 `a`태그 대신 `Link` 컴포넌트를 사용합니다.\
`a`태그를 사용할 경우 경로 간 새로고침이 발생합니다.

```tsx
import Link from 'next/link'

export default function Page() {
  return <Link href="/dashboard">Dashboard</Link>
}
```

## 동적 라우트

![dynamic-routes](./img/dynamic-routes.png)

NextJS에서 dynamic route (동적 라우트)는 대괄호(`[]`)를 사용한 중첩 폴더를 추가해 만들 수 있습니다.\
NextJS에서 지원되는 특수한 문법으로 대괄호 사이에 임의로 식별자를 넣을 수 있습니다.

```tsx
// app/blog/page.js

import Link from "next/link";

export default function BlogPage() {
  return (
    <main>
      <h1>The Blog</h1>
      <p><Link href="/blog/post-1">Post 1</Link></p>
      <p><Link href="/blog/post-2">Post 2</Link></p>
    </main>
  )
}
```

```tsx
// app/blog/[slug]/page.js

export default function BlogPostPage({
  params
}: {
  params: { slug: string }
}) {
// post-1 또는 post-2
  return (
    <main>
      <h1>My Post: {params.slug}</h1>
    </main>
  )
}
```

`<p><Link href="/blog/post-1">Post 1</Link></p>` 링크를 클릭하면 `[slug]`폴더는 `post-1` 이 됩니다.\
`<p><Link href="/blog/post-2">Post 2</Link></p>` 링크를 클릭하면 `[slug]`폴더는 `post-2` 이 됩니다.

Route | Example URL | params
:-: | :-: | :-:
`app/blog/[slug]/page.js` | `/blog/a` | `{ slug: 'a' }`
`app/blog/[slug]/page.js` | `/blog/b` | `{ slug: 'b' }`
`app/blog/[slug]/page.js` | `/blog/c` | `{ slug: 'c' }`

### Catch-all Segments

![catch-all](./img/catch-all.png)

Catch-all 세그먼트는 여러 세그먼트(경로의 일부)를 한 번에 처리하는 기능입니다.\
파일 이름에 대괄호(`[]`)와 줄임표(`...`)를 사용하여 해당 경로의 모든 하위 세그먼트를 캡처합니다.

예를 들어, app/shop/[...slug]/page.js라는 파일이 있으면 /shop 뒤에 오는 경로들이 무엇이든 다 처리할 수 있습니다.

다음과 같은 경로들이 모두 일치합니다.

- `/shop/clothes`

- `/shop/clothes/tops`

- `/shop/clothes/tops/t-shirts`

params는 경로 뒤에 오는 세그먼트들을 배열로 저장합니다. 예시를 보면:

- `/shop/a` → `{ slug: ['a'] }`

- `/shop/a/b` → `{ slug: ['a', 'b'] }`

- `/shop/a/b/c` → `{ slug: ['a', 'b', 'c'] }`

즉, 뒤에 몇 개의 세그먼트가 오든 그 경로를 모두 캡처할 수 있는 기능입니다.

### Optional Catch-all Segments

![optional-catch-all](./img/optional-catch-all.png)

Optional Catch-all 세그먼트는 catch-all과 거의 같지만, 추가로 경로에 세그먼트가 아예 없는 경우도 처리할 수 있습니다.\
이중 대괄호(`[[...]]`)를 사용하여 만듭니다.

예를 들어, `app/shop/[[...slug]]/page.js`라는 파일이 있으면 다음과 같은 경로들에 모두 일치합니다:

- `/shop`

- `/shop/clothes`

- `/shop/clothes/tops`

- `/shop/clothes/tops/t-shirts`

여기서 /shop 경로에도 일치한다는 점이 catch-all 세그먼트와의 차이입니다.\
매개변수가 없어도 경로가 일치한다는 것이 핵심입니다.\
params는 경로에 세그먼트가 없으면 빈 객체로 반환됩니다.

- `/shop` → `{}`

- `/shop/a` → `{ slug: ['a'] }`

- `/shop/a/b` → `{ slug: ['a', 'b'] }`

- `/shop/a/b/c` → `{ slug: ['a', 'b', 'c'] }`

Catch-all은 경로 뒤에 반드시 세그먼트가 필요하지만, optional catch-all은 세그먼트가 없어도 일치할 수 있는 점이 차이입니다.

### 경로 매개 변수

NextJS는 `props` 객체를 모든 페이지 컴포넌트에 넘기며 `params`를 `prop`에서 구조 분해 할당을 통해 뽑아 낼 수 있습니다.

`params` `prop` 안에는 dynamic route (동적 라우트)에 임의로 넣은 모든 이름이 있는 객체가 있습니다.\

```tsx
// app/blog/[slug]/page.js

export default function BlogPostPage({ params }) {
  // `http://localhost:3000/blog/post-1`으로 접근 했을 때
  console.log(params) // { slug: 'post-1' }

  return (
    <main>
      <h1>Blog Post</h1>
    </main>
  )
}
```

## Parallel Routes

![parallel-routes](./img/parallel-routes.png);

하나의 화면 안에 여러 개의 페이지를 병렬로 함께 렌더링 시켜주는 패턴.

Parallel Routes는 Next.js에서 동적인 앱을 구축할 때, 동일한 레이아웃 내에서 여러 페이지를 동시에 또는 조건에 따라 렌더링할 수 있게 해줍니다.\
소셜 미디어 대시보드 같은 곳에서 유용하며, 페이지 내 여러 섹션을 분리하여 동시에 처리할 수 있게 합니다.

예를 들어, 대시보드에 `team`과 `analytics`라는 두 페이지가 있다면, 이 두 페이지를 Parallel Routes를 통해 하나의 레이아웃에서 동시에 렌더링할 수 있습니다.

### Slots

![parallel-routes-file-system](./img/parallel-routes-file-system.png);

Parallel Routes는 slots라는 개념을 사용합니다. Slots는 `@folder` 규칙을 따라 파일 구조 내에서 정의됩니다.\

예를 들어:

```graphql
app/
  ├── layout.js
  ├── @analytics/
  │   └── page.js
  └── @team/
      └── page.js
```

위의 파일 구조에서는 `@team`과 `@analytics`라는 두 개의 slot이 정의됩니다.\
이 slots들은 `layout.js` 파일에서 props로 전달되어 병렬로 렌더링될 수 있습니다.

### 레이아웃에서 Slots 사용 예시

```tsx
export default function Layout({
  children,
  team,
  analytics,
}: {
  children: React.ReactNode
  analytics: React.ReactNode
  team: React.ReactNode
}) {
  return (
    <>
      {children}   {/*메인 콘텐츠 */}
      {team}       {/* 팀 관련 콘텐츠 */}
      {analytics}  {/* 분석 관련 콘텐츠*/}
    </>
  )
}
```

이 구조는 대시보드에서 메인 콘텐츠(`children`)와 두 개의 부가 페이지(team과 analytics)를 동시에 렌더링할 수 있게 해줍니다.

### Slots와 URL

Slots는 URL에 영향을 미치지 않습니다.\
예를 들어, `@analytics` slot의 URL은 `/@analytics/views`가 아닌 `/views`가 됩니다.\
Slots는 내부적으로 분리된 레이아웃이지만, 라우트 세그먼트처럼 URL을 생성하지 않는다는 특징이 있습니다.

#### 알아두면 좋은 점

`children` prop은 기본적으로 모든 레이아웃에 포함된 slot으로, 특별히 정의하지 않아도 됩니다.\
Parallel Routes는 복잡한 앱에서 UI를 효율적으로 관리할 수 있게 도와줍니다.

### Active state and navigation

Parallel Routes에서의 Active State와 Navigation은 사용자가 여러 페이지를 탐색할 때 각 slot에 어떤 콘텐츠가 표시될지를 결정하는 중요한 요소입니다.

#### Soft Navigation vs Hard Navigation

- Soft Navigation: 클라이언트 측 탐색에서 페이지 일부만 업데이트되며, 다른 slot의 활성 상태는 유지됩니다.\
예를 들어, 대시보드의 한 부분만 변경하고 나머지는 그대로 남겨둡니다.

- Hard Navigation: 전체 페이지를 새로고침할 때, Next.js는 현재 URL과 일치하지 않는 slot의 활성 상태를 기억하지 않습니다.\
이 경우, `default.js` 파일을 사용하여 기본 상태를 렌더링하거나 해당하는 파일이 없으면 `404` 에러 페이지가 표시됩니다.

#### default.js

![parallel-routes-unmatched-routes](./img/parallel-routes-unmatched-routes.png)

`default.js`는 일치하지 않는 slot에 기본으로 표시될 내용을 정의하는 파일입니다.\
예를 들어, /settings 페이지에서 `@team`은 관련된 콘텐츠를 보여주지만, `@analytics`에 해당하는 내용이 없으면 `default.js`에서 기본 페이지를 렌더링할 수 있습니다.

#### useSelectedLayoutSegment(s) Hook

이 Hook을 사용하면 특정 slot 내에서 현재 활성화된 페이지 세그먼트를 읽어올 수 있습니다.\
예를 들어, 사용자가 `/login` 페이지에 있을 때, `useSelectedLayoutSegment('auth')`는 `"login"`을 반환합니다.\
이를 통해 현재 어떤 하위 페이지가 활성 상태인지 알 수 있습니다.

```tsx
'use client'

import { useSelectedLayoutSegment } from 'next/navigation'

export default function Layout({
  auth
}: {
    auth: React.ReactNode
}) {
  const loginSegment = useSelectedLayoutSegment('auth')
  // ...
}
```

#### 정리

Parallel Routes는 대시보드나 복잡한 UI에서 여러 페이지를 동시 또는 조건에 따라 렌더링할 수 있게 도와줍니다.\
Soft Navigation과 Hard Navigation의 차이점과 함께 `default.js`와 `useSelectedLayoutSegment`는 페이지 상태를 관리하는 데 유용한 도구입니다.

### Conditional Routes

Parallel Routes를 사용하여 사용자 역할과 같은 특정 조건에 따라 라우트를 조건부로 렌더링할 수 있습니다.\
예를 들어, `/admin` 또는 `/user` 역할에 대해 다른 대시보드 페이지를 렌더링하려면:

![conditional-routes-ui](./img/conditional-routes-ui.png)

```tsx
app/dashboard/layout.tsx
import { checkUserRole } from '@/lib/auth'

export default function Layout({
  user,
  admin,
}: {
  user: React.ReactNode
  admin: React.ReactNode
}) {
  const role = checkUserRole()
  return <>{role === 'admin' ? admin : user}</>
}
```

### Tab Groups

사용자가 slot을 독립적으로 탐색할 수 있도록 slots 내에 `layout`을 추가할 수 있습니다.\
이는 탭을 만드는 데 유용합니다.

예를 들어, `@analytics` slot에는 두 개의 하위 페이지 `/page-views`와 `/visitors`가 있습니다.

![parallel-routes-tab-groups](./img/parallel-routes-tab-groups.png)

`@analytics` 내에 `layout` 파일을 생성하여 두 페이지 간에 탭을 공유하게 합니다:

```tsx
// filename="app/@analytics/layout.tsx" switcher
import Link from 'next/link'

export default function Layout({
  children
}: {
  children: React.ReactNode
}) {
  return (
    <>
      <nav>
        <Link href="/page-views">Page Views</Link>
        <Link href="/visitors">Visitors</Link>
      </nav>
      <div>{children}</div>
    </>
  )
}
```

### Loading and Error UI

Parallel Routes는 독립적으로 스트리밍될 수 있으므로 각 라우트에 대해 독립적인 오류 및 로딩 상태를 정의할 수 있습니다:

![parallel-routes-cinematic-universe](./img/parallel-routes-cinematic-universe.png)

## Intercepting Routes

![one-bite-intercepting](./img/one-bite-intercepting.png)

웹사이트에서 새로운 페이지로 완전히 이동하지 않고, 현재 페이지 위에 다른 페이지의 내용을 겹쳐서 보여줄 수 있는 기능입니다.\
예를 들어, 사진 목록에서 사진을 클릭했을 때, 새 창이 아닌, 현재 페이지 위에 사진을 모달(팝업 창)로 띄워서 보여주는 것입니다.\
이때 URL은 바뀌지만, 페이지는 그대로 유지됩니다.

### Soft Navigation과 Hard Navigation

- Soft Navigation: 페이지 전체가 새로 로드되지 않고, 모달처럼 부드럽게 새로운 내용을 덧붙여 보여줍니다. URL이 바뀌긴 하지만 페이지 전환 없이 자연스럽게 동작합니다.

- Hard Navigation: URL을 직접 입력하거나 페이지를 새로 고침할 때 발생합니다. 이 경우, 모달이 아닌, 해당 페이지로 완전히 이동하게 됩니다.

### 경로 규칙

Intercepting Routes에서는 `( .. )` 같은 규칙을 사용해 경로를 지정합니다. 이 규칙은 부모 디렉토리로 가는 것과 비슷합니다.

- `(.)`: 현재 위치와 같은 경로입니다.

- `( .. )`: 한 단계 위의 경로입니다.

- `( .. )( .. )`: 두 단계 위의 경로입니다.

- `(...)`: 애플리케이션의 최상단, 즉 처음 시작하는 경로를 뜻합니다.

예를 들어, 사진 목록에서 특정 사진을 모달로 띄우고 싶다면, `(..)photo`라는 경로를 사용해서 사진을 모달로 가로챌 수 있습니다.

![intercepted-routes-files](./img/intercepted-routes-files.png)

### 모달 예시

이 기능을 사용하면 페이지 이동 없이 모달을 만들 수 있습니다.\

예를 들어 사용자가 사진을 클릭하면 모달이 열리고, URL은 사진 페이지로 바뀝니다.\
사용자가 페이지를 새로고침해도 모달이 계속 열려 있거나, 이전 페이지로 돌아갔을 때 모달이 닫히는 기능을 쉽게 만들 수 있습니다.\
즉, Intercepting Routes는 사용자가 페이지를 완전히 벗어나지 않고도 새로운 내용을 볼 수 있도록 해주는 기능으로, 특히 모달 창을 띄울 때 유용합니다.
