# Mutation

## useMutation

```tsx
const {
  data,
  error,
  isError,
  isIdle,
  isPending,
  isPaused,
  isSuccess,
  failureCount,
  failureReason,
  mutate,
  mutateAsync,
  reset,
  status,
  submittedAt,
  variables,
} = useMutation(
  {
    mutationFn,
    gcTime,
    meta,
    mutationKey,
    networkMode,
    onError,
    onMutate,
    onSettled,
    onSuccess,
    retry,
    retryDelay,
    scope,
    throwOnError,
  },
  queryClient
);
```

데이터 변경 작업(생성, 수정, 삭제) 등 변경 작업을 처리하기 위해 사용하는 훅입니다.\
'가져오기'에 집중하는 `useQuery`와 다르게 `useMutation`는 '보내기'에 집중합니다.\

`useMutation`은 한 번의 작업이기 때문에 캐시된 데이터가 없습니다.\
따라서 `isLoading`과 `isFetching`은 없고 `isPending`만 있습니다.\
또한 캐시된 것이 없기 때문에 `queryKey`도 필요하지 않습니다.

### mutationFn

서버와의 실제 변경 작업을 수행하는 함수입니다.

```tsx
function App() {
  const updateMutation = useMutation({
    mutationFn: (newTodo) => {
      return axios.post("/todos", newTodo);
    },
  });

  return (
    <div>
      {/* 갱신 요청중 */}
      {updateMutation.isPending ? (
        "Adding todo..."
      ) : (
        <>
          {/* 갱신 요청중 에러 */}
          {updateMutation.isError ? (
            <div>An error occurred: {updateMutation.error.message}</div>
          ) : null}

          {/* 갱신 요청 성공 */}
          {updateMutation.isSuccess ? <div>Todo added!</div> : null}

          <button
            onClick={() => {
              updateMutation.mutate({ id: new Date(), title: "Do Laundry" });
            }}
          >
            Create Todo
          </button>
        </>
      )}
    </div>
  );
}
```

### `.mutate()`

데이터 변경 작업 실행하여 서버와의 상호작용(예: POST, PUT, DELETE)을 트리거합니다.\
호출 시 `isPending`이 `true`로 변경되고, 작업 완료 시 성공(`isSuccess`) 또는 실패(`isError`) 상태로 전환됩니다.

`useQuery`에 인수로 전달하는 쿼리 함수와는 달리, 인수로 전달하는 `mutationFn`는 `.mutate()`을 통해 실제로 인수를 가질 수 있습니다.

```tsx
import {
  useQuery,
  useMutation,
  useQueryClient,
  QueryClient,
  QueryClientProvider,
} from "@tanstack/react-query";
import { getTodos, postTodo } from "../my-api";

function Todos() {
  // Access the client
  const queryClient = useQueryClient();

  // Queries

  // Mutations
  const mutation = useMutation({
    // postTodo는 mutate로 받은 { id: Date.now(), title: "Do Laundry" } 객체를 받음
    mutationFn: postTodo,
    onSuccess: () => {
      // 기존 캐시 데이터를 무효화하여 이후 렌더링에서 새 데이터를 가져옴
      queryClient.invalidateQueries({ queryKey: ["todos"] });
    },
  });

  return (
    <div>
      <ul>
        {query.data?.map((todo) => (
          <li key={todo.id}>{todo.title}</li>
        ))}
      </ul>

      <button
        onClick={() => {
          // mutationFn으로 { id: Date.now(), title: "Do Laundry" } 전달
          mutation.mutate({
            id: Date.now(),
            title: "Do Laundry",
          });
        }}
      >
        Add Todo
      </button>
    </div>
  );
}

render(<App />, document.getElementById("root"));
```

### .reset()

useMutation의 메서드로, 에러나 데이터를 초기화하고 싶을 때는 `reset` 함수를 사용합니다.
`reset()`을 사용해 `useMutation`의 작업 상태를 초기화하여 해당 문구가 표시 되지 않게 해줍니다.

