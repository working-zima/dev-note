# stop()

현재 클라이언트에 대한 요청 가로채기를 중지합니다.

## 호출 시그니처

`worker.stop()` 함수는 인자를 받지 않으며 값을 반환하지 않습니다.

```tsx
worker.stop();
```

## 동작 설명

`worker.stop()` 메서드는 논리적으로 `worker.start()`의 반대이지만, 워커를 등록 해제하지 않습니다. 대신 현재 클라이언트(페이지)에 대해 API 모킹을 비활성화하라는 지시를 내립니다. 이를 통해 여러 클라이언트를 열어 각기 다른 요청 가로채기 상태를 유지할 수 있습니다.

이 메서드는 런타임 시 요청 가로채기 흐름을 제어하도록 설계되었습니다. 글로벌 범위에 `worker` 참조를 노출하고 브라우저에서 언제든지 `window.worker.stop()`을 호출할 수 있습니다.

```tsx
// mocks/browser.js
import { setupWorker } from "msw/browser";
import { handlers } from "./handlers";

export const worker = setupWorker(...handlers);

// 워커 인스턴스를 글로벌 범위에 노출합니다.
window.worker = worker;
```

> 런타임에서 워커를 중지하면 중지된 상태는 페이지 새로 고침 시 유지되지 않습니다.
