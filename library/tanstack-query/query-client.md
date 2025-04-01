# QueryClient

`QueryClient`는 캐시와 상호작용하기 위한 중심 객체입니다. 쿼리/뮤테이션 관련 다양한 작업을 수행할 수 있는 메서드를 제공합니다.

## 사용 예시

```tsx
import { QueryClient } from "@tanstack/react-query";

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: Infinity, // 쿼리 데이터는 항상 fresh 상태로 유지
    },
  },
});

await queryClient.prefetchQuery({
  queryKey: ["posts"],
  queryFn: fetchPosts,
});
```

### 생성자 옵션

`QueryClient`를 생성할 때 다음과 같은 옵션들을 전달할 수 있습니다:

| 옵션명           | 타입             | 설명                                                 |
| ---------------- | ---------------- | ---------------------------------------------------- |
| `queryCache`     | `QueryCache`     | 이 클라이언트가 연결될 쿼리 캐시 (선택 사항)         |
| `mutationCache`  | `MutationCache`  | 이 클라이언트가 연결될 뮤테이션 캐시 (선택 사항)     |
| `defaultOptions` | `DefaultOptions` | 쿼리 및 뮤테이션에 대한 기본 옵션을 정의 (선택 사항) |

> `defaultOptions`는 Hydration에도 적용할 수 있습니다.

## `queryClient.fetchQuery`

`fetchQuery`는 비동기적으로 쿼리를 가져오고 캐시에 저장하는 메서드입니다.  
성공하면 데이터를 반환하고, 실패하면 에러를 던집니다.

> 결과를 사용할 필요 없이 단순히 데이터를 미리 받아오고 싶다면, `prefetchQuery`를 사용하는 것이 더 적합합니다.

### 작동 방식

- 쿼리가 이미 존재하고, 무효화되지 않았으며 `staleTime`보다 최근이라면, 캐시된 데이터를 반환합니다.
- 위 조건을 만족하지 않으면, 최신 데이터를 서버로부터 요청합니다.

### 기본 사용 예시

```tsx
try {
  const data = await queryClient.fetchQuery({
    queryKey,
    queryFn,
  });
} catch (error) {
  console.log(error);
}
```

### `staleTime` 사용 예시

`staleTime`을 설정하면, 해당 시간이 지나기 전에는 캐시된 데이터만 사용하고,  
시간이 지난 경우에만 새로운 데이터를 가져옵니다.

```tsx
try {
  const data = await queryClient.fetchQuery({
    queryKey,
    queryFn,
    staleTime: 10000, // 10초 동안은 캐시 데이터 사용
  });
} catch (error) {
  console.log(error);
}
```

### 옵션

`fetchQuery`의 옵션은 `useQuery`와 거의 동일하지만,  
다음 옵션은 지원하지 않으며, `useQuery`/`useInfiniteQuery` 전용입니다:

- `enabled`
- `refetchInterval`
- `refetchIntervalInBackground`
- `refetchOnWindowFocus`
- `refetchOnReconnect`
- `refetchOnMount`
- `notifyOnChangeProps`
- `throwOnError`
- `select`
- `suspense`
- `placeholderData`

### 반환값

```ts
Promise<TData>;
```

비동기적으로 동작하며, 데이터가 준비되면 `Promise`를 통해 반환됩니다.

## queryClient.fetchInfiniteQuery

`fetchInfiniteQuery`는 `fetchQuery`와 유사하지만, **무한 쿼리(infinite query)**에 사용됩니다.  
주로 페이지네이션이나 스크롤 기반 로딩에 활용됩니다.

### fetchInfiniteQuery 사용 예시

```tsx
try {
  const data = await queryClient.fetchInfiniteQuery({
    queryKey,
    queryFn,
  });
  console.log(data.pages);
} catch (error) {
  console.log(error);
}
```

### fetchInfiniteQuery 옵션

`fetchInfiniteQuery`의 옵션은 `fetchQuery`와 동일합니다.

### fetchInfiniteQuery 반환값

```ts
Promise<InfiniteData<TData, TPageParam>>;
```

## queryClient.prefetchQuery

`prefetchQuery`는 `useQuery`로 데이터를 실제로 렌더링하기 **전에 미리 데이터를 가져오는** 비동기 메서드입니다.  
`fetchQuery`와 거의 동일하게 동작하지만, **결과 데이터를 반환하거나 에러를 throw하지 않습니다.**

