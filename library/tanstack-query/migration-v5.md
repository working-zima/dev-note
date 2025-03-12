# TanStack Query v5 마이그레이션

## 🛠️ Breaking Changes

v5는 메이저 버전이므로 주의해야 할 **호환성 깨짐(breaking changes)** 이 포함되어 있습니다.

<br>

## ✅ 하나의 시그니처만 지원: 객체 형태로 통일

`useQuery` 및 관련 훅들은 기존에는 여러 형태의 오버로드(overload)를 지원했습니다.  
하지만 이는 타입 유지보수가 어렵고 런타임 체크도 필요했기 때문에, **이제는 오직 하나의 객체 인자 형태만 지원합니다.**

### ❗ 기존 방식 (제거됨)

```tsx
useQuery(queryKey, queryFn, options);
useInfiniteQuery(queryKey, queryFn, options);
useMutation(mutationFn, options);
useIsFetching(queryKey, filters);
useIsMutating(mutationKey, filters);
```

### ✅ 변경된 방식

```tsx
useQuery({ queryKey, queryFn, ...options });
useInfiniteQuery({ queryKey, queryFn, ...options });
useMutation({ mutationFn, ...options });
useIsFetching({ queryKey, ...filters });
useIsMutating({ mutationKey, ...filters });
```

<br>

## ✅ queryClient 메서드 변경

다음과 같은 메서드들도 모두 **객체 인자**만 받도록 변경되었습니다:

```tsx
queryClient.isFetching({ queryKey, ...filters });
queryClient.ensureQueryData({ queryKey, ...filters });
queryClient.getQueriesData({ queryKey, ...filters });
queryClient.setQueriesData({ queryKey, ...filters }, updater, options);
queryClient.removeQueries({ queryKey, ...filters });
queryClient.resetQueries({ queryKey, ...filters }, options);
queryClient.cancelQueries({ queryKey, ...filters }, options);
queryClient.invalidateQueries({ queryKey, ...filters }, options);
queryClient.refetchQueries({ queryKey, ...filters }, options);
queryClient.fetchQuery({ queryKey, queryFn, ...options });
queryClient.prefetchQuery({ queryKey, queryFn, ...options });
queryClient.fetchInfiniteQuery({ queryKey, queryFn, ...options });
queryClient.prefetchInfiniteQuery({ queryKey, queryFn, ...options });
```

<br>

## ✅ queryCache 변경

```tsx
queryCache.find({ queryKey, ...filters });
queryCache.findAll({ queryKey, ...filters });
```

<br>

## ✅ queryClient.getQueryData / getQueryState

두 메서드 모두 `queryKey`만 인자로 받습니다.

```tsx
// 이전 방식
queryClient.getQueryData(queryKey, filters);

// 변경 방식
queryClient.getQueryData(queryKey);

// getQueryState도 동일
queryClient.getQueryState(queryKey);
```

<br>

## ⚙️ Codemod 지원

이러한 오버로드 제거 마이그레이션을 쉽게 할 수 있도록 `v5`는 codemod를 제공합니다.

> ❗ 자동 변경 도구이지만, 반드시 결과를 검토하세요! 일부 케이스는 자동으로 변환되지 않을 수 있습니다.

### js/jsx 파일에 적용하려면

```tsx
npx jscodeshift@latest ./path/to/src/ \
  --extensions=js,jsx \
  --transform=./node_modules/@tanstack/react-query/build/codemods/src/v5/remove-overloads/remove-overloads.cjs
```

### ts/tsx 파일에 적용하려면

```tsx
npx jscodeshift@latest ./path/to/src/ \
  --extensions=ts,tsx \
  --parser=tsx \
  --transform=./node_modules/@tanstack/react-query/build/codemods/src/v5/remove-overloads/remove-overloads.cjs
```

> ⚠️ TypeScript의 경우 `--parser=tsx`를 반드시 사용해야 정상 적용됩니다.
> 💄 Codemod 적용 후에는 반드시 `prettier` 또는 `eslint --fix` 등을 실행하여 코드 스타일을 정리하세요.

<br>

## 🧠 Codemod 작동 방식 요약

- 이미 객체 인자 형태이고 `queryKey` 또는 `mutationKey`가 포함된 경우 → codemod는 해당 코드를 **변경하지 않음** 🎉
- 배열이나 배열을 참조하는 식별자인 경우 → 객체로 감싸줌
- 기존 속성이 추론 가능한 경우 → 새 객체로 복사
- 추론 불가한 경우 → 콘솔에 파일명 + 라인 번호 출력 → **수동 처리 필요**
- 변환 실패 시 → 콘솔에 오류 메시지 출력 → **수동 마이그레이션 필요**

