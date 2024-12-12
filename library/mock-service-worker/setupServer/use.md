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

## 관련 자료

- **런타임 요청 핸들러 (Runtime request handlers)**
- **네트워크 동작 재정의 (Network behavior overrides)**