### prefetchQuery 사용 예시

```tsx
await queryClient.prefetchQuery({
  queryKey,
  queryFn,
});
```

또는, `default queryFn`이 설정된 경우:

```tsx
await queryClient.prefetchQuery({
  queryKey,
});
```

### prefetchQuery 옵션

`prefetchQuery`의 옵션은 `fetchQuery`와 동일합니다.

### prefetchQuery 반환값

```ts
Promise<void>;
```

- 요청이 필요하지 않으면 즉시 resolve됩니다.
- 요청이 필요한 경우, 쿼리가 끝난 후 resolve됩니다.
- **데이터를 반환하거나 에러를 throw하지 않습니다.**

## queryClient.prefetchInfiniteQuery

`prefetchInfiniteQuery`는 `prefetchQuery`와 유사하지만, **무한 쿼리(infinite query)**에 사용할 수 있습니다.  
미리 데이터를 가져와 캐시에 저장해두는 데 사용됩니다.

### prefetchInfiniteQuery 사용 예시

```tsx
await queryClient.prefetchInfiniteQuery({
  queryKey,
  queryFn,
});
```

### prefetchInfiniteQuery 옵션

`fetchQuery`와 동일한 옵션을 사용합니다.

### prefetchInfiniteQuery 반환값

```ts
Promise<void>;
```

- 요청이 필요하지 않으면 즉시 resolve됩니다.
- 요청이 필요하면 쿼리 실행 이후 resolve됩니다.
- **데이터를 반환하지 않고, 에러도 발생시키지 않습니다.**

## queryClient.getQueryData

`getQueryData`는 동기 함수이며, 지정한 쿼리의 **캐시된 데이터를 즉시 반환**합니다.  
쿼리가 존재하지 않으면 `undefined`를 반환합니다.

### getQueryData 사용 예시

```tsx
const data = queryClient.getQueryData(queryKey);
```

### getQueryData 옵션

- `queryKey: QueryKey` — 조회할 쿼리 키

### getQueryData 반환값

```ts
TQueryFnData | undefined;
```

- 캐시된 데이터가 있다면 해당 데이터
- 쿼리가 존재하지 않으면 `undefined`

## queryClient.ensureQueryData

`ensureQueryData`는 비동기 함수이며, 쿼리의 **캐시된 데이터를 반환**합니다.  
쿼리가 존재하지 않으면 내부적으로 `fetchQuery`를 호출하여 데이터를 가져옵니다.

### ensureQueryData 사용 예시

```tsx
const data = await queryClient.ensureQueryData({
  queryKey,
  queryFn,
});
```

### ensureQueryData 옵션

- `fetchQuery`와 동일한 옵션 사용 가능
- `revalidateIfStale?: boolean`
  - 기본값: `false`
  - `true`로 설정 시, stale 데이터는 즉시 반환되고, 백그라운드에서 재요청이 실행됩니다.

### ensureQueryData 반환값

```ts
Promise<TData>;
```

- 기존 데이터 또는 서버에서 새로 받아온 데이터를 반환합니다.

## queryClient.ensureInfiniteQueryData

`ensureInfiniteQueryData`는 비동기 함수로, **무한 쿼리의 캐시 데이터를 반환**합니다.  
쿼리가 존재하지 않으면 `fetchInfiniteQuery`를 호출하여 데이터를 가져옵니다.

### ensureInfiniteQueryData 사용 예시

```tsx
const data = await queryClient.ensureInfiniteQueryData({
  queryKey,
  queryFn,
  initialPageParam,
  getNextPageParam,
});
```

### ensureInfiniteQueryData 옵션

- `fetchInfiniteQuery`와 동일한 옵션 사용 가능
- `revalidateIfStale?: boolean`
  - 기본값: `false`
  - `true`로 설정 시, stale 데이터는 즉시 반환되고 백그라운드에서 다시 요청됩니다.

### ensureInfiniteQueryData 반환값

```ts
Promise<InfiniteData<TData, TPageParam>>;
```

- 기존에 캐시된 무한 쿼리 데이터 또는 새로 받아온 데이터를 반환합니다.

