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

## 페이지 및 레이아웃

`page.js` 파일이 페이지의 내용을 정의한다면, `layout.js` 파일은 하나 또는 그 이상의 페이지를 감싸는 껍데기를 정의합니다.\
레이아웃이 wrapper이고 페이지가 실제 내용이라고 생각하면 쉽습니다.

![page example](./img/page-example.png)

### layout

모든 Next 프로젝트에는 최소 하나의 Root `layout.js` 파일이 필요합니다.\
또한 중첩된 `layout.js` 파일도 있을 수 있습니다.

```tsx
// app/layout.js

export const metadata = {
  title: 'NextJS Course App',
  description: 'Your first NextJS app!',
};

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

#### metadata

`head`에 들어가는 모든 내용은 `metadata`에 의해 설정되거나 NextJS로 인해 이면에서 자동으로 설정됩니다.

#### children

현재 활성화된 페이지의 내용입니다.\
`children`을 통해 중첩 레이아웃 또는 페이지에 접근할 수 있습니다.

## 보호된 파일명

`app` 폴더에는 `page.js`, `icon.png` 또는 `layout.js` 와 같은 보호 파일명이 있어서 다양한 기능을 사용할 수 있게합니다.

### page.js

신규 페이지 생성합니다. (예: `app/about/page.js`은 `<your-domain>/about page`을 생성)

```tsx
// app/blog/[slug]/page.tsx

export default function Page({
  params,
  searchParams,
}: {
  params: { slug: string }
  searchParams: { [key: string]: string | string[] | undefined }
}) {
  return <h1>My Page</h1>
}
```

### layout.js

형제 및 중첩 페이지를 감싸는 신규 레이아웃 생성합니다.

```tsx
// app/layout.tsx

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  )
}
```

```tsx
// app/dashboard/layout.tsx

export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return <section>{children}</section>
}
```

### not-found.js

‘Not Found’ 오류에 대한 폴백 페이지(형제 또는 중첩 페이지 또는 레이아웃에서 전달된)

```tsx
// app/not-found.tsx

import Link from 'next/link'

export default function NotFound() {
  return (
    <div>
      <h2>Not Found</h2>
      <p>Could not find requested resource</p>
      <Link href="/">Return Home</Link>
    </div>
  )
}
```

### error.js

기타 오류에 대한 폴백 페이지입니다. (형제 또는 중첩 페이지 또는 레이아웃에서 전달된)

```tsx
'use client' // Error components must be Client Components

import { useEffect } from 'react'

export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string }
  reset: () => void
}) {
  useEffect(() => {
    // Log the error to an error reporting service
    console.error(error)
  }, [error])

  return (
    <div>
      <h2>Something went wrong!</h2>
      <button
        onClick={
          // Attempt to recover by trying to re-render the segment
          () => reset()
        }
      >
        Try again
      </button>
    </div>
  )
}
```

### loading.js

형제 또는 중첩 페이지(또는 레이아웃)가 데이터를 가져오는 동안 표시되는 폴백 페이지

```tsx
export default function Loading() {
  // 커스텀 로딩 스켈레톤 컴포넌트를 사용할 수도 있음
  return <p>Loading...</p>
}
```

### route.js

API 경로 생성(즉, JSX 코드가 아닌 데이터를 반환하는 페이지, 예: JSON 형식)

```tsx
// route.ts

export async function GET(request: Request) {}

export async function HEAD(request: Request) {}

export async function POST(request: Request) {}

export async function PUT(request: Request) {}

export async function DELETE(request: Request) {}

export async function PATCH(request: Request) {}

// 만약 OPTIONS 메서드가 정의되지 않은 경우, Next.js는 이를 자동으로 구현하고, 라우트 핸들러에 정의된 다른 메서드들(GET, POST 등)을 기반으로 적절한 Allow 응답 헤더를 설정합니다. OPTIONS 메서드는 클라이언트가 서버에서 지원하는 HTTP 메서드를 확인하는 데 사용됩니다. 직접 정의하려면 다음과 같이 작성할 수 있습니다:
export async function OPTIONS(request: Request) {}
```

```tsx
// app/dashboard/[team]/route.ts

type Params = {
  team: string
}

export async function GET(request: Request, context: { params: Params }) {
  const team = context.params.team // '1'
}

