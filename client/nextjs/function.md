# Function

## usePathName

`usePathname`은 현재 URL의 경로명을 읽을 수 있게 해주는 클라이언트 컴포넌트 훅입니다.

```tsx
// app/example-client-component.tsx
'use client'

import { usePathname } from 'next/navigation'

export default function ExampleClientComponent() {
  const pathname = usePathname()
  return <p>Current pathname: {pathname}</p>
}
```

```js
// app/example-client-component.js
'use client'

import { usePathname } from 'next/navigation'

export default function ExampleClientComponent() {
  const pathname = usePathname()
  return <p>Current pathname: {pathname}</p>
}
```

`usePathname`은 의도적으로 클라이언트 컴포넌트에서 사용해야 합니다.\
클라이언트 컴포넌트는 서버 컴포넌트 아키텍처의 중요한 부분으로, 성능 저하 요소가 아닙니다.

예를 들어, `usePathname`을 사용하는 클라이언트 컴포넌트는 초기 페이지 로드 시 HTML로 렌더링됩니다.\
새로운 경로로 이동할 때, 이 컴포넌트는 다시 가져올 필요가 없습니다.\
대신, 컴포넌트는 한 번 다운로드(클라이언트 자바스크립트 번들)되고 현재 상태에 따라 다시 렌더링됩니다.

### 알아두면 좋은 점

- 서버 컴포넌트에서 현재 URL을 읽는 것은 지원되지 않습니다.\
이는 페이지 탐색 간 레이아웃 상태가 유지되도록 지원하기 위한 의도적인 설계입니다.

- 호환 모드:
  - 대체 경로가 렌더링되거나 Next.js에 의해 자동으로 정적으로 최적화된 pages 디렉토리 페이지일 때 usePathname이 null을 반환할 수 있습니다.
  - 프로젝트에 app 및 pages 디렉토리가 모두 있는 경우 Next.js가 자동으로 타입을 업데이트합니다.

## Parameters

```ts
const pathname = usePathname()
```

`usePathname`은 파라미터를 받지 않습니다.

## Returns

`usePathname`은 현재 URL의 경로명을 나타내는 문자열을 반환합니다.\

예를 들어

URL | 반환 값
:-: | :-:
`/` | `'/'`
`/dashboard` | `'/dashboard'`
`/dashboard?v=2` | `'/dashboard'`
`/blog/hello-world` | `'/blog/hello-world'`

## Examples

경로 변경에 반응하여 작업 수행

```tsx
// app/example-client-component.tsx
'use client'

import { usePathname, useSearchParams } from 'next/navigation'

function ExampleClientComponent() {
  const pathname = usePathname()
  const searchParams = useSearchParams()
  useEffect(() => {
    // 여기에서 작업 수행...
  }, [pathname, searchParams])
}
```

```js
app/example-client-component.js
'use client'

import { usePathname, useSearchParams } from 'next/navigation'

function ExampleClientComponent() {
  const pathname = usePathname()
  const searchParams = useSearchParams()
  useEffect(() => {
    // 여기에서 작업 수행...
  }, [pathname, searchParams])
}
```

## revalidatePath

revalidatePath 함수는 특정 경로에 대해 캐시된 데이터를 필요에 따라 무효화할 수 있게 해줍니다.

### 캐시된 데이터란?

웹사이트를 방문할 때 서버에서 데이터를 받아오면, 이 데이터를 다시 요청할 필요 없이 미리 저장해 두는 것을 "캐싱"이라고 합니다.\
하지만, 이 데이터가 오래되어 업데이트가 필요한 경우, 캐시를 지우고 새 데이터를 불러와야 합니다.\
revalidatePath는 이 캐시를 지워주는 역할을 합니다.

### 어떻게 동작하나요?

경로를 무효화한다는 것은, 그 경로의 캐시된 데이터를 삭제하고, 다음 번에 그 경로를 방문할 때 최신 데이터를 다시 가져오게 한다는 뜻입니다.\
이 함수는 서버와 클라이언트 모두에서 작동하며, 페이지나 레이아웃 등 다양한 부분에서 사용할 수 있습니다.

### 주요 포인트

- 즉시 새로운 데이터를 불러오진 않음: 이 함수는 무효화된 경로를 다음에 방문할 때 새 데이터를 불러옵니다.

