# Paginated / Lagged Queries

<br>

## 📌 페이지네이션 UI는 어떻게 처리하나요?

페이지 기반 데이터 렌더링은 아주 일반적인 UI 패턴입니다.  
TanStack Query에서는 쿼리 키에 페이지 정보를 포함하는 것만으로 동작합니다:

### 예시:

```tsx
const result = useQuery({
  queryKey: ["projects", page],
  queryFn: fetchProjects,
});
```

하지만 위와 같은 단순한 예제를 실행해보면,  
다음과 같은 **이상한 UI 점프 현상**을 목격하게 됩니다:

- 각 페이지 이동마다 `success ↔ pending` 상태로 전환됨
- 쿼리 키가 바뀌기 때문에 매번 새 쿼리처럼 취급됨

이건 최적의 UX가 아니며, 많은 라이브러리들이 이런 식으로 작동합니다.  
그러나 TanStack Query는 다릅니다!  
바로 **`placeholderData`를 이용한 부드러운 페이지 전환**을 지원하기 때문입니다.

<br>

## placeholderData를 이용한 더 나은 페이지네이션

페이지 인덱스나 커서를 기반으로 데이터를 패치할 때,  
아래와 같이 `placeholderData`를 `keepPreviousData`로 설정하면 다음과 같은 이점이 있습니다:

1. **이전 성공 데이터가 유지됨**  
   → 새로운 쿼리가 로딩되는 동안에도 기존 데이터를 보여줄 수 있음

2. **새 데이터가 오면 매끄럽게 교체됨**

3. **`isPlaceholderData` 플래그 제공**  
   → 현재 표시 중인 데이터가 임시(이전) 데이터인지 확인 가능

### 예시: 페이지 이동 UI 구현

```tsx
import { keepPreviousData, useQuery } from "@tanstack/react-query";
import React from "react";

function Todos() {
  const [page, setPage] = React.useState(0);

  const fetchProjects = (page = 0) =>
    fetch("/api/projects?page=" + page).then((res) => res.json());

  const { isPending, isError, error, data, isFetching, isPlaceholderData } =
    useQuery({
      queryKey: ["projects", page],
      queryFn: () => fetchProjects(page),
      placeholderData: keepPreviousData,
    });

  return (
    <div>
      {isPending ? (
        <div>Loading...</div>
      ) : isError ? (
        <div>Error: {error.message}</div>
      ) : (
        <div>
          {data.projects.map((project) => (
            <p key={project.id}>{project.name}</p>
          ))}
        </div>
      )}
      <span>Current Page: {page + 1}</span>
      <button
        onClick={() => setPage((old) => Math.max(old - 1, 0))}
        disabled={page === 0}
      >
        Previous Page
      </button>
      <button
        onClick={() => {
          if (!isPlaceholderData && data.hasMore) {
            setPage((old) => old + 1);
          }
        }}
        disabled={isPlaceholderData || !data?.hasMore}
      >
        Next Page
      </button>
      {isFetching ? <span> Loading...</span> : null}
    </div>
  );
}
```

<br>

## Infinite Query에서도 placeholderData 사용 가능

무한 스크롤 방식에서도 `placeholderData`는 완벽하게 작동합니다.  
`useInfiniteQuery`와 함께 사용할 경우,  
사용자가 **캐시된 데이터를 계속 볼 수 있게 하면서도** 쿼리 키가 바뀌는 과정을 부드럽게 처리할 수 있습니다.

<br>

필요한 경우 위 내용과 연결되는 `Paginated Queries` vs `Infinite Queries` 차이점도 정리해드릴 수 있어요!  
언제든 말씀해주세요.