## queryClient.getQueriesData

`getQueriesData`는 동기 함수이며, 여러 개의 쿼리에 대해 **캐시된 데이터를 가져오는 함수**입니다.  
전달된 `queryKey` 또는 `queryFilter`와 일치하는 쿼리들만 반환합니다.

### getQueriesData 사용 예시

```tsx
const data = queryClient.getQueriesData(filters);
```

### getQueriesData 옵션

- `filters: QueryFilters`
  - 전달된 필터와 일치하는 쿼리들만 반환됩니다.

### getQueriesData 반환값

```ts
[queryKey: QueryKey, data: TQueryFnData | undefined][]
```

- 일치하는 쿼리들의 `(queryKey, data)` 쌍을 포함한 배열을 반환합니다.
- 일치하는 쿼리가 없다면 빈 배열 `[]`을 반환합니다.

### 주의 사항

- 반환되는 각 `data` 값의 구조는 쿼리마다 다를 수 있습니다.
- 타입스크립트 사용 시 기본적으로 `TData`는 `unknown`으로 처리됩니다.
- 동일한 타입의 데이터만 반환된다는 확신이 있다면, 제네릭 타입 `TData`를 명시할 수 있습니다.

## queryClient.setQueryData

`setQueryData`는 **지정한 쿼리의 캐시 데이터를 즉시 동기적으로 수정**하는 함수입니다.  
쿼리가 존재하지 않으면 자동으로 생성되며, 사용되지 않을 경우 기본 `gcTime`(5분) 이후 가비지 컬렉션됩니다.

### setQueryData 사용 예시

```tsx
queryClient.setQueryData(queryKey, updater);
```

### setQueryData 옵션

- `queryKey: QueryKey`
  - 대상이 되는 쿼리의 키
- `updater: TQueryFnData | undefined | ((oldData: TQueryFnData | undefined) => TQueryFnData | undefined)`
  - 값 자체를 전달하면 캐시가 해당 값으로 갱신됩니다.
  - 함수를 전달하면 기존 데이터를 받아 새로운 값을 리턴하는 방식으로 갱신됩니다.

#### 값으로 직접 업데이트

```tsx
queryClient.setQueryData(queryKey, newData);
```

- `newData`가 `undefined`이면 데이터는 업데이트되지 않습니다.

#### 함수로 업데이트

```tsx
queryClient.setQueryData(queryKey, (oldData) => {
  return newData;
});
```

- `oldData`가 `undefined`이면 `undefined`를 리턴하여 업데이트를 중단할 수 있습니다.
- 함수가 `undefined`를 반환하면 새로운 캐시 엔트리를 만들지 않습니다.

### 비동기 데이터 갱신과의 차이점

- `setQueryData`는 **데이터가 이미 메모리에 존재할 때 사용하는 동기적 방식**입니다.
- 서버에서 새로 데이터를 받아와야 하는 경우, `fetchQuery`나 `refetchQueries`를 사용해야 합니다.

### 불변성 주의사항

- `setQueryData`를 사용할 때는 **불변성(immutability)**을 유지해야 합니다.
- `getQueryData`로 가져온 데이터를 직접 수정하는 방식은 **절대 사용하지 마세요.**

## queryClient.getQueryState

`getQueryState`는 동기 함수로, **쿼리의 현재 상태 객체를 반환**합니다.  
쿼리가 존재하지 않으면 `undefined`가 반환됩니다.

### getQueryState 사용 예시

```tsx
const state = queryClient.getQueryState(queryKey);
console.log(state.dataUpdatedAt);
```

### getQueryState 옵션

- `queryKey: QueryKey`
  - 상태를 조회할 쿼리의 키

### getQueryState 반환값

- 쿼리 상태 객체 또는 쿼리가 없으면 `undefined`

> 이 메서드는 쿼리의 내부 상태(예: `dataUpdatedAt`, `status`, `fetchStatus` 등)를 확인할 때 유용합니다.

## queryClient.setQueriesData

`setQueriesData`는 **동기 함수**로, 여러 쿼리의 캐시 데이터를 한 번에 수정할 수 있습니다.  
`queryKey`의 일부 또는 `queryFilter` 조건에 맞는 쿼리만 업데이트되며, **새로운 캐시 엔트리는 생성되지 않습니다.**  
내부적으로는 `setQueryData`가 각 일치하는 쿼리에 대해 호출됩니다.

