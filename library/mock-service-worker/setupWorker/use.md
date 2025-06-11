# use()

현재 워커 인스턴스에 요청 핸들러를 앞쪽에 추가합니다.

## 호출 시그니처

```tsx
import { http } from "msw";
import { worker } from "./mocks/browser";

// 새 요청 핸들러 목록을 워커 인스턴스에 추가합니다.
// 이 지점 이후 네트워크 동작이 확장됩니다.
worker.use(http.get("/resource"), http.post("/resource"));
```

## 동작 설명

- `worker.start()`와 유사하게 `worker.use()` 메서드에는 요청 핸들러 목록을 스프레드 인자로 전달할 수 있습니다.\
  여러 번 호출할 필요가 없습니다.

- 추가된 요청 핸들러는 현재 런타임이 유지되는 동안 워커 인스턴스에 지속됩니다.\
  따라서 이를 "런타임 요청 핸들러"라고도 합니다.

## 관련 자료

- 런타임 요청 핸들러 (`docs/basics/request-handler#runtime-request-handlers` 섹션)
- 네트워크 동작 재정의
