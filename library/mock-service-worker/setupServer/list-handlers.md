# listHandlers()

현재 서버 인스턴스에 등록된 요청 핸들러 목록을 반환합니다.

## 호출 시그니처

```typescript
server.listHandlers();
```

## 동작 설명

- `server.listHandlers()` 메서드는 인자를 받지 않으며, 서버 객체에 등록된 모든 요청 핸들러 목록을 반환합니다.
- 주로 **디버깅** 및 **내부 상태 확인(Introspection)** 목적으로 사용됩니다.

### 핸들러 필터링

특정 유형의 핸들러만 필터링하려면 `instanceof` 연산자를 사용하여 `server.listHandlers()` 결과를 필터링할 수 있습니다.

#### HTTP 핸들러만 로깅하는 예시

```tsx
import { RequestHandler } from "msw";

// HTTP 핸들러 필터링
const handlers = server.listHandlers().filter((handler) => {
  // `http.*` 및 `graphql.*` 핸들러만 포함
  // `ws.*` 핸들러는 제외
  return handler instanceof RequestHandler;
});

console.log(handlers);
```

#### WebSocket 핸들러만 필터링

```tsx
import { WebSocketHandler } from "msw";

// WebSocket 핸들러 필터링
const wsHandlers = server.listHandlers().filter((handler) => {
  return handler instanceof WebSocketHandler;
});

console.log(wsHandlers);
```

## 주요 특징

- **요청 핸들러 상태 확인:** 현재 서버 인스턴스에 등록된 모든 핸들러 확인 가능
- **필터링 지원:** `instanceof` 연산자를 사용하여 특정 핸들러 유형 필터링 가능