### setQueriesData 사용 예시

```tsx
queryClient.setQueriesData(filters, updater);
```

### setQueriesData 옵션

- `filters: QueryFilters`
  - 필터에 해당하는 `queryKey`를 가진 쿼리들만 업데이트됩니다.
- `updater: TQueryFnData | (oldData: TQueryFnData | undefined) => TQueryFnData`
  - 새로운 데이터 또는 이전 데이터를 기반으로 새로운 값을 반환하는 함수

## queryClient.invalidateQueries

`invalidateQueries`는 **하나 또는 여러 쿼리를 무효화하고 백그라운드에서 다시 가져오도록(triggers refetch)** 설정할 수 있는 메서드입니다.  
쿼리 키 또는 쿼리 상태/속성 등을 기준으로 조건을 설정할 수 있습니다.

### invalidateQueries 기본 동작

- 기본적으로 조건에 맞는 **모든 쿼리는 즉시 invalid 처리**됩니다.
- `useQuery` 등으로 현재 사용 중인(active) 쿼리는 **백그라운드에서 자동으로 다시 요청됩니다**.

### invalidateQueries 사용 예시

```tsx
await queryClient.invalidateQueries(
  {
    queryKey: ["posts"],
    exact: true,
    refetchType: "active", // 기본값
  },
  {
    throwOnError: true,
    cancelRefetch: true,
  }
);
```

### 첫 번째 인자: Query 필터 옵션

- `filters?: QueryFilters`
- `queryKey?: QueryKey`
- `refetchType?: 'active' | 'inactive' | 'all' | 'none'`
  - 기본값: `'active'`

| 옵션값     | 설명                                                                                  |
| ---------- | ------------------------------------------------------------------------------------- |
| `active`   | 현재 활성 상태인 쿼리만 백그라운드에서 다시 요청됨 (`useQuery` 등에서 사용 중인 쿼리) |
| `inactive` | 비활성 상태인 쿼리만 다시 요청됨                                                      |
| `all`      | 모든 일치하는 쿼리가 다시 요청됨                                                      |
| `none`     | 어떤 쿼리도 다시 요청되지 않으며, 단지 무효화만 됨                                    |

### 두 번째 인자: Invalidate 옵션

- `throwOnError?: boolean`
  - `true`일 경우, refetch 중 오류가 발생하면 예외를 throw합니다.
- `cancelRefetch?: boolean`
  - 기본값: `true`
  - 현재 진행 중인 요청을 취소하고 새 요청을 실행합니다.
  - `false`로 설정하면 요청 중인 쿼리는 다시 요청하지 않습니다.

## queryClient.refetchQueries

`refetchQueries`는 **지정된 조건에 따라 쿼리를 다시 요청(refetch)** 하는 메서드입니다.

### refetchQueries 사용 예시

```tsx
// 모든 쿼리 다시 요청
await queryClient.refetchQueries();

// stale 상태인 쿼리만 다시 요청
await queryClient.refetchQueries({ stale: true });

// 'posts'로 시작하는 활성 쿼리 다시 요청
await queryClient.refetchQueries({ queryKey: ["posts"], type: "active" });

// 정확히 ['posts', 1]인 활성 쿼리만 다시 요청
await queryClient.refetchQueries({
  queryKey: ["posts", 1],
  type: "active",
  exact: true,
});
```

### refetchQueries 옵션

#### 첫 번째 인자 (`filters?: QueryFilters`)

- `queryKey`, `stale`, `type`, `exact` 등 사용 가능

#### 두 번째 인자 (`options?: RefetchOptions`)

- `throwOnError?: boolean`
  - `true`일 경우, 하나라도 refetch에 실패하면 에러를 throw
- `cancelRefetch?: boolean`
  - 기본값: `true`
  - 기존 요청이 있다면 취소하고 새로 요청

### refetchQueries 반환값

- 모든 쿼리의 refetch가 완료되면 resolve되는 `Promise`
- 기본적으로 refetch 실패 시에도 에러를 throw하지 않지만, `throwOnError`로 설정 가능

## queryClient.cancelQueries