// Params라는 타입을 정의하여 라우트의 URL 파라미터 타입을 지정합니다.
```

### default.js

Next.js가 전체 페이지 새로고침 후에도 슬롯의 활성 상태(현재 페이지)를 복구할 수 없을 때, 대체 화면을 보여주기 위해 사용됩니다.\
만약 일치하는 하위 페이지가 없으면 `default.js`가 렌더링되며, 이 파일이 없으면 404 페이지가 대신 나타납니다. 특히 자식 경로에 대해서도 `default.js`가 필요할 수 있습니다.

### instrumentation.js

애플리케이션에 모니터링 및 로깅 도구를 통합하는 데 사용됩니다.\
이를 통해 애플리케이션의 성능과 동작을 추적하고, 프로덕션 환경에서 발생하는 문제를 디버깅할 수 있습니다.

이 파일을 사용하려면, 애플리케이션의 루트에 배치하거나 src 폴더를 사용하는 경우 그 안에 넣으면 됩니다.

#### Config Option

```tsx
module.exports = {
  experimental: {
    instrumentationHook: true,
  },
}
```

#### Export

```tsx
import { registerOTel } from '@vercel/otel'

export function register() {
  registerOTel('next-app')
}
```

### middleware.js

요청이 완료되기 전에 서버에서 코드를 실행하는 미들웨어를 작성하는 데 사용됩니다.\
들어오는 요청에 따라 응답을 재작성, 리디렉션, 요청이나 응답 헤더 수정, 또는 직접 응답하는 방식으로 응답을 수정할 수 있습니다.

미들웨어는 경로가 렌더링되기 전에 실행되며, 인증, 로깅, 리디렉션 처리 같은 서버 측 로직을 구현할 때 유용합니다.

middleware.ts(또는 .js) 파일을 프로젝트 루트에 배치하여 미들웨어를 정의할 수 있습니다.
예를 들어, app 또는 pages와 같은 수준에 두거나, src 폴더 안에 넣을 수 있습니다.

```tsx
// middleware.ts

import { NextResponse, NextRequest } from 'next/server'

// This function can be marked `async` if using `await` inside
export function middleware(request: NextRequest) {
  return NextResponse.redirect(new URL('/home', request.url))
}

export const config = {
  matcher: '/about/:path*',
}
```

### template.js

레이아웃과 유사하지만, 각 자식 레이아웃이나 페이지를 감싸는 역할을 합니다.\
레이아웃과는 달리, 레이아웃은 경로를 가로질러 지속되며 상태를 유지하는 반면, 템플릿은 내비게이션 시 각 자식에 대해 새로운 인스턴스를 생성합니다.

즉, 템플릿은 페이지 간 이동할 때마다 자식 컴포넌트의 새 인스턴스를 생성하므로 상태가 초기화됩니다.

```tsx
// app/template.tsx

export default function Template({
  children
}: {
  children: React.ReactNode
}) {
  return <div>{children}</div>
}
```

### globals.css

`layout.js` 파일에 `import` 되어 로딩된 모든 페이지에서 사용됩니다.

### icon.png

favicon으로 사용하게 됩니다.

## 커스텀 컴포넌트

`header` 같은 경우 특별할 파일명이 아니기 때문에 레이아웃 또는 페이지로 렌더링 되지 않습니다.

```tsx
// app/header.jsx

export default function header() {
  return (
    <>
      <img src="/logo.png" alt="A server surrounded by magic sparkles." />
      <h1>Welcome to this NextJS Course!</h1>
    </>
  )
}
```

그렇기 때문에 `app` 폴더 외부에서 위치해도 상관없습니다.

```tsx
// components/page.js

import Link from "next/link";

import Header from './components/header'

export default function Home() {
  console.log(`Executing...`)
  return (
    <main>
      <Header />
      <p>🔥 Let&apos;s get started! 🔥</p>
      <p><Link href="/about">About Us</Link></p>
    </main>
  );
}
```

`jsconfig` 파일에서 `import` 경로에 앳 사인(`@`)를 사용해 `root` 프로젝트를 조회할 수 있습니다.

```tsx
// components/page.js

import Link from "next/link";

import Header from '@/components/header'

export default function Home() {
  console.log(`Executing...`)
  return (
    <main>
      <Header />
      <p>🔥 Let&apos;s get started! 🔥</p>
      <p><Link href="/about">About Us</Link></p>
    </main>
  );
}
```

```json
// jsconfig.js

{
  "compilerOptions": {
    "paths": {
      "@/*": ["./*"]
    }
  }
}
```

## 동적 라우트

NextJS에서 dynamic route (동적 라우트)는 대괄호를 사용한 중첩 폴더를 추가해 만들 수 있습니다.\
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

export default function BlogPostPage() {
  return (
    <main>
      <h1>Blog Post</h1>
    </main>
  )
}
```

`<p><Link href="/blog/post-1">Post 1</Link></p>` 링크를 클릭하면 `[slug]`폴더는 `post-1` 이 되고, `<p><Link href="/blog/post-2">Post 2</Link></p>` 링크를 클릭하면 `[slug]`폴더는 `post-2` 이 됩니다.

## 경로 매개 변수

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
