# `QueryCache`

`QueryCache`는 TanStack Query에서 **쿼리의 데이터, 상태, 메타 정보**를 저장하는 캐시 저장소입니다.  
일반적으로 직접 사용할 일은 드물고, `QueryClient`를 통해 간접적으로 사용하게 됩니다.  
하지만, 고급 기능 구현 시 유용하게 활용될 수 있습니다.

## 기본 생성

```tsx
import { QueryCache } from "@tanstack/react-query";

const queryCache = new QueryCache({
  onError: (error) => console.log("❌ Error:", error),
  onSuccess: (data) => console.log("✅ Success:", data),
  onSettled: (data, error) => console.log("🟡 Settled:", data, error),
});
```

## 옵션

| 옵션        | 설명                                                                       |
| ----------- | -------------------------------------------------------------------------- |
| `onError`   | 쿼리 실행 중 에러 발생 시 호출됩니다. `(error, query)`                     |
| `onSuccess` | 쿼리가 성공적으로 실행될 때 호출됩니다. `(data, query)`                    |
| `onSettled` | 쿼리가 성공 또는 실패 등 모든 결과 이후 호출됩니다. `(data, error, query)` |

## QueryCache 메서드

### `queryCache.find(queryKey)`

> 특정 queryKey를 가진 **하나의 쿼리 인스턴스**를 반환합니다.

```tsx
const query = queryCache.find(["posts"]);
```

- 반환: `Query` 인스턴스 또는 `undefined`
- 고급 기능: 예) `query.state.dataUpdatedAt`을 확인하여 데이터 freshness 판단

### `queryCache.findAll(queryKey?)`

> 특정 queryKey 또는 패턴을 만족하는 **모든 쿼리 인스턴스 배열**을 반환합니다.

```tsx
const queries = queryCache.findAll(["posts"]);
```

- 반환: `Query[]`
- 쿼리가 없다면 빈 배열 `[]` 반환

### `queryCache.subscribe(callback)`

> 쿼리 캐시에 **변화가 생길 때마다 알림을 받을 수 있는 구독 함수**입니다.

```tsx
const callback = (event) => {
  console.log(event.type, event.query);
};

const unsubscribe = queryCache.subscribe(callback);
```

- `event.type`: 이벤트 유형 (`added`, `removed`, `updated` 등)
- 반환값: `unsubscribe()` → 호출 시 구독 해제

### `queryCache.clear()`

> **전체 캐시 초기화**. 모든 쿼리를 제거하고 새롭게 시작할 수 있습니다.

```tsx
queryCache.clear();
```

- 주의: 모든 쿼리 상태 및 데이터가 제거됩니다.

### 활용 상황 예시

| 상황                               | 활용 예시                                         |
| ---------------------------------- | ------------------------------------------------- |
| 쿼리 최신 상태 직접 확인           | `queryCache.find(['posts'])?.state.dataUpdatedAt` |
| 고급 쿼리 추적 및 리스닝           | `queryCache.subscribe()`                          |
| 초기화 버튼 클릭 시 캐시 전체 제거 | `queryCache.clear()`                              |
| 조건부 캐시 검색                   | `queryCache.findAll()`과 `filters` 옵션 사용 가능 |

## QueryCache의 내부 구조 요약

`queryClient.getQueryCache()`를 통해 얻는 `QueryCache`는 내부적으로 `Query` 인스턴스를 여러 개 저장합니다.\
아래는 `QueryCache`와 그 내부에 존재하는 `Query` 객체의 실제 구조를 상세히 정리한 것입니다.

### QueryCache에서 쿼리 가져오기

```ts
const queryCache = queryClient.getQueryCache();
const queries = queryCache.getAll(); // 모든 쿼리 인스턴스 반환

queries.forEach((query) => {
  console.log(query); // query가 내부적으로 가진 구조
});
```

### `Query` 인스턴스의 주요 프로퍼티

| 속성           | 설명                                                     |
| -------------- | -------------------------------------------------------- |
| `queryKey`     | 쿼리의 고유 키 (`['user', 1]` 등)                        |
| `queryHash`    | queryKey를 문자열로 변환한 해시                          |
| `state`        | 쿼리의 상태 (데이터, 에러, status 등)                    |
| `meta`         | 쿼리를 생성할 때 전달한 사용자 정의 메타 정보            |
| `options`      | 쿼리 생성 시 사용한 옵션 전체 (`queryFn`, `retry` 등)    |
| `observers`    | 현재 이 쿼리를 구독 중인 observer 리스트 (`useQuery` 등) |
| `gcTime`       | 쿼리의 가비지 컬렉션 시간                                |
| `isFetching`   | 현재 fetching 중인지 여부                                |
| `fetchFn`      | 쿼리의 fetch 함수                                        |
| `invalidate()` | 쿼리 무효화 트리거                                       |
| `cancel()`     | 요청 취소 함수                                           |
| `destroy()`    | 쿼리 제거 함수                                           |

### `query.state` 내부 구조

```ts
{
  data: any,                        // 실제 쿼리 결과 데이터
  error: unknown | null,           // 에러 정보
  status: 'pending' | 'success' | 'error', // 쿼리 상태
  dataUpdatedAt: number,           // 마지막으로 데이터가 갱신된 시간
  errorUpdatedAt: number,          // 마지막 에러 발생 시간
  fetchFailureCount: number,       // fetch 실패 횟수
  fetchStatus: 'idle' | 'fetching' | 'paused',
  isInvalidated: boolean,          // 무효화 여부
  fetchMeta?: any                  // fetch 관련 메타 정보
}
```

## 요약

| 메서드                            | 설명                               |
| --------------------------------- | ---------------------------------- |
| `find`                            | 특정 queryKey의 쿼리 인스턴스 찾기 |
| `findAll`                         | 여러 쿼리 인스턴스를 배열로 찾기   |
| `subscribe`                       | 쿼리 상태 변경 감지 및 구독 처리   |
| `clear`                           | 전체 캐시 제거                     |
| `onError / onSuccess / onSettled` | 캐시 레벨의 전역 이벤트 리스너     |