- 동적 경로 지원: 동적으로 변화하는 URL(예: `/product/[slug]`)도 처리할 수 있습니다.

- 특정 페이지, 레이아웃 구분 가능: `revalidatePath`는 특정 페이지나 레이아웃만 무효화하는 옵션도 제공합니다.

### revalidatePath Parameters

```ts
revalidatePath(path: string, type?: 'page' | 'layout'): void;
```

`path`: 무효화하려는 데이터와 관련된 파일 시스템 경로(예: `/product/[slug]/page`) 또는 실제 경로 세그먼트(예: `/product/123`)를 나타내는 문자열입니다. 1024자 미만이어야 하며, 대소문자를 구분합니다.
`type`: (선택 사항) 무효화할 경로의 유형을 변경하기 위한 `'page'` 또는 `'layout'` 문자열입니다. `path`에 동적 세그먼트가 포함된 경우(예: `/product/[slug]/page`), 이 매개변수가 필요합니다. 경로가 실제 경로 세그먼트를 참조하는 경우, 예: 동적 페이지(예: `/product/[slug]/page`)의 경우 `/product/1`을 사용하면 `type`을 제공할 필요가 없습니다.

### revalidatePath Returns

`revalidatePath`는 값을 반환하지 않습니다.

### revalidatePath Examples

#### 특정 URL 재검증

```ts
import { revalidatePath } from 'next/cache'
revalidatePath('/blog/post-1')
```

이는 다음 페이지 방문 시 특정 URL을 재검증합니다.

#### page 경로 재검증

```ts
import { revalidatePath } from 'next/cache'
revalidatePath('/blog/[slug]', 'page')
// 또는 경로 그룹과 함께 사용
revalidatePath('/(main)/post/[slug]', 'page')
```

이는 다음 페이지 방문 시 제공된 `page` 파일과 일치하는 모든 URL을 재검증합니다.\
특정 페이지 이하의 페이지는 무효화되지 않습니다.\
예를 들어, `/blog/[slug]`는 `/blog/[slug]/[author]`를 무효화하지 않습니다.

#### layout 경로 재검증

```ts
import { revalidatePath } from 'next/cache'
revalidatePath('/blog/[slug]', 'layout')
// 또는 경로 그룹과 함께 사용
revalidatePath('/(main)/post/[slug]', 'layout')
```

이는 다음 페이지 방문 시 제공된 `layout` 파일과 일치하는 모든 URL을 재검증합니다.\
이는 동일한 레이아웃을 가진 페이지들이 다음 방문 시 재검증되도록 합니다.\
예를 들어, 위의 경우에서 `/blog/[slug]/[another]`도 다음 방문 시 재검증됩니다.

#### 모든 데이터 재검증

```ts
import { revalidatePath } from 'next/cache'

revalidatePath('/', 'layout')
```

이는 클라이언트 측 라우터 캐시를 제거하고, 다음 페이지 방문 시 데이터 캐시를 재검증합니다.

#### 서버 액션

```ts
// app/actions.ts
'use server'

import { revalidatePath } from 'next/cache'

export default async function submit() {
  await submitForm()
  revalidatePath('/')
}
```

#### 라우트 핸들러

```ts
// app/api/revalidate/route.ts
import { revalidatePath } from 'next/cache'
import type { NextRequest } from 'next/server'

export async function GET(request: NextRequest) {
  const path = request.nextUrl.searchParams.get('path')

  if (path) {
    revalidatePath(path)
    return Response.json({ revalidated: true, now: Date.now() })
  }

  return Response.json({
    revalidated: false,
    now: Date.now(),
    message: 'Missing path to revalidate',
  })
}
```

```js
// app/api/revalidate/route.js
import { revalidatePath } from 'next/cache'

export async function GET(request) {
  const path = request.nextUrl.searchParams.get('path')

  if (path) {
    revalidatePath(path)
    return Response.json({ revalidated: true, now: Date.now() })
  }

  return Response.json({
    revalidated: false,
    now: Date.now(),
    message: 'Missing path to revalidate',
  })
}
```

## useRouter