```tsx
const CreateTodo = () => {
  const [title, setTitle] = useState("");
  const mutation = useMutation({ mutationFn: createTodo });

  const onCreateTodo = (e) => {
    e.preventDefault();
    mutation.mutate({ title });
  };

  return (
    <form onSubmit={onCreateTodo}>
      {mutation.error && (
        <h5 onClick={() => mutation.reset()}>{mutation.error}</h5>
      )}
      <input
        type="text"
        value={title}
        onChange={(e) => setTitle(e.target.value)}
      />
      <button type="submit">할 일 생성</button>
    </form>
  );
};
```

## Mutation의 상태

Mutation은 언제든지 다음 중 **한 가지 상태만** 가질 수 있습니다:

- `isIdle` 또는 `status === 'idle'`: 초기 또는 재설정 상태
- `isPending` 또는 `status === 'pending'`: 현재 실행 중
- `isError` 또는 `status === 'error'`: 에러 발생
- `isSuccess` 또는 `status === 'success'`: 성공적으로 완료됨

상태에 따라 아래와 같은 추가 정보를 확인할 수 있습니다:

- `error`: 에러 상태일 경우, 에러 정보 제공
- `data`: 성공 상태일 경우, 응답 데이터 제공

`mutate` 함수에 변수나 객체를 전달하여 mutation 함수에 전달할 수 있습니다.

## onSuccess, invalidateQueries, setQueryData를 활용한 고급 사용법

`onSuccess`, `invalidateQueries`, `setQueryData` 등을 활용하면 매우 강력한 mutation 처리가 가능합니다.

### 무효화 (Invalidation)

이는 화면을 최신 상태로 유지하는 가장 간단한 방법입니다.\
서버 상태와 함께라면, 당신이 표시하는 것은 해당 시점의 데이터의 스냅샷일 뿐임을 기억하세요.\
물론 React Query는 그 데이터를 최신 상태로 유지하려고 합니다. 하지만 변형(`mutation`)을 통해 의도적으로 서버 상태를 변경한다면, 이는 React Query에게 캐시된 일부 데이터가 이제 “무효”하다고 알릴 좋은 시점입니다.\
그러면 React Query는 현재 사용 중인 데이터를 다시 불러오고, 불러오기가 완료되면 화면이 자동으로 업데이트됩니다.\
라이브러리에게는 단지 어떤 쿼리를 무효화하고 싶은지만 알려주면 됩니다.

```tsx
// invalidation-from-mutation

const useAddComment = (id) => {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: (newComment) => axios.post(`/posts/${id}/comments`, newComment),
    onSuccess: () => {
      // ✅ 블로그 포스트의 댓글을 다시 불러옵니다.
      queryClient.invalidateQueries({
        queryKey: ["posts", id, "comments"],
      });
    },
  });
};
```

쿼리 무효화는 꽤 똑똑합니다. 모든 쿼리 필터와 마찬가지로, 쿼리 키에 대해 퍼지 매칭(fuzzy matching)을 사용합니다.\
따라서 여러분이 댓글 목록에 대해 여러 키를 가지고 있다면, 그 모든 것들이 무효화될 것입니다. 하지만, 현재 활성화된 것들만 다시 불러옵니다.\
나머지는 오래되었다고(stale) 표시되며, 다음에 사용될 때 다시 불러오게 됩니다.

예를 들어, 우리가 댓글을 정렬할 수 있는 옵션을 가지고 있고, 새 댓글이 추가된 시점에서 우리의 캐시에는 두 개의 댓글 쿼리가 있다고 가정해 봅시다:

```tsx
['posts', 5, 'comments', { sortBy: ['date', 'asc'] }
['posts', 5, 'comments', { sortBy: ['author', 'desc'] }
```

우리는 이 중 하나만을 화면에 보여주고 있기 때문에, `invalidateQueries`는 해당 쿼리만 다시 불러오고 다른 하나는 오래되었다고 표시할 것입니다.

### 직접 업데이트 (Direct updates)

