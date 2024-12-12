# setupWorker

브라우저에서 요청 가로채기를 구성합니다.

`setupWorker()` 함수는 브라우저에서 API 모킹을 활성화하기 위해 클라이언트-워커 통신 채널을 설정합니다.

## 호출 시그니처

`setupWorker()` 함수는 선택적 요청 핸들러 목록을 인자로 받아 워커 인스턴스를 반환합니다.

인자를 전달하지 않고 호출하면 네트워크 설명이 없는 워커 인스턴스(즉, 요청 핸들러가 없음)가 반환됩니다. `worker.use()` 및 `worker.resetHandlers()`와 같은 메서드를 사용하여 런타임 시 요청 핸들러를 추가 및 제거할 수 있습니다.

```tsx
import { setupWorker } from "msw/browser";

const worker = setupWorker();
// worker.start()
// worker.use(...handlers)
```

그러나 `setupWorker()` 함수에 요청 핸들러를 스프레드 인자로 전달하면 초기 핸들러로 간주되며 핸들러 재설정 시에도 워커 인스턴스에서 항상 유지됩니다.

```tsx
import { http, HttpResponse } from "msw";
import { setupWorker } from "msw/browser";

const worker = setupWorker(
  http.get("/resource", () => HttpResponse.json({ id: "abc-123" }))
);
```

## 워커 인스턴스

`setupWorker()` 함수는 브라우저 환경에서 API 모킹을 제어할 수 있는 워커 인스턴스를 반환합니다. 아래는 워커 인스턴스에서 사용할 수 있는 메서드 목록입니다.

### 메서드

- **`start(options)`**: 요청 가로채기를 시작합니다.
- **`stop()`**: 요청 가로채기를 중지합니다.
- **`use(...handlers)`**: 런타임 요청 핸들러를 추가합니다.
- **`resetHandlers(...handlers)`**: 요청 핸들러를 재설정합니다.
- **`restoreHandlers()`**: 초기 요청 핸들러 상태로 복원합니다.
- **`printHandlers()`**: 등록된 요청 핸들러 목록을 출력합니다.