Next.js에서 클라이언트 컴포넌트 내에서 프로그래밍 방식으로 라우트를 변경할 수 있도록 도와줍니다.\
하지만 대부분의 네비게이션 상황에서는 `<Link>` 컴포넌트를 사용하는 것이 권장됩니다.\ `useRouter`는 특별한 요구 사항이 있을 때 유용합니다.

### 기본 예시

```tsx
'use client'

import { useRouter } from 'next/navigation'

export default function Page() {
  const router = useRouter()

  return (
    <button type="button" onClick={() => router.push('/dashboard')}>
      대시보드로 이동
    </button>
  )
}
```

### useRouter의 주요 메서드

- `router.push(href: string, { scroll: boolean })`:\
새로운 라우트로 이동하며 브라우저의 히스토리 스택에 새 항목을 추가합니다.\
`scroll` 옵션을 `false`로 설정하면 페이지 상단으로 스크롤되는 것을 방지할 수 있습니다.

- `router.replace(href: string, { scroll: boolean })`:\
`push()`와 유사하지만, 히스토리 항목을 새로 추가하지 않고 현재 항목을 대체합니다.

- `router.refresh()`:\
현재 페이지를 새로고침하여 서버에서 최신 데이터를 다시 요청합니다.\
서버 컴포넌트를 업데이트하면서 클라이언트 측 상태(useState)나 브라우저 상태(예: 스크롤 위치)는 유지됩니다.

- `router.prefetch(href: string)`:\
빠른 클라이언트 전환을 위해 특정 경로를 미리 로드합니다.\
이는 페이지 전환 성능을 향상시키기 위해 사용됩니다.

- `router.back()`:\
브라우저의 히스토리 스택에서 이전 페이지로 이동합니다.

- `router.forward()`:\
히스토리 스택에서 다음 페이지로 이동합니다.

#### useRouter 주의 사항

`<Link>` 컴포넌트는 자동으로 경로를 뷰포트에 보이게 될 때 미리 로드합니다.\
캐싱된 요청이 있을 경우 refresh()는 동일한 결과를 다시 가져올 수 있습니다.\
또한 동적 API(예: 쿠키, 헤더)로 인해 응답이 달라질 수 있습니다.\
`next/router`에서 `next/navigation`으로의 마이그레이션 `App Router`를 사용하는 경우, `next/router` 대신 `next/navigation`을 사용해야 합니다.\
마이그레이션 시 알아야 할 주요 변경 사항은 다음과 같습니다:

1. `useRouter`는 `next/router`가 아닌 `next/navigation`에서 가져와야 합니다.

2. `pathname` 문자열은 더 이상 사용되지 않으며, 대신 `usePathname()`을 사용해야 합니다.

3. `query` 객체는 사라졌고, `useSearchParams()`로 대체되었습니다.

4. `router.events`는 다른 방식으로 대체되었습니다.

### 라우트 변경 감지

클라이언트 컴포넌트 내에서 URL 변경을 감지하려면 `usePathname()`과 `useSearchParams()` 훅을 사용하여 라우트 변경을 처리할 수 있습니다.

```jsx
'use client'

import { useEffect } from 'react'
import { usePathname, useSearchParams } from 'next/navigation'

export function NavigationEvents() {
  const pathname = usePathname()
  const searchParams = useSearchParams()

  useEffect(() => {
    const url = `${pathname}?${searchParams}`
    console.log(url)
    // 현재 URL을 처리할 수 있습니다
  }, [pathname, searchParams])

  return '...'
}
```

### 스크롤 상단으로 이동 방지

Next.js는 기본적으로 새로운 경로로 이동할 때 페이지 상단으로 자동 스크롤합니다.\
이 동작을 비활성화하려면 `router.push()`나 `router.replace()`에 `scroll: false` 옵션을 전달하면 됩니다.

```tsx
'use client'

import { useRouter } from 'next/navigation'

export default function Page() {
  const router = useRouter()

  return (
    <button
      type="button"
      onClick={() => router.push('/dashboard', { scroll: false })}
    >
      대시보드
    </button>
  )
}
```

#### useRouter 참고 사항

- `Suspense`: `useSearchParams()`를 사용할 때는 클라이언트 렌더링 중 가장 가까운 `Suspense` 경계까지 감싸줘야 합니다.\
이는 정적 렌더링 시 클라이언트 사이드 렌더링이 발생하기 때문입니다.
