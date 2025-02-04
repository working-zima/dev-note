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

useMutation의 메서드로, 변경 작업 상태를 초기화하는 데 사용됩니다.
위의 예시에서 `reset()`을 사용하지 않는 경우 요청 이후 `"Post was deleted"`라던가 `"Title was updated"` 안내 문구가 다른 Post에서도 보이게 됩니다.\
`reset()`을 사용해 `useMutation`의 작업 상태를 초기화하여 해당 문구가 표시 되지 않게 해줍니다.

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
