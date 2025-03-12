# Placeholder Query Data

<br>

## Placeholder Data란?

`placeholderData`는 쿼리가 마치 이미 데이터를 가지고 있는 것처럼 작동하게 만들어 주는 기능입니다.  
이는 `initialData`와 비슷하지만, **캐시에 저장되지 않는다는 점**이 다릅니다.

> ✔️ 즉, **임시/가짜 데이터로 UI를 먼저 보여준 후**,  
> 실제 데이터를 백그라운드에서 패치합니다.

<br>

### 예시: 블로그 포스트 프리뷰

- 리스트 페이지에서 게시글 제목과 일부 본문 스니펫만 제공
- 상세 페이지에서는 전체 게시글 데이터를 패치
- 리스트에서 일부 데이터를 **미리 보여주되**, 해당 데이터는 캐시에 영구 저장되면 안 됨
- 하지만 UI 렌더링에는 충분히 유용함

<br>

## placeholderData를 제공하는 방식

**1. 선언적으로 (Declaratively)**  
→ `useQuery`의 `placeholderData` 옵션을 이용해 초기 데이터 제공

**2. 명령적으로 (Imperatively)**  
→ `queryClient.fetchQuery()` 혹은 `prefetchQuery()`와 함께 `placeholderData` 사용

<br>

## placeholderData가 적용된 쿼리는?

- `pending` 상태가 **아닙니다**
- 시작부터 `success` 상태입니다 (왜냐하면 보여줄 데이터가 있으니까요)
- 대신 `isPlaceholderData`가 **true**로 설정됩니다 → 실데이터 아님을 구분

<br>

## Placeholder Data를 값으로 직접 전달

```tsx
function Todos() {
  const result = useQuery({
    queryKey: ["todos"],
    queryFn: () => fetch("/todos"),
    placeholderData: placeholderTodos,
  });
}
```

<br>

## Placeholder Data를 useMemo로 메모이제이션

```tsx
function Todos() {
  const placeholderData = useMemo(() => generateFakeTodos(), []);
  const result = useQuery({
    queryKey: ["todos"],
    queryFn: () => fetch("/todos"),
    placeholderData,
  });
}
```

<br>

## Placeholder Data를 함수로 제공

`placeholderData`는 함수도 받을 수 있습니다.  
이 함수는 **이전 쿼리의 데이터와 메타 정보를 인자로 받아**  
다음 쿼리의 플레이스홀더로 사용할 수 있습니다.

→ 예: `['todos', 1]` → `['todos', 2]`로 쿼리 키가 바뀌는 동안  
이전 데이터를 보여주고 로딩 스피너를 피할 수 있음

```tsx
const result = useQuery({
  queryKey: ["todos", id],
  queryFn: () => fetch(`/todos/${id}`),
  placeholderData: (previousData, previousQuery) => previousData,
});
```

<br>

## Placeholder Data를 캐시에서 가져오기

다른 쿼리의 캐시 데이터를 활용해 placeholder를 제공할 수도 있습니다.

예: 블로그 게시글 리스트 쿼리에서 프리뷰 데이터를 꺼내  
해당 게시글 상세 쿼리의 placeholder로 사용

```tsx
function Todo({ blogPostId }) {
  const queryClient = useQueryClient();
  const result = useQuery({
    queryKey: ["blogPost", blogPostId],
    queryFn: () => fetch(`/blogPosts/${blogPostId}`),
    placeholderData: () => {
      return queryClient
        .getQueryData(["blogPosts"])
        ?.find((d) => d.id === blogPostId);
    },
  });
}
```

<br>

## 🔎 더 읽을거리

`placeholderData` vs `initialData` 비교는  
커뮤니티 자료(Community Resources)에서 확인해보세요.