때로는, 특히 변형(mutation) 이후에 필요한 모든 것이 이미 반환된 경우에는 데이터를 다시 불러오고 싶지 않을 수 있습니다.\
블로그 포스트의 제목을 업데이트하는 변형이 있고 백엔드가 완전한 블로그 포스트를 응답으로 반환하는 경우, setQueryData를 통해 쿼리 캐시를 직접 업데이트할 수 있습니다.

```tsx
// update-from-mutation-response

const useUpdateTitle = (id) => {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: (newTitle) =>
      axios
        .patch(`/posts/${id}`, { title: newTitle })
        .then((response) => response.data),
    // 💡 변형 (mutation)의 응답이 onSucess로 전달됩니다.
    onSuccess: (newPost) => {
      // ✅ 상세보가 화면을 직접 업데이트 합니다.
      queryClient.setQueryData(["posts", id], newPost);
    },
  });
};
```

`setQueryData`를 통해 직접 캐시에 데이터를 넣는 것은 이 데이터가 백엔드에서 반환된 것처럼 작동할 것이며, 그에 따라 해당 쿼리를 사용하는 모든 컴포넌트가 리렌더링될 것임을 의미합니다.

개인적으로, 대부분의 경우에 무효화를 선호해야 한다고 생각합니다.\
물론, 사용 사례에 따라 다르지만, 직접 업데이트가 신뢰성 있게 작동하려면 프론트엔드에 더 많은 코드가 필요하며 어느 정도 백엔드의 로직과 중복된 로직이 필요합니다.\
예를 들어, 정렬된 목록은 직접 업데이트하기가 꽤 어렵습니다.\
업데이트로 인해 내 항목의 위치가 변경되었을 수 있기 때문입니다.\
목록 전체를 무효화하는 것이 “더 안전한” 접근 방법입니다.

### 낙관적 업데이트 (Optimistic updates)

낙관적 업데이트는 React Query 변형(`mutation`)을 사용하는 주요 장점 중 하나입니다.\
useQuery 캐시는 쿼리 간의 전환 시, 특히 데이터 미리 불러오기(`prefetching`)와 같이 사용할 때 데이터를 즉시 제공합니다.\
이 덕분에 전체적인 UI가 매우 빠르게 느껴집니다.\
변형(`mutation`)에도 같은 이점을 얻을 수 있다면 어떨까요?

대부분의 경우, 우리는 업데이트가 성공할 것이라고 확신합니다.\
백엔드로부터 ok를 받고 UI에 결과를 표시하기까지 몇 초를 사용자가 왜 기다려야 할까요?\
낙관적 업데이트의 아이디어는 서버로 전송하기 전에 변형(`mutation`)의 성공을 가정하는 것입니다.\
성공적인 응답을 받게 되면, 우리가 해야 할 일은 화면을 다시 무효화하여 실제 데이터를 보여주는 것입니다.\
요청이 실패할 경우, 우리는 UI를 변형 이전의 상태로 롤백할 것입니다.

이 방법은 즉각적인 사용자 피드백이 필요한 작은 변형(`mutation`)에 대해 훌륭하게 작동합니다.\
요청을 수행하는 토글 버튼이 있고, 요청이 완료될 때까지 전혀 반응하지 않는 것보다 더 나쁜 것은 없습니다.\
사용자들은 그 버튼을 두 번이나 세 번 클릭할 것이고, 모든 곳에서 "느리다고" 느껴질 것입니다.

React Query는 `mutation`이 완료되기 전에 UI를 낙관적으로 업데이트할 수 있는 두 가지 방법을 제공합니다:

1. `onMutate` 옵션을 사용해 **캐시를 직접 수정**
2. `useMutation` 훅에서 반환되는 `variables`를 활용해 **UI에서 직접 수정**

#### 1. UI를 통한 낙관적 업데이트 (Via the UI)

이 방식은 캐시에 직접 접근하지 않고 UI만 수정하는 간단한 방법입니다.

```tsx
const addTodoMutation = useMutation({
  mutationFn: (newTodo: string) => axios.post("/api/data", { text: newTodo }),
  onSettled: () => queryClient.invalidateQueries({ queryKey: ["todos"] }),
});

const { isPending, submittedAt, variables, mutate, isError } = addTodoMutation;
```

