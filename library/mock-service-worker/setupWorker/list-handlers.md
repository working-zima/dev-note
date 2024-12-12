# listHandlers()

현재 요청 핸들러 목록을 반환합니다.

## 호출 시그니처

```tsx
worker.listHandlers();
```

## 동작 설명

- `worker.listHandlers()` 메서드는 인자를 받지 않으며, 워커 객체에 등록된 모든 요청 핸들러 목록을 반환합니다.
- 이 메서드는 주로 디버깅 및 내부 상태 확인(Introspection) 목적으로 사용됩니다.

## 핸들러 필터링

특정 유형(예: HTTP 또는 WebSocket 핸들러)만 필터링하려면 `.listHandlers()` 메서드 호출 결과에서 `instanceof` 확인을 수행할 수 있습니다.

다음은 HTTP 핸들러만 로깅하는 예시입니다.

```tsx
import { RequestHandler } from "msw";

const handlers = worker.listHandlers().filter((handler) => {
  // `http.*` 및 `graphql.*` 핸들러만 포함하고,
  // `ws.*` 핸들러는 제외합니다.
  return handler instanceof RequestHandler;
});

console.log(handlers);
```

- `handler instanceof WebSocketHandler`를 사용하면 WebSocket 핸들러만 반환합니다.

## 관련 자료

- `RequestHandler`
