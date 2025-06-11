# use()

현재 서버 인스턴스에 요청 핸들러를 앞쪽에 추가합니다.

## 호출 시그니처

```tsx
import { http } from "msw";
import { setupServer } from "msw/node";

const server = setupServer();

// 서버 인스턴스에 요청 핸들러 추가
server.use(http.get("/resource", resolver), http.post("/resource", resolver));
```

## 동작 설명

- `server.use()` 메서드는 서버 인스턴스에 런타임 요청 핸들러를 추가합니다.
- 추가된 요청 핸들러는 현재 Node.js 런타임이 유지되는 동안 지속됩니다.

## 예시

기존 핸들러(`/api/user`의 성공 응답)를 일시적으로 덮어쓰기 해서, 특정 테스트에서만 실패 응답(500)을 주도록 바꿀 수 있습니다.\
테스트 끝나면 `resetHandlers()`로 원래대로 돌려놓을 수 있습니다.

```ts
// 일반 핸들러에서는 200번으로 성공 응답
rest.get("/api/user", (req, res, ctx) => {
  return res(ctx.status(200), ctx.json({ name: "홍길동" }));
});
```

```ts
// setupTests.ts 또는 test-utils.ts
import { server } from "./__mocks__/server";

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());
```

```ts
import { rest } from "msw";
import { server } from "@/__mocks__/server";
import { render, screen } from "@testing-library/react";
import MyComponent from "./MyComponent";

test("유저 API가 실패하면 에러 메시지를 보여준다", async () => {
  // 이 테스트에서만 응답을 바꿔치기
  server.use(
    rest.get("/api/user", (req, res, ctx) => {
      return res(ctx.status(500)); // 강제로 에러 응답
    })
  );

  render(<MyComponent />);

  expect(await screen.findByText("에러 발생")).toBeInTheDocument();
});
```

## 관련 자료

- **런타임 요청 핸들러 (Runtime request handlers)**
- **네트워크 동작 재정의 (Network behavior overrides)**