##### UI에 변수로 임시 항목 추가

```tsx
<ul>
  {todoQuery.items.map((todo) => (
    <li key={todo.id}>{todo.text}</li>
  ))}
  {isPending && <li style={{ opacity: 0.5 }}>{variables}</li>}
</ul>
```

- `isPending` 상태일 때 `variables` 항목을 리스트에 투명도 낮춰서 보여줍니다.
- mutation 완료 후 리스트가 리페치되며 정상적으로 표시됩니다.
- 에러가 발생하면 항목이 사라지지만, 원한다면 에러 상태에서 계속 보여줄 수도 있습니다

##### 에러 시 재시도 버튼 표시

```tsx
{
  isError && (
    <li style={{ color: "red" }}>
      {variables}
      <button onClick={() => mutate(variables)}>재시도</button>
    </li>
  );
}
```

#### 2. mutation과 query가 서로 다른 컴포넌트에 있을 때

같은 컴포넌트에 둘 수 없다면 `useMutationState` 훅을 통해 전역에서 접근할 수 있습니다.

```tsx
// mutation 정의
const { mutate } = useMutation({
  mutationFn: (newTodo: string) => axios.post("/api/data", { text: newTodo }),
  onSettled: () => queryClient.invalidateQueries({ queryKey: ["todos"] }),
  mutationKey: ["addTodo"],
});

// 다른 컴포넌트에서 해당 상태 조회
const variables = useMutationState<string>({
  filters: { mutationKey: ["addTodo"], status: "pending" },
  select: (mutation) => mutation.state.variables,
});
```

> `variables`는 배열입니다. 동시에 여러 mutation이 실행될 수 있으므로 고유 key가 필요하다면 `submittedAt`을 활용하세요.

#### 3. 캐시를 통한 낙관적 업데이트 (Via the cache)

mutation 전에 상태를 낙관적으로 업데이트할 수 있으며, 실패 시 롤백도 가능합니다.

#### `onMutate`에서 롤백 정보를 반환하고, `onError`에서 복원

##### 할 일 목록에 항목 추가

```tsx
const queryClient = useQueryClient();

useMutation({
  mutationFn: updateTodo,
  onMutate: async (newTodo) => {
    await queryClient.cancelQueries({ queryKey: ["todos"] });
    const previousTodos = queryClient.getQueryData(["todos"]);
    queryClient.setQueryData(["todos"], (old) => [...old, newTodo]);
    return { previousTodos };
  },
  onError: (err, newTodo, context) => {
    queryClient.setQueryData(["todos"], context.previousTodos);
  },
  onSettled: () => queryClient.invalidateQueries({ queryKey: ["todos"] }),
});
```

##### 단일 할 일 항목 수정

```tsx
useMutation({
  mutationFn: updateTodo,
  onMutate: async (newTodo) => {
    await queryClient.cancelQueries({ queryKey: ["todos", newTodo.id] });
    const previousTodo = queryClient.getQueryData(["todos", newTodo.id]);
    queryClient.setQueryData(["todos", newTodo.id], newTodo);
    return { previousTodo, newTodo };
  },
  onError: (err, newTodo, context) => {
    queryClient.setQueryData(
      ["todos", context.newTodo.id],
      context.previousTodo
    );
  },
  onSettled: (newTodo) =>
    queryClient.invalidateQueries({ queryKey: ["todos", newTodo.id] }),
});
```

## `onSettled`에서 롤백 처리도 가능

```tsx
useMutation({
  mutationFn: updateTodo,
  onSettled: async (newTodo, error, variables, context) => {
    if (error) {
      // 에러 처리
    }
  },
});
```

### 언제 어떤 방법을 써야 할까?

- **단일 UI 컴포넌트만 업데이트 필요할 때**  
  → `variables`을 UI에서 직접 사용 (간단하고 코드 적음)

