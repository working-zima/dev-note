# close()

현재 Node.js 프로세스에서 요청 가로채기를 중지합니다.

## 호출 시그니처

```typescript
server.close();
```

## 동작 설명

- `server.close()` 메서드는 인자를 받지 않으며 값을 반환하지 않습니다.
- Node.js에서 요청 가로채기는 네이티브 클래스 확장을 통해 수행되므로 동기적으로 실행됩니다.

## 사용 예시

테스트 실행이 끝나거나 더 이상 API 모킹 기능이 필요하지 않을 때 `server.close()` 메서드를 호출합니다.

```tsx
import { setupServer } from "msw/node";
import { handlers } from "./handlers";

const server = setupServer(...handlers);

// 요청 가로채기 시작
server.listen();

// 테스트 종료 시 요청 가로채기 중지
afterAll(() => {
  server.close();
});
```

## 관련 자료

- `server.listen()`