## ✅ Callbacks on useQuery (and QueryObserver) have been removed

`useQuery` 및 `QueryObserver`에서는 다음 콜백들이 제거되었습니다:

- `onSuccess`
- `onError`
- `onSettled`

> 이들은 **Mutation에서는 여전히 사용 가능**합니다.  
> 왜 제거되었는지와 대체 방안은 공식 RFC 문서를 참고하세요.

<br>

## ✅ refetchInterval 콜백은 이제 query 객체만 받습니다

기존에는 `refetchInterval` 콜백이 `data`와 `query` 두 인자를 받았지만,  
이제는 **오직 `query` 객체 하나만** 받도록 변경되었습니다.

> 이는 `refetchOnWindowFocus`, `refetchOnMount`, `refetchOnReconnect` 등과 동일한 형태이며,  
> `select`로 변환된 데이터를 받는 과정에서 발생하던 타입 문제도 해결됩니다.

### 변경 전후 타입

```tsx
// 변경 전
refetchInterval: number |
  false |
  ((data: TData | undefined, query: Query) => number | false | undefined);

// 변경 후
refetchInterval: number |
  false |
  ((query: Query) => number | false | undefined);
```

⚠️ `query.state.data`를 통해 여전히 원본 데이터를 접근할 수 있지만,  
이는 `select`로 변환된 데이터가 **아니므로** 주의하세요.  
`select`로 변환한 결과가 필요하다면, 다시 수동으로 변환해야 합니다.

<br>

## ✅ `remove` 메서드가 제거되었습니다

`useQuery`에서는 `remove()` 메서드가 제거되었습니다.

기존에는 이 메서드를 통해 queryCache에서 쿼리를 제거할 수 있었지만,  
**관찰자(observer)에게 알리지 않고 제거**되기 때문에 부작용이 있을 수 있습니다.

예: 사용자가 로그아웃할 때 데이터를 강제로 지우는 경우

이제는 다음과 같이 `queryClient.removeQueries`를 사용하세요:

```tsx
const queryClient = useQueryClient();
const query = useQuery({ queryKey, queryFn });

// 이전 방식 (제거됨)
query.remove();

// 변경된 방식
queryClient.removeQueries({ queryKey });
```

<br>

## ✅ 최소 TypeScript 버전은 4.7 이상으로 상향되었습니다