- **여러 UI 컴포넌트가 캐시 데이터를 공유할 때**  
  → `onMutate`로 캐시 수정 (자동 반영 + 롤백 가능)

저는 낙관적 업데이트가 다소 과도하게 사용된다고 생각합니다.\
모든 변형(`mutation`)을 낙관적으로 수행할 필요는 없습니다.\
실패할 확률이 매우 드물다는 것을 정말 확신할 때에만 낙관적 업데이트를 고려해야 합니다.\
왜냐하면 롤백에 대한 UX는 그리 좋지 않기 때문입니다.\
예를 들면 제출할 때 닫히는 모달 팝업 내부의 폼이나, 업데이트 후 상세 화면에서 리스트 화면으로의 리다이렉션이 있습니다.\
이러한 동작들이 성급하게 수행되면, 다시 되돌리기가 어렵습니다.

또한, 즉각적인 피드백이 정말 필요한 것인지를 꼭 확인하세요 (위의 토글 버튼 예제처럼).\
낙관적 업데이트를 작동시키는 데 필요한 코드는 간단하지 않으며, "표준적인" 변형(`mutation`)과 비교하면 특히 더 그렇습니다.\
결과를 가정해서 보여주려면 백엔드가 하는 일을 모방해야 하며, 이는 `Boolean`을 뒤집거나 배열에 항목을 추가하는 것처럼 쉬울 수 있지만, 빠른 속도로 복잡해질 수도 있습니다:

추가하는 할 일에 `id`가 필요하다면, 어디서 가져오시겠어요?\
현재 보고 있는 리스트가 정렬되어 있다면, 새로운 항목을 올바른 위치에 삽입하시나요?\
다른 사용자가 그 사이에 다른 것을 추가했다면, 낙관적으로 추가된 항목의 위치가 재요청 후에 바뀌나요?\
이러한 모든 경우의 수는 일부 상황에서 UX를 실제로 더 나쁘게 만들 수 있으며, 오히려 변형(`mutation`)이 진행 중일 때 버튼을 비활성화하고 로딩 애니메이션을 출력하는 것만으로 충분할 수 있습니다.\
언제나 그렇듯, 올바른 도구를 올바른 작업에 사용하세요.

## Mutation의 라이프사이클 훅

`useMutation`에는 라이프사이클별 훅이 존재하여 다양한 시점에 부수 효과를 삽입할 수 있습니다:

```tsx
useMutation({
  mutationFn: addTodo,
  onMutate: (variables) => {
    // mutation 시작 전 호출
    return { id: 1 }; // 예: 낙관적 업데이트용 데이터 전달
  },
  onError: (error, variables, context) => {
    // 에러 발생 시
    console.log(`롤백: ID ${context.id}`);
  },
  onSuccess: (data, variables, context) => {
    // 성공 시
  },
  onSettled: (data, error, variables, context) => {
    // 성공/실패와 무관하게 항상 호출됨
  },
});
```

### 비동기 콜백 체이닝

```tsx
useMutation({
  mutationFn: addTodo,
  onSuccess: async () => {
    console.log("먼저 실행");
  },
  onSettled: async () => {
    console.log("그다음 실행");
  },
});
```

### mutate 시점에 추가 콜백 지정

```tsx
const mutation = useMutation({
  mutationFn: addTodo,
  onSuccess: () => {
    console.log("전역 성공 콜백");
  },
});

mutation.mutate(todo, {
  onSuccess: () => {
    console.log("개별 성공 콜백");
  },
});
```

## 연속적인 Mutation 처리 시 주의점

`useMutation`에 지정한 콜백은 `mutate()` 호출마다 실행되지만, `mutate()`에 개별로 지정한 콜백은 **마지막 mutation**에만 실행됩니다.

```tsx
useMutation({
  mutationFn: addTodo,
  onSuccess: () => {
    // 3번 모두 실행됨
  },
});

const todos = ["1", "2", "3"];
todos.forEach((todo) =>
  mutate(todo, {
    onSuccess: () => {
      // 마지막 todo에 대해서만 실행됨
    },
  })
);
```