`cancelQueries`는 **진행 중인 쿼리 요청을 취소**할 때 사용됩니다.  
주로 **낙관적 업데이트(Optimistic Update)** 수행 시, 기존 요청이 낙관적 데이터와 충돌하지 않도록 할 때 유용합니다.

### cancelQueries 사용 예시

```tsx
await queryClient.cancelQueries({ queryKey: ["posts"], exact: true });
```

### cancelQueries 옵션

- `filters?: QueryFilters`
  - `queryKey`, `exact`, `predicate` 등 사용 가능

### cancelQueries 반환값

- 반환값 없음 (`void`)

## queryClient.removeQueries

`removeQueries`는 **지정된 조건에 맞는 쿼리를 캐시에서 제거**합니다.  
쿼리 키 또는 필터로 일치하는 쿼리만 제거되며, **캐시에서 완전히 삭제**됩니다.

### removeQueries 사용 예시

```tsx
queryClient.removeQueries({ queryKey, exact: true });
```

### removeQueries 옵션

- `filters?: QueryFilters`
  - 삭제할 쿼리의 조건

### removeQueries 반환값

- 없음 (`void`)

## queryClient.resetQueries

`resetQueries`는 **지정된 쿼리들을 초기 상태로 리셋**하는 메서드입니다.  
이는 `invalidateQueries`와 달리 **구독자(subscriber)를 유지하며**,  
`initialData`가 있다면 해당 값으로 데이터를 되돌립니다.

활성 쿼리인 경우, 자동으로 다시 요청됩니다.

### resetQueries 사용 예시

```tsx
queryClient.resetQueries({ queryKey, exact: true });
```

### 첫 번째 인자: 필터

- `filters?: QueryFilters`
  - 초기화할 쿼리를 지정하는 필터

### 두 번째 인자: 옵션

- `throwOnError?: boolean`
  - `true`일 경우, refetch 중 실패하면 에러를 throw
- `cancelRefetch?: boolean`
  - 기본값: `true`
  - 진행 중인 요청을 취소한 뒤 새 요청을 실행

### resetQueries 반환값

```ts
Promise<void>;
```

- 모든 활성 쿼리의 reset과 refetch가 완료되면 resolve됨

## queryClient.isFetching

`isFetching`은 현재 **fetching 중인 쿼리의 수**를 반환하는 메서드입니다.  
이는 백그라운드 fetch, 새 페이지 로딩, infinite query의 추가 페이지 로딩 등을 포함합니다.

### isFetching 사용 예시

```tsx
if (queryClient.isFetching()) {
  console.log("At least one query is fetching!");
}
```

### isFetching 옵션

- `filters?: QueryFilters`
  - 특정 쿼리 조건을 기반으로 필터링 가능

### isFetching 반환값

```ts
number;
```

- 현재 fetching 중인 쿼리의 개수

> 컴포넌트에서 상태를 구독하고 싶다면, `useIsFetching` 훅을 사용하는 것이 권장됩니다.

## queryClient.isMutating

`isMutating`은 현재 **fetching 중인 뮤테이션의 수**를 반환하는 메서드입니다.

### isMutating 사용 예시

```tsx
if (queryClient.isMutating()) {
  console.log("At least one mutation is fetching!");
}
```

### isMutating 옵션

- `filters: MutationFilters`
  - 뮤테이션 필터 조건 사용 가능

### isMutating 반환값

```ts
number;
```

- 현재 fetch 중인 뮤테이션 개수

> 컴포넌트에서 상태를 구독하고 싶다면, `useIsMutating` 훅을 사용하는 것이 유용합니다.

## queryClient.getDefaultOptions

`getDefaultOptions`는 `QueryClient` 생성 시 설정한 기본 옵션이나,  
`setDefaultOptions`를 통해 나중에 설정한 값을 반환합니다.

### getDefaultOptions 사용 예시

```tsx
const defaultOptions = queryClient.getDefaultOptions();
```

## queryClient.setDefaultOptions

`setDefaultOptions`는 `QueryClient`의 **기본 옵션을 동적으로 설정**할 수 있는 메서드입니다.  
이전에 정의된 값은 덮어쓰기(overwrite)됩니다.

### setDefaultOptions 사용 예시

```tsx
queryClient.setDefaultOptions({
  queries: {
    staleTime: Infinity,
  },
});
```

