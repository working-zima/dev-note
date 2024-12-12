# boundary()

지정된 범위에서 네트워크 가로채기를 격리합니다.

## 호출 시그니처

```typescript
function boundary<Callback extends (...args: Array<unknown>) => unknown>(
  callback: Callback
): (...args: Parameters<Callback>) => ReturnType<Callback>;
```

## 사용법

### 기본 사용 예시

`server.boundary()`는 Node.js 전용 API로, 브라우저에서는 사용할 수 없습니다. 지정된 범위 내에서만 요청 가로채기 동작이 영향을 미치도록 설정할 수 있습니다.

```tsx
import { HttpResponse } from "msw";
import { setupServer } from "msw/node";

const server = setupServer();
server.listen();

function app() {
  fetch("https://example.com");

  server.boundary(() => {
    server.use(
      http.get("https://example.com", () => {
        return HttpResponse.error();
      })
    );
    fetch("https://example.com"); // 네트워크 오류 발생
  })();
}
```

## 네트워크 상태 관리

- **범위 격리:** 범위 내에서 추가된 요청 핸들러는 해당 범위에서만 적용되며 글로벌 네트워크 상태에는 영향을 주지 않습니다.
- **초기 핸들러 상속:** `setupServer()`로 정의된 초기 요청 핸들러는 모든 범위의 기본 상태로 유지됩니다.

### 요청 핸들러 상태 재정의 예시

```tsx
const server = setupServer(
  http.get("https://example.com/user", () => {
    return HttpResponse.json({ name: "John" });
  })
);

server.boundary(() => {
  server.use(
    http.post("https://example.com/login", () => {
      return new HttpResponse(null, { status: 500 });
    })
  );

  server.boundary(() => {
    server.use(
      http.delete("https://example.com/post", () => {
        return new HttpResponse(null, { status: 404 });
      })
    );

    // 현재 범위의 요청 핸들러 재설정
    server.resetHandlers(); // POST /login은 유지됨
  })();
})();
```

## 병렬 테스트 실행

`server.boundary()`는 병렬 테스트 실행 시 예측 가능한 네트워크 동작을 보장합니다.

```tsx
import { http, HttpResponse } from "msw";
import { setupServer } from "msw/node";

const server = setupServer(
  http.get("https://example.com/user", () => {
    return HttpResponse.json({ name: "John" });
  })
);

beforeAll(() => {
  server.listen();
});

afterAll(() => {
  server.close();
});

it.concurrent(
  "fetches the user",
  server.boundary(async () => {
    const response = await fetch("https://example.com/user");
    const user = await response.json();
    expect(user).toEqual({ name: "John" });
  })
);

it.concurrent(
  "handles the server error",
  server.boundary(async () => {
    server.use(
      http.get("https://example.com/user", () => {
        return new HttpResponse(null, { status: 500 });
      })
    );
    const response = await fetch("https://example.com/user");
    expect(response.status).toBe(500);
  })
);

it.concurrent(
  "handles network errors",
  server.boundary(async () => {
    server.use(
      http.get("https://example.com/user", () => {
        return HttpResponse.error();
      })
    );
    await expect(fetch("https://example.com/user")).rejects.toThrow(
      "Failed to fetch"
    );
  })
);
```

## 주요 특징

- **범위 격리:** 특정 범위에서만 요청 핸들러 재정의가 적용됩니다.
- **초기 핸들러 상속:** 상위 범위의 핸들러가 하위 범위에 상속됩니다.
- **병렬 테스트 지원:** 테스트 격리로 안정적인 병렬 테스트 실행을 지원합니다.
