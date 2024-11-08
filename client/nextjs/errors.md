# Errors

## Missing Suspense boundary with useSearchParams

`useSearchParams`는 현재 브라우저로 부터 요청을 받은 쿼리 스트링의 값을 가져옵니다.\
하지만 빌드 타임에는 쿼리 스트링이 존재할 수 없기 때문에 에러가 발생하는 것입니다.

### 해결법

`useSearchParams`를 사용하는 컴포넌트를 사전 렌더링에서 배제하고 오직 클라이언트측에서만 실행 되도록 설정하는 방법이 있습니다.

컴포넌트를 배체하는 방법으로는 React의 `Suspense`를 사용하는 것이 있습니다.\
Next 서버측에서 사전 렌더링을 진행할 때 `Suspense`에 감싸져 있는 컴포넌트는 바로 렌더링 하지 않고 `fallback`이라는 props로 전달한 대체 UI를 렌더링합니다.

`Suspense`에 감싸져 있는 컴포넌트는 비동기 작업(`useSearchParams`는 비동기 함수)이 종료 되어야 완료됩니다.\
즉, 브라우저에 마운트 되어 쿼리 스트링을 불러 왔을 때 렌더링이 이루어지게 됩니다.

```tsx
'use client'

import { useSearchParams } from 'next/navigation'
import { Suspense } from 'react'

// useSearchParams를 사용하는 컴포넌트
function Search() {
  const searchParams = useSearchParams()

  return <input placeholder="Search..." />
}

export function Searchbar() {
  return (
    // 사전 렌더링에서는 fallback에 해당하는 UI를 렌더링
    <Suspense fallback={<div>Loading ...</div>}>
      <Search />
    </Suspense>
  )
}
```
