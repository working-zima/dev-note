# restoreHandlers()

사용된 일회성 요청 핸들러를 미사용 상태로 표시합니다.

## 호출 시그니처

```typescript
server.restoreHandlers();
```

## 동작 설명

- `server.restoreHandlers()` 메서드는 모든 사용된 **일회성 요청 핸들러**를 미사용 상태로 복원합니다.
- 복원된 요청 핸들러는 다시 네트워크 요청에 영향을 미칩니다.

### 사용 예시

```tsx
import { setupServer } from "msw/node";
import { http, HttpResponse } from "msw";

// 서버 설정
const server = setupServer(
  http.get("/user", () => HttpResponse.json({ firstName: "John" }), {
    once: true, // 일회성 요청 핸들러
  })
);

// 첫 번째 "GET /user" 요청은 가로채져 모킹된 JSON 응답 반환
await fetch("/user"); // 응답: { firstName: 'John' }

// 요청 핸들러는 이미 사용되었으므로 이후 동일 요청은 원래 응답 반환
await fetch("/user"); // 원래 서버 응답

// 사용된 요청 핸들러 복원
server.restoreHandlers();

// 복원된 요청 핸들러가 다시 작동하여 모킹된 응답 반환
await fetch("/user"); // 응답: { firstName: 'John' }
```

## 주요 특징

- **일회성 요청 관리:** `{ once: true }`로 설정된 요청 핸들러가 다시 활성화됩니다.
- **요청 처리 복원:** 네트워크 요청 동작을 원래 상태로 되돌립니다.