## mutateAsync로 프로미스 사용하기

```tsx
const mutation = useMutation({ mutationFn: addTodo });

try {
  const todo = await mutation.mutateAsync(todo);
  console.log(todo);
} catch (error) {
  console.error(error);
} finally {
  console.log("완료");
}
```

## 재시도 (Retry)

기본적으로 mutation은 실패 시 재시도하지 않지만, `retry` 옵션을 통해 설정할 수 있습니다:

```tsx
const mutation = useMutation({
  mutationFn: addTodo,
  retry: 3,
});
```

## 오프라인 상태에서의 처리

디바이스가 오프라인인 경우, 연결이 복원되면 mutation은 동일한 순서로 재시도됩니다.

## Mutation 저장 및 복구 (Persist Mutations)

mutation을 저장소에 저장하고 나중에 복원하려면 아래와 같이 합니다:

```tsx
queryClient.setMutationDefaults(["addTodo"], {
  mutationFn: addTodo,
  onMutate: async (variables) => {
    await queryClient.cancelQueries({ queryKey: ["todos"] });
    const optimisticTodo = { id: uuid(), title: variables.title };
    queryClient.setQueryData(["todos"], (old) => [...old, optimisticTodo]);
    return { optimisticTodo };
  },
  onSuccess: (result, variables, context) => {
    queryClient.setQueryData(["todos"], (old) =>
      old.map((todo) => (todo.id === context.optimisticTodo.id ? result : todo))
    );
  },
  onError: (error, variables, context) => {
    queryClient.setQueryData(["todos"], (old) =>
      old.filter((todo) => todo.id !== context.optimisticTodo.id)
    );
  },
  retry: 3,
});

const mutation = useMutation({ mutationKey: ["addTodo"] });
mutation.mutate({ title: "제목" });

const state = dehydrate(queryClient);
hydrate(queryClient, state);
queryClient.resumePausedMutations();
```

### 재시작 시 mutationFn 없으면 복구 불가

함수를 직렬화할 수 없기 때문에 저장소에 저장되는 건 mutation 상태뿐입니다. 따라서 재시작 후 mutation을 복구하려면 기본 mutation 함수가 필요합니다.

```tsx
queryClient.setMutationDefaults(["todos"], {
  mutationFn: ({ id, data }) => {
    return api.updateTodo(id, data);
  },
});
```

## Mutation Scope

기본적으로 모든 mutation은 병렬로 실행됩니다. 하지만 `scope.id`를 지정하면 **같은 scope 내의 mutation은 순차적으로 실행**됩니다.

```tsx
const mutation = useMutation({
  mutationFn: addTodo,
  scope: {
    id: "todo",
  },
});
```

## useMutationState

현재 실행 중인 `useMutation`의 상태를 추적하는 훅입니다.\
특정 뮤테이션의 진행 상태, 요청 데이터, 응답 데이터 등을 가져올 수 있는 기능을 제공합니다.

```tsx
// 특정 mutationKey를 가진 뮤테이션이 실행 중인지 확인하고, 해당 뮤테이션의 요청 데이터를 가져오는 역할
const mutationState = useMutationState({
  filters: { mutationKey: ["update-user"], status: "pending" }, // 현재 진행 중인 뮤테이션만 가져오기
  select: (mutation) => mutation.state.variables, // 요청 데이터 가져오기
});

const pendingUser = pendingData ? pendingData[0] : null;

// 서버 응답을 기다리는 동안 미리 변경될 데이터(pendingUser) 사용
<Heading>
  Information for {pendingUser ? pendingUser.name : user?.name}
</Heading>;
```

### 직접 UI를 바꾸지 않고, useMutationState을 사용하는 이유

직접 바꿀경우 요청이 실패하면 UI를 다시 원래대로 돌려야 하는 rollback 로직이 필요합니다.\
`useMutationState`를 사용하면 Tanstack Query 내부 상태와 자동으로 동기화됩니다.\
따라서 자동으로 내부 상태에 따라 `pendingData`가 사라지므로 rollback이 필요 없습니다.