타입 추론 관련 중요한 패치가 포함되어 있기 때문입니다.  
자세한 내용은 [해당 TypeScript 이슈](https://github.com/microsoft/TypeScript/issues/47109) 참고.

<br>

## ✅ `isDataEqual` 옵션 제거 → `structuralSharing`으로 대체

기존 `isDataEqual`은 이전 데이터와 새 데이터를 비교해, 같으면 이전 데이터를 유지하게 해주는 역할을 했습니다.

v5에서는 이 기능을 `structuralSharing` 함수로 대체합니다.

### 예시

```tsx
import { replaceEqualDeep } from "@tanstack/react-query";

// 제거된 방식
isDataEqual: (oldData, newData) => customCheck(oldData, newData);

// 변경된 방식
structuralSharing: (oldData, newData) =>
  customCheck(oldData, newData) ? oldData : replaceEqualDeep(oldData, newData);
```

## ✅ deprecated custom logger 제거됨

v4에서 이미 deprecated 되었던 **custom logger** 기능이 완전히 제거되었습니다.

이 기능은 개발 모드에서만 작동했고,  
실제 프로덕션 동작에는 영향을 주지 않았습니다.

→ 이제 로깅이 필요한 경우, React 디버깅 툴 또는 외부 로거를 사용하세요.

<br>

## ✅ 지원 브라우저 목록 업데이트

브라우저 지원 목록이 업데이트되어 더 **현대적이고 가벼운 번들**이 생성됩니다.

→ 자세한 사항은 [browserslist 공식 문서](https://github.com/browserslist/browserslist) 참고.

<br>

## ✅ private 필드/메서드가 진짜 private 됨

기존에는 클래스의 private 필드와 메서드가 **TypeScript 상에서만 private**이었지만,  
v5부터는 ECMAScript의 **진짜 private(#)** 필드를 사용합니다.

→ 외부에서 접근 불가

<br>

## ✅ cacheTime → gcTime으로 이름 변경

많은 개발자가 `cacheTime`을 **"캐싱되는 시간"**으로 오해했기 때문에  
더 명확한 의미인 `gcTime`(Garbage Collection Time)으로 이름이 바뀌었습니다.

`gcTime`은 쿼리가 더 이상 사용되지 않을 때,  
얼마 뒤에 메모리에서 제거할지를 지정합니다.

### 예시

```tsx
const MINUTE = 1000 * 60;

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      // 변경 전
      // cacheTime: 10 * MINUTE

      // 변경 후
      gcTime: 10 * MINUTE,
    },
  },
});
```

<br>

## ✅ useErrorBoundary → throwOnError로 이름 변경

`useErrorBoundary` 옵션은 다음 이유로 `throwOnError`로 변경되었습니다:

- React 훅은 `use`로 시작하는 네이밍을 가짐 → 혼동 방지
- `ErrorBoundary`와의 혼란을 줄임
- **기능에 더 정확하게 부합하는 이름**

→ 기존 코드의 `useErrorBoundary: true` → `throwOnError: true`로 변경하세요.

<br>

## ✅ 에러 타입 기본값이 `unknown` → `Error`로 변경됨

JavaScript는 `throw`로 어떤 값이든 던질 수 있어서,  
기존에는 에러 타입이 `unknown`이었지만…

대부분의 경우 `Error` 혹은 그 하위 클래스만을 사용하기 때문에  
이제는 기본 타입이 `Error`입니다.

### 예시

```tsx
useQuery<number, string>({
  queryKey: ["some-query"],
  queryFn: async () => {
    if (Math.random() > 0.5) {
      throw "some error";
    }
    return 42;
  },
});
```

⚠️ 문자열이나 다른 타입을 던지고 싶다면,  
**에러 타입을 명시적으로 설정**해야 합니다.

→ 전역 에러 타입 설정 방법은 TypeScript 가이드를 참고하세요.

<br>

## ✅ eslint prefer-query-object-syntax 룰 제거

이제 객체 형태만 지원되므로,  
`prefer-query-object-syntax` 규칙은 필요 없어져 제거되었습니다.

<br>

다음은 `keepPreviousData` → `placeholderData` 변경 관련 내용입니다.

## ✅ `keepPreviousData` 제거 → `placeholderData`로 대체됨

`keepPreviousData` 옵션과 `isPreviousData` 플래그는 제거되었고,  
대신 `placeholderData`와 `isPlaceholderData`로 통합되었습니다.

두 옵션은 실제로 유사한 기능을 했기 때문에,  
이제는 **`placeholderData`에 identity 함수**를 넘기는 방식으로 이전 기능을 그대로 구현할 수 있습니다.

### 예시 1: TanStack에서 제공하는 helper 사용

```tsx
import { useQuery, keepPreviousData } from "@tanstack/react-query";

const {
  data,
  // isPreviousData → 제거됨
  isPlaceholderData,
} = useQuery({
  queryKey,
  queryFn,
  // 변경 전
  // keepPreviousData: true,

  // 변경 후
  placeholderData: keepPreviousData,
});
```

<br>

### 예시 2: identity 함수로 직접 구현

`identity 함수`란 인자를 그대로 반환하는 함수입니다.

```ts
const identity = (value) => value;
```

이 방식은 `keepPreviousData`와 동일한 동작을 하게 합니다.

```tsx
useQuery({
  queryKey,
  queryFn,
  placeholderData: (previousData, previousQuery) => previousData,
});
```

<br>

## ⚠️ 주의할 점들 (차이점 있음!)

1. `placeholderData`는 항상 **성공 상태(success)** 로 간주됩니다.  
   반면, `keepPreviousData`는 이전 쿼리 상태(예: 에러)를 유지할 수 있었습니다.

2. `keepPreviousData`는 이전 쿼리의 `dataUpdatedAt` 값을 유지했지만,  
   `placeholderData`는 `dataUpdatedAt`이 **0으로 초기화**됩니다.

→ UI에서 timestamp가 중요한 경우 아래처럼 `useEffect`로 수동 관리해야 합니다.

### 예시: `dataUpdatedAt` 유지하기

```tsx
const [updatedAt, setUpdatedAt] = useState(0);

const { data, dataUpdatedAt } = useQuery({
  queryKey: ["projects", page],
  queryFn: () => fetchProjects(page),
});

useEffect(() => {
  if (dataUpdatedAt > updatedAt) {
    setUpdatedAt(dataUpdatedAt);
  }
}, [dataUpdatedAt]);
```

<br>

## ✅ Window Focus Refetch: 이제 `focus` 이벤트 대신 `visibilitychange` 사용

브라우저가 포커스를 잃었다가 다시 돌아올 때 쿼리를 리패치하던 동작은  
기존엔 `focus` 이벤트에 의존했지만, 이제는 더 안정적인 `visibilitychange` 이벤트만 사용합니다.

→ 이는 모든 지원 브라우저가 `visibilitychange`를 지원하기 때문에 가능해졌으며,  
관련된 여러 버그들을 해결했습니다.

<br>

## ✅ 네트워크 상태: 이제 `navigator.onLine` 대신 이벤트 기반 처리

`navigator.onLine`은 Chromium 계열 브라우저에서 **신뢰성이 낮은** 것으로 확인되었습니다.  
(예: 실제로 온라인인데도 오프라인 상태로 잘못 판단)

v5부터는 다음과 같이 변경됩니다:

- 기본 상태는 항상 `online: true`
- 브라우저의 `online`, `offline` 이벤트를 통해 상태 변경 감지
- 더 이상 `navigator.onLine`은 사용하지 않음

> 참고: 서비스 워커를 사용하는 앱에서는 가짜 "온라인" 상태가 될 수 있습니다.  
> 예: 실제 인터넷 연결이 없어도 앱이 작동할 수 있음.

<br>

## ✅ custom context prop 제거 → `custom queryClient` 사용

v4에서는 `useQuery`, `useMutation` 등에 `context`를 넘겨  
다른 `queryClient` 인스턴스를 참조할 수 있었습니다.

하지만 이 `context`는 React 전용 기능이며,  
타 프레임워크와의 호환성이 떨어졌습니다.

→ 이제는 **React가 아니어도 사용할 수 있도록**  
context 대신 `queryClient` 인스턴스를 직접 넘겨주는 방식으로 변경되었습니다.

### 변경 예시

```tsx
import { queryClient } from './my-client'

const { data } = useQuery({
queryKey: ['users', id],
queryFn: () => fetch(...),

// 이전 방식
// context: customContext

// 변경된 방식
queryClient,
})
```

<br>

## ✅ Infinite Query: `refetchPage` 제거 → `maxPages` 사용

기존에는 Infinite Query에서 `refetchPage` 옵션을 통해  
리패치할 페이지를 직접 지정할 수 있었습니다.

하지만 이 방식은:

- 일반 쿼리에서는 작동하지 않음
- 리패치 시 UI 불일치 문제 발생

이제는 `maxPages` 옵션을 사용하여 다음을 조절할 수 있습니다:

- 메모리에 저장할 페이지 수
- 리패치 시 다시 불러올 페이지 수

→ `maxPages`는 더 단순하면서도 강력한 옵션입니다.

<br>

## ✅ Dehydrate API 변경

`dehydrate` 함수의 옵션이 간소화되었습니다.

- `dehydrateMutations?: boolean` → 제거됨
- `dehydrateQueries?: boolean` → 제거됨

→ 대신 아래와 같은 **함수 형태의 옵션**으로 대체되었습니다:

- `shouldDehydrateQuery?: (query) => boolean`
- `shouldDehydrateMutation?: (mutation) => boolean`

### 참고: 쿼리/뮤테이션을 전혀 탈수(dehydrate)하지 않으려면

```tsx
dehydrate(client, {
  shouldDehydrateQuery: () => false,
  shouldDehydrateMutation: () => false,
});
```

<br>

## ✅ Infinite Query는 `initialPageParam`이 **필수**입니다

기존에는 쿼리 함수의 `pageParam`에 기본값을 할당할 수 있었지만,  
이 방식은 `undefined`가 queryCache에 저장되어 **직렬화 문제가 생길 수 있었습니다.**

v5부터는 **반드시 `initialPageParam`을 명시적으로 지정해야 합니다.**

### 변경 전

```tsx
useInfiniteQuery({
  queryKey,
  queryFn: ({ pageParam = 0 }) => fetchSomething(pageParam),
  getNextPageParam: (lastPage) => lastPage.next,
});
```

### 변경 후

```tsx
useInfiniteQuery({
  queryKey,
  queryFn: ({ pageParam }) => fetchSomething(pageParam),
  initialPageParam: 0,
  getNextPageParam: (lastPage) => lastPage.next,
});
```

<br>

## ✅ Manual mode for infinite queries 제거됨

이전에는 `fetchNextPage({ pageParam })`처럼  
`pageParam`을 수동으로 전달할 수 있었지만…

- 리패치 시 작동하지 않음
- 사용자가 거의 몰랐음
- 구현상 복잡함

→ 이 기능은 제거되었고, 대신 `getNextPageParam`, `getPreviousPageParam`이 **필수**입니다.

<br>

## ✅ `getNextPageParam`, `getPreviousPageParam`에서 `null` 반환 가능

기존에는 더 이상 불러올 페이지가 없을 때 `undefined`를 반환해야 했지만,  
이제는 `null`도 사용 가능합니다.

```ts
getNextPageParam: () => null;
```

<br>

## ✅ 서버에서의 retry 기본값: 3 → 0

React 18의 suspense 지원으로 인해 서버에서도 쿼리를 실행할 수 있게 되었기 때문에,  
서버 환경에서는 retry를 하지 않도록 기본값이 **0**으로 바뀌었습니다.

> 클라이언트에서는 여전히 기본 3회 재시도합니다.

<br>

## ✅ 상태 관련 네이밍 변경

쿼리와 뮤테이션의 상태 이름이 다음과 같이 바뀌었습니다:

| 변경 전             | 변경 후             |
| ------------------- | ------------------- |
| `status: 'loading'` | `status: 'pending'` |
| `isLoading`         | `isPending`         |
| `isInitialLoading`  | `isLoading`         |

추가로 새롭게 도입된 `isLoading`은 다음과 같이 정의됩니다:

```ts
isLoading = isPending && isFetching;
```

> `isInitialLoading`은 deprecated 되었으며,  
> 다음 major 버전에서 제거될 예정입니다.

<br>

## ✅ `hashQueryKey` → `hashKey`로 이름 변경

이 함수는 쿼리 키뿐 아니라 **mutation 키**도 해시 처리할 수 있기 때문에  
더 일반적인 이름인 `hashKey`로 변경되었습니다.

→ `useIsMutating`, `useMutationState` 등에서도 사용 가능합니다.

<br>

## ✅ React 최소 버전이 18.0으로 상향됨

v5는 `useSyncExternalStore` 훅을 내부적으로 사용하며,  
이는 React 18 이상에서만 지원됩니다.

→ 기존에는 이 훅의 shim(폴리필)을 사용했지만, 이제는 **React 18 필수**

<br>

## ✅ `contextSharing` 옵션 제거됨

기존에는 `QueryClientProvider`에 `contextSharing` 옵션을 주어  
다중 번들/마이크로 프론트엔드 환경에서 **queryClient 인스턴스를 공유**할 수 있었습니다.

그러나 이제는 `context` prop 자체가 제거되었기 때문에  
**공유된 custom queryClient 인스턴스를 직접 전달**해야 합니다.

→ 자세한 내용은 "custom context prop 제거" 항목 참고.

<br>

## ✅ React용 `unstable_batchedUpdates` 더 이상 사용되지 않음

이전에는 React와 React Native에서 `unstable_batchedUpdates`를 batching 함수로 등록했지만,  
React 18부터는 **이 함수는 noop이며 의미 없음**.

→ 이제 자동으로 등록되지 않으며,  
커스텀 프레임워크에서는 직접 batching 함수를 설정해야 함.

### 예시: Solid.js에서 batching 등록

```tsx
import { notifyManager } from "@tanstack/query-core";
import { batch } from "solid-js";

notifyManager.setBatchNotifyFunction(batch);
```

<br>

## ✅ Hydration API 변경

### 주요 변경사항 요약:

1. `Hydrate` 컴포넌트 → `HydrationBoundary`로 이름 변경
2. `useHydrate` 훅 제거됨
3. **Mutation은 자동으로 hydration되지 않음**
4. 이미 캐시에 있는 데이터는 **렌더 이후 effect에서 hydration 처리**

### 예시 변경

```tsx
// 이전
import { Hydrate } from "@tanstack/react-query";

<Hydrate state={dehydratedState}>
  <App />
</Hydrate>;

// 변경 후
import { HydrationBoundary } from "@tanstack/react-query";

<HydrationBoundary state={dehydratedState}>
  <App />
</HydrationBoundary>;
```

> 📌 대부분의 SSR 앱에서는 이 변경이 영향을 주지 않습니다.  
> 다만 React Server Components를 사용할 경우, hydration 타이밍의 차이로  
> **이전 데이터가 잠깐 보였다가 새 데이터로 갱신되는 현상**이 있을 수 있습니다.

<br>

## ✅ Query 기본값 병합 방식 변경

`queryClient.getQueryDefaults()`는 이제  
**모든 일치하는 기본값 등록을 병합(merge)** 하도록 변경되었습니다.

기존에는 첫 번째로 일치하는 것만 반환했지만,  
이제는 가장 일반적인 것부터 구체적인 순서로 병합됩니다.

### ✅ 따라서 기본값 등록 순서를 다음과 같이 해야 합니다:

1. **더 일반적인 키 먼저 등록**
2. **더 구체적인 키를 나중에 등록**

### 예시

```tsx
queryClient.setQueryDefaults(["todo"], {
  retry: false,
  staleTime: 60_000,
});

queryClient.setQueryDefaults(["todo", "detail"], {
  retry: true, // ['todo']에서 상속된 retry: false를 덮어쓰기
  retryDelay: 1_000,
  staleTime: 10_000,
});
```

> ✅ 이전과 동일한 동작을 유지하려면 **구체적인 설정에서 상속된 값을 덮어쓰기** 해야 합니다.

<br>

# 🚀 새로운 기능들

<br>

## ✅ Optimistic Update 간소화됨

v5에서는 `useMutation`의 `variables` 값을 활용해  
더 간단하게 낙관적 업데이트를 구현할 수 있습니다.

### 예시

```tsx
const queryInfo = useTodos();

const addTodoMutation = useMutation({
  mutationFn: (newTodo: string) => axios.post("/api/data", { text: newTodo }),
  onSettled: () => queryClient.invalidateQueries({ queryKey: ["todos"] }),
});

if (queryInfo.data) {
  return (
    <ul>
      {queryInfo.data.items.map((todo) => (
        <li key={todo.id}>{todo.text}</li>
      ))}
      {addTodoMutation.isPending && (
        <li key={String(addTodoMutation.submittedAt)} style={{ opacity: 0.5 }}>
          {addTodoMutation.variables}
        </li>
      )}
    </ul>
  );
}
```

> 캐시를 직접 수정하지 않고, **UI에서만 낙관적으로 렌더링**하는 방식입니다.  
> 단일 컴포넌트에서만 낙관적 UI가 필요한 경우에 적합합니다.

<br>

## ✅ Infinite Query에서 `maxPages`로 페이지 수 제한 가능

무한 스크롤 방식에서 너무 많은 페이지를 불러오면:

- 메모리 낭비
- 느려진 리패치

`maxPages` 옵션을 사용하면:

- 저장할 페이지 수 제한
- 리패치 시 불러올 페이지 제한 가능

> 양방향 스크롤 UX를 위해선 `getNextPageParam`, `getPreviousPageParam` 모두 정의해야 합니다.

<br>

## ✅ Infinite Queries: 여러 페이지 프리패치 가능

기존에는 첫 페이지만 prefetch되었지만,  
이제는 `pages` 옵션을 통해 **여러 페이지 프리패치 가능**합니다.

→ 자세한 내용은 [prefetching 가이드](https://tanstack.com/query/latest/docs/framework/react/guides/prefetching) 참고

<br>

## ✅ `useQueries`에 `combine` 옵션 추가

다수의 쿼리를 조합하여 **단일 결과로 결합**할 수 있습니다.  
자세한 내용은 `useQueries` 문서를 참고하세요.

<br>

## ✅ 실험적: 세분화된 저장소 Persister

`experimental_createPersister`를 통해  
더 세밀한 로컬 저장 전략을 구현할 수 있습니다.

<br>

## ✅ 타입 안전한 Query Options 생성 지원

Query 옵션을 타입 기반으로 안전하게 생성하는 방식이 개선되었습니다.  
자세한 내용은 [TypeScript 문서](https://tanstack.com/query/latest/docs/framework/react/guides/typescript) 참고

<br>

## ✅ Suspense 전용 훅 추가됨 (`useSuspenseQuery` 등)

v5부터 Suspense가 **"안정화"** 되었으며,  
다음과 같은 **전용 훅**이 추가되었습니다:

- `useSuspenseQuery`
- `useSuspenseInfiniteQuery`
- `useSuspenseQueries`

→ 이 훅들을 사용하면 **`data`가 undefined일 수 없음** → 타입 안정성 강화

### 예시

```tsx
const { data: post } = useSuspenseQuery({
  queryKey: ["post", postId],
  queryFn: () => fetchPost(postId),
});
```

> `suspense: true` 옵션은 제거되었으며,  
> 이제는 **전용 훅만 사용**하는 방식입니다.
