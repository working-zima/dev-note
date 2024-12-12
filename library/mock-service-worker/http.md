# HTTP

HTTP 요청을 가로챕니다.

`http` 네임스페이스는 HTTP 요청을 가로채기 위한 요청 핸들러 생성을 지원합니다. 주로 REST API 작업 시 유용하며, `http.get()` 및 `http.post()`와 같은 메서드를 사용하여 리소스 작업을 정의할 수 있습니다.

## 호출 시그니처

```tsx
http.get<PathParams, RequestBodyType, ResponseBodyType>(
  predicate: string | RegExp,
  resolver: ResponseResolver<
    HttpRequestResolverExtras<Params>,
    RequestBodyType,
    ResponseBodyType
  >,
  options?: RequestHandlerOptions
)
```

## 표준 메서드

`http` 네임스페이스에는 WHATWG Fetch API HTTP 메서드를 나타내는 키가 포함되어 있습니다. 이를 사용하여 특정 메서드의 요청을 캡처할 수 있습니다.

### `http.get()`

```tsx
http.get("/user/:id", ({ params }) => {
  const { id } = params;
  console.log('ID가 "%s"인 사용자를 가져옵니다', id);
});
```

### `http.head()`

```tsx
http.head("/resource", () => {
  return new Response(null, {
    status: 200,
    headers: {
      "Content-Type": "application/json",
      "Content-Length": 1270,
      "Last-Modified": "Mon, 13 Jul 2020 15:00:00 GMT",
    },
  });
});
```

### `http.post()`

```tsx
http.post("/login", async ({ request }) => {
  const info = await request.formData();
  console.log('"%s"로 로그인 중입니다', info.get("username"));
});
```

### `http.put()`

```tsx
http.put("/post/:id", async ({ request, params }) => {
  const { id } = params;
  const nextPost = await request.json();
  console.log('"%s" 게시글을 업데이트 중:', id, nextPost);
});
```

### `http.patch()`

```tsx
http.patch("/cart/:cartId/order/:orderId", async ({ request, params }) => {
  const { cartId, orderId } = params;
  const orderUpdates = await request.json();

  console.log(
    '"%s" 카트의 주문 "%s"을 업데이트 중:',
    cartId,
    orderId,
    orderUpdates
  );
});
```

### `http.delete()`

```tsx
http.delete("/user/:id", ({ params }) => {
  const { id } = params;
  console.log('ID가 "%s"인 사용자를 삭제합니다', id);
});
```

### `http.options()`

```tsx
http.options("https://api.example.com", () => {
  return new Response(null, {
    status: 200,
    headers: {
      Allow: "GET,HEAD,POST",
    },
  });
});
```

## 사용자 정의 메서드

`http` 네임스페이스에는 추가 기능을 제공하는 특수 키가 있으며, 이는 HTTP 메서드와 일치하지 않습니다.

### `http.all()`

주어진 엔드포인트로의 모든 요청을 메서드와 관계없이 가로채는 요청 핸들러를 생성합니다.

```tsx
import { http } from "msw";

export const handlers = [
  // 이 핸들러는 "/user" 엔드포인트의 모든 요청을 캡처합니다: GET, POST, DELETE 등.
  http.all("/user", () => {
    // 요청을 처리합니다.
  }),
];
```

> 사용자 정의 HTTP 메서드를 모킹할 때는 `http.all()`을 사용하는 것이 좋습니다.

## 리졸버 인자

모든 `http.*` 메서드의 응답 리졸버 함수는 다음과 같은 인자 객체 키를 가집니다:

|   이름    |   타입    |        설명        |
| :-------: | :-------: | :----------------: |
| `request` | `Request` |   전체 요청 참조   |
| `params`  | `object`  | 요청 경로 매개변수 |
| `cookies` | `object`  |     요청 쿠키      |

이 인자는 응답 리졸버 함수의 인자 객체에서 접근할 수 있습니다.

```tsx
http.get("/user/:id", ({ request, params, cookies }) => {});
```

## 핸들러 옵션

`http` 네임스페이스의 모든 메서드는 요청 핸들러 옵션을 나타내는 선택적 세 번째 인자를 수락합니다. 지원되는 옵션 속성은 다음과 같습니다.

### `once`

- `boolean`

`true`로 설정하면 첫 번째 성공적인 요청 매치 후 요청 핸들러가 사용된 것으로 표시됩니다. 사용된 요청 핸들러는 이후 트래픽에 대해 효과가 없으며 요청 가로채기 시 무시됩니다.

```tsx
http.get("/greeting", () => HttpResponse.text("안녕하세요"), {
  once: true,
});
```

> `worker`/`server` 인스턴스의 `.restoreHandlers()` 메서드를 사용하여 사용된 요청 핸들러를 다시 미사용 상태로 설정할 수 있습니다.