## 사용 예시 1

`QueryClientProvider`를 사용하여 `QueryClient`를 제공합니다.

```jsx
// App.jsx

import { QueryClient, QueryClientProvider } from "@tanstack/react-query";
import { ReactQueryDevtools } from "@tanstack/react-query-devtools";

import { Posts } from "./Posts";

import "./App.css";

const queryClient = new QueryClient();

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <div className="App">
        <Posts />
      </div>
      <ReactQueryDevtools />
    </QueryClientProvider>
  );
}

export default App;
```

페이지네이션을 통해 페이지에 맞는 데이터를 가져옵니다.\
다음 페이지는 `prefetchQuery`를 통해 미리 캐시에 가져옵니다.

```jsx
// Posts.jsx

import { useEffect, useState } from "react";

import { useMutation, useQuery, useQueryClient } from "@tanstack/react-query";

import { fetchPosts, deletePost, updatePost } from "./api";

import { PostDetail } from "./PostDetail";

const maxPostPage = 10;

export function Posts() {
  const [currentPage, setCurrentPage] = useState(1);
  const [selectedPost, setSelectedPost] = useState(null);

  const queryClient = useQueryClient();

  // useQuery는 '가져오기'에 집중
  const { data, isError, error, isLoading } = useQuery({
    queryKey: ["posts", currentPage],
    queryFn: () => fetchPosts(currentPage),
    staleTime: 2000,
  });

  // 데이터 변경 작업(생성, 수정, 삭제 등)을 위한 훅
  // useMutation는 '보내기'에 집중
  const deleteMutation = useMutation({
    mutationFn: (postId) => deletePost(postId),
  });
  const updateMutation = useMutation({
    mutationFn: (postId) => updatePost(postId),
  });

  useEffect(() => {
    if (currentPage < maxPostPage) {
      const nextPage = currentPage + 1;
      // 다음 페이지에 해당하는 데이터를 미리 가져와서 로딩시간 동안 보여줌
      queryClient.prefetchQuery({
        queryKey: ["posts", nextPage],
        queryFn: () => fetchPosts(nextPage),
      });
    }
  }, [currentPage, queryClient]);

  // prefetchQuery를 통해 다음에 보여줄 데이터가 캐싱되어 있기 때문에 isLoading을 사용하여 첫 데이터를 가져올 때만 로딩 표시
  if (isLoading) return <h3>Loading...</h3>;
  if (isError) {
    return (
      <>
        <h3>Something went wrong...</h3>
        <p>{error.toString()}</p>
      </>
    );
  }

  return (
    <>
      <ul>
        {/* Post 목록 */}
        {data.map((post) => (
          <li
            key={post.id}
            className="post-title"
            onClick={() => {
              // updateMutation 또는 deleteMutation을 사용 후 다른 <li>를 클릭했을 때 수정시 사용한 is~ 상태를 초기로 돌림
              // 만약 reset()을 하지 않으면 mutation 사용 후 다른 <li>를 클릭했을 때 Post was deleted 등의 메시지가 사라지지 않음
              deleteMutation.reset();
              updateMutation.reset();
              setSelectedPost(post);
            }}
          >
            {post.title}
          </li>
        ))}
      </ul>

      {/* 페이지 전환 버튼 섹션 */}
      <div className="pages">
        {/* 이전 페이지 버튼 */}
        <button
          disabled={currentPage <= 1}
          onClick={() => {
            setCurrentPage((prevValue) => prevValue - 1);
          }}
        >
          Previous page
        </button>
        {/* 현재 페이지 */}
        <span>Page {currentPage}</span>
        {/* 다음 페이지 버튼 */}
        <button
          disabled={currentPage >= maxPostPage}
          onClick={() => {
            setCurrentPage((prevValue) => prevValue + 1);
          }}
        >
          Next page
        </button>
      </div>

      <hr />

      {/* 선택된 Post의 상세 정보 표시 */}
      {selectedPost && (
        <PostDetail
          post={selectedPost}
          deleteMutation={deleteMutation}
          updateMutation={updateMutation}
        />
      )}
    </>
  );
}
```