## queryClient.getQueryDefaults

`getQueryDefaults`는 특정 `queryKey`에 대해 등록된 기본 옵션들을 반환합니다.

### getQueryDefaults 사용 예시

```tsx
const defaultOptions = queryClient.getQueryDefaults(["posts"]);
```

> 여러 `queryKey`와 일치하는 기본값이 등록되어 있으면, 등록 순서에 따라 병합됩니다.  
> 더 구체적인 키를 나중에 등록하는 것이 우선순위를 가지게 됩니다.

## queryClient.setQueryDefaults

`setQueryDefaults`는 특정 쿼리 키에 대해 기본 옵션을 설정하는 메서드입니다.  
`getQueryDefaults`와 연동되며, 등록 순서가 중요합니다.

### setQueryDefaults 사용 예시

```tsx
queryClient.setQueryDefaults(["posts"], {
  queryFn: fetchPosts,
});

function Component() {
  const { data } = useQuery({
    queryKey: ["posts"],
  });
}
```

### setQueryDefaults 옵션

- `queryKey: QueryKey`
  - 기본 옵션을 설정할 대상 쿼리 키
- `options: QueryOptions`
  - 해당 쿼리의 기본 옵션 정의

> 기본값 등록 시에는 **일반적인 키 → 구체적인 키 순서로 등록**해야 병합 우선순위가 올바르게 동작합니다.

## queryClient.getMutationDefaults

`getMutationDefaults`는 특정 `mutationKey`에 대해 설정된 **기본 옵션**을 반환합니다.

### getMutationDefaults 사용 예시

```tsx
const defaultOptions = queryClient.getMutationDefaults(["addPost"]);
```

- 지정된 `mutationKey`와 일치하는 뮤테이션의 기본 설정값을 반환합니다.

## queryClient.setMutationDefaults

`setMutationDefaults`는 특정 `mutationKey`에 대해 **기본 옵션을 등록**하는 데 사용됩니다.

### setMutationDefaults 사용 예시

```tsx
queryClient.setMutationDefaults(["addPost"], {
  mutationFn: addPost,
});

function Component() {
  const { data } = useMutation({
    mutationKey: ["addPost"],
  });
}
```

### setMutationDefaults 옵션

- `mutationKey: unknown[]`
  - 기본 옵션을 적용할 뮤테이션 키
- `options: MutationOptions`
  - 해당 뮤테이션의 기본 설정값

> `setQueryDefaults`와 마찬가지로, 등록 순서가 중요합니다.  
> **일반적인 키 → 구체적인 키 순서**로 등록해야 병합 우선순위가 올바르게 작동합니다.

## queryClient.getQueryCache

`getQueryCache`는 현재 `QueryClient`가 연결된 **쿼리 캐시 인스턴스**를 반환합니다.

### getQueryCache 사용 예시

```tsx
const queryCache = queryClient.getQueryCache();
```

- 캐시에 직접 접근하거나, 내부 동작을 커스터마이징할 때 사용됩니다.

## queryClient.getMutationCache

`getMutationCache`는 현재 `QueryClient`가 연결된 **뮤테이션 캐시 인스턴스**를 반환합니다.

### getMutationCache 사용 예시

```tsx
const mutationCache = queryClient.getMutationCache();
```

## queryClient.clear

`clear`는 `QueryClient`에 연결된 **모든 캐시 (쿼리 및 뮤테이션)** 를 초기화합니다.

### clear 사용 예시

```tsx
queryClient.clear();
```

- 모든 데이터, 상태, 구독자가 제거됩니다.

> `resetQueries`는 구독자를 유지하며 데이터를 초기화하지만, `clear`는 구독자까지 제거합니다.

## queryClient.resumePausedMutations

`resumePausedMutations`는 **네트워크 연결이 끊겨 중단된 뮤테이션을 다시 재개**할 때 사용됩니다.

### resumePausedMutations 사용 예시

```tsx
queryClient.resumePausedMutations();
```

- 예를 들어, 사용자가 오프라인일 때 실패한 뮤테이션을 다시 실행할 수 있습니다.
- `window.addEventListener('online')` 이벤트와 함께 자주 사용됩니다.

```tsx
window.addEventListener("online", () => {
  queryClient.resumePausedMutations();
});
```
