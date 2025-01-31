# API Reference

## useIsFetching

`useIsFetching`은 선택적인 훅입니다.\
애플리케이션에서 백그라운드에서 loading 또는 fetching 중인 쿼리의 개수를 반환합니다.\
(앱 전역 로딩 인디케이터에 유용하게 사용할 수 있음)

### useIsFetching 사용법

```tsx
import { useIsFetching } from "@tanstack/react-query";

// 현재 백그라운드에서 가져오고 있는 쿼리의 개수 (0이면 없음)
const isFetching = useIsFetching();

// 특정 queryKey를 가진 쿼리 중 현재 가져오고 있는 개수
const isFetchingPosts = useIsFetching({ queryKey: ["posts"] });
```

### useIsFetching 옵션 (Options)

- `filters?: QueryFilters`: 특정 쿼리를 필터링하여 확인할 수 있습니다.

  ```tsx
  // queryKey가 'users'이고 현재 fetching 상태인 쿼리 개수 반환
  // exact: false(기본값)라면 queryKey가 ['users', 'profile'] 같은 경우도 포함
  const isFetchingUsers = useIsFetching({
    queryKey: ["users"],
    fetchStatus: "fetching",
    exact: true,
  });
  ```

- `queryClient?: QueryClient`: 사용자 지정 QueryClient를 사용할 수 있으며, 지정하지 않으면 가장 가까운 `QueryClient` 컨텍스트를 사용합니다.

  ```tsx
  // 기본 QueryClient가 아닌 특정 QueryClient 사용
  import {
    useQueryClient,
    useIsFetching,
    QueryClient,
  } from "@tanstack/react-query";
  const customQueryClient = new QueryClient();
  const isFetchingCustom = useIsFetching({}, customQueryClient);
  ```

### useIsFetching 반환값 (Returns)

- `isFetching: number`
  현재 애플리케이션에서 로딩 중이거나 가져오고 있는 쿼리의 개수를 반환합니다.

## QueryCache (쿼리 캐시)

`QueryCache`는 TanStack Query의 저장 메커니즘으로, 포함된 모든 쿼리의 데이터, 메타 정보 및 상태를 저장하는 역할을 합니다.

일반적으로 `QueryCache`를 직접 다룰 필요는 없으며, 특정 캐시를 다룰 때는 `QueryClient`를 사용하는 것이 일반적입니다.

### 사용 예시

```tsx
import { QueryCache } from "@tanstack/react-query";

const queryCache = new QueryCache({
  onError: (error) => {
    console.log(error);
  },
  onSuccess: (data) => {
    console.log(data);
  },
  onSettled: (data, error) => {
    console.log(data, error);
  },
});

// 특정 queryKey를 가진 쿼리 찾기
const query = queryCache.find(["posts"]);
```

### 사용 가능한 메서드

- **`find`**
- **`findAll`**
- **`subscribe`**
- **`clear`**

### QueryCache 옵션 (Options)

- **`onError?: (error: unknown, query: Query) => void`**

  - 선택 사항
  - 쿼리가 오류를 만나면 호출되는 함수

- **`onSuccess?: (data: unknown, query: Query) => void`**

  - 선택 사항
  - 쿼리가 성공하면 호출되는 함수

- **`onSettled?: (data: unknown | undefined, error: unknown | null, query: Query) => void`**
  - 선택 사항
  - 쿼리가 완료(성공 또는 실패)되면 호출되는 함수

### `queryCache.find`

`find` 메서드는 캐시에 존재하는 특정 쿼리 인스턴스를 가져오는 동기적인 메서드입니다.
이 인스턴스에는 해당 쿼리의 상태뿐만 아니라 모든 내부 정보가 포함됩니다.
만약 해당하는 쿼리가 없다면 `undefined`를 반환합니다.

**일반적으로 필요하지 않지만, 특정한 상황에서 유용할 수 있음**
(예: `query.state.dataUpdatedAt`을 확인하여 쿼리가 충분히 최신 상태인지 판단)

```tsx
const query = queryCache.find(queryKey);
```

#### `queryCache.find` 옵션

- **`filters?: QueryFilters`**: 특정 조건을 가진 쿼리를 필터링

#### `queryCache.find` 반환값

- **`Query`**
  - 캐시에서 찾은 쿼리 인스턴스

### `queryCache.findAll`

`findAll`은 `find`보다 더 발전된 메서드로, 특정 `queryKey`와 부분적으로 일치하는 여러 개의 쿼리 인스턴스를 가져올 수 있습니다.
만약 해당하는 쿼리가 없다면 빈 배열(`[]`)을 반환합니다.

📌 **일반적으로 필요하지 않지만, 특정한 상황에서 유용할 수 있음**

```tsx
const queries = queryCache.findAll(queryKey);
```

#### `queryCache.findAll` 옵션

- **`queryKey?: QueryKey`**: 쿼리 키
- **`filters?: QueryFilters`**: 특정 조건을 가진 쿼리 필터링

#### `queryCache.findAll` 반환값

- **`Query[]`**
  - 캐시에서 찾은 여러 개의 쿼리 인스턴스 배열

### `queryCache.subscribe`

`subscribe` 메서드는 쿼리 캐시에 대한 구독을 생성하여,
쿼리 상태 변경, 쿼리 추가, 삭제 등의 이벤트를 감지할 수 있습니다.

```tsx
const callback = (event) => {
  console.log(event.type, event.query);
};

const unsubscribe = queryCache.subscribe(callback);
```

#### `queryCache.subscribe` 옵션

- **`callback: (event: QueryCacheNotifyEvent) => void`**
  - 캐시가 변경될 때마다 호출되는 콜백 함수
  - 내부적으로 `query.setState`, `queryClient.removeQueries` 등의 메서드가 실행될 때만 이벤트가 발생함
  - 직접 캐시를 조작하는 것은 권장되지 않으며, 이에 따른 이벤트는 발생하지 않음

#### `queryCache.subscribe` 반환값

- **`unsubscribe: Function => void`**
  - 해당 콜백을 쿼리 캐시 구독에서 해제하는 함수

### `queryCache.clear`

`clear` 메서드는 캐시를 완전히 비우고 초기화할 때 사용됩니다.

```tsx
queryCache.clear();
```