```jsx
// PostDetails.jsx

import { useQuery } from "@tanstack/react-query";

import { fetchComments } from "./api";

import "./PostDetail.css";

export function PostDetail({ post, deleteMutation, updateMutation }) {
  const { data, isError, error, isLoading } = useQuery({
    // queryKey에 post.id가 없다면 fetchComments(post.id)의 post.id가 바뀌어도 comment가 바뀌지 않음
    queryKey: ["comments", post.id],
    queryFn: () => fetchComments(post.id),
  });

  if (isLoading) return <h3>Loading...</h3>;
  if (isError) {
    return (
      <>
        <h3>Something went wrong...</h3>
        <p>{error.toString()}</p>
      </>
    );
  }

  return (
    <>
      <h3 style={{ color: "blue" }}>{post.title}</h3>
      {/* 삭제 버튼 */}
      <div>
        {/* Post.jsx의 deleteMutation을 통해 해당 post.id 게시물 삭제 요청 */}
        <button onClick={() => deleteMutation.mutate(post.id)}>Delete</button>
      </div>

      {/* 삭제 요청이 pending 상태일 경우 안내 */}
      {deleteMutation.isPending && <p className="loading">Deleting the post</p>}

      {/* 삭제 요청이 error 일 경우 안내 */}
      {deleteMutation.isError && (
        <p className="error">
          Error deleting the post: {deleteMutation.error.toString()}
        </p>
      )}

      {/* 삭제 요청이 완료되었을 경우 */}
      {deleteMutation.isSuccess && <p className="success">Post was deleted</p>}

      {/* 갱신 버튼 */}
      <div>
        {/* Post.jsx의 updateMutation을 통해 해당 post.id 게시물 갱신 요청 */}
        <button onClick={() => updateMutation.mutate(post.id)}>
          Update title
        </button>
      </div>

      {/* 갱신 요청이 pending 상태일 경우 안내 */}
      {updateMutation.isPending && <p className="loading">Updating the post</p>}

      {/* 갱신 요청이 error 일 경우 안내 */}
      {updateMutation.isError && (
        <p className="error">
          Error updating the post: {updateMutation.error.toString()}
        </p>
      )}

      {/* 갱신 요청이 완료되었을 경우 */}
      {updateMutation.isSuccess && <p className="success">Title was updated</p>}

      {/* 선택된 Post 본문 */}
      <p>{post.body}</p>
      <h4>Comments</h4>

      {/* 선택 된 Post에 달린 댓글 */}
      {data.map((comment) => (
        <li key={comment.id}>
          {comment.email}: {comment.body}
        </li>
      ))}
    </>
  );
}
```

## 자료

- [TanStack Query v5](https://tanstack.com/query/latest)
- [React Query / TanStack Query : React로 서버 상태 관리하기](https://www.udemy.com/course/react-query-react/?couponCode=BFCPSALE24&utm_source=adwords&utm_medium=udemyads&utm_campaign=Webindex_Catchall_la.KR_cc.KR&campaigntype=Search&portfolio=SouthKorea&language=KR&product=Course&test=&audience=DSA&topic=&priority=&utm_content=deal4584&utm_term=_._ag_154831691911_._ad_667917181863_._kw__._de_c_._dm__._pl__._ti_dsa-1456167871416_._li_9211460_._pd__._&matchtype=&gad_source=1&gclid=CjwKCAiAjKu6BhAMEiwAx4UsAtEeV5vH-JflZbvwTHBc43TAyLJbxP0YIKR7ww3-7ux0GKGKi929kRoCbvMQAvD_BwE)
- [TanStack Query(React Query) 핵심 정리](https://www.heropy.dev/p/HZaKIE)
- [react-query-[useQuery & isLoading VS isFetching]](https://velog.io/@rlwjd31/react-query-useQuery-isLoading-VS-isFetching)
- [(번역) #12: Mastering Mutations in React Query](https://highjoon-dev.vercel.app/blogs/12-mastering-mutations-in-react-query)
