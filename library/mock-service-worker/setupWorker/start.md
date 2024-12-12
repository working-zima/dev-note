# start()

서비스 워커를 등록하고 요청 가로채기를 시작합니다.

## 호출 시그니처

`worker.start()` 메서드는 선택적 옵션 객체를 인자로 받아 서비스 워커 등록을 사용자 정의할 수 있습니다.

기본적으로 인자를 전달하지 않으면 `worker.start()` 메서드는 `./mockServiceWorker.js`에 있는 서비스 워커를 등록하고 요청 가로채기를 시작합니다.

```tsx
import { setupWorker } from "msw/browser";
import { handlers } from "./handlers";

const worker = setupWorker(...handlers);
await worker.start(); // Promise<{ pending }>
```

> 서비스 워커 등록 및 활성화는 비동기 작업입니다. `worker.start()`는 워커가 준비되었을 때 해결되는 프로미스를 반환합니다. 네트워크 의존적인 코드와 워커 등록 간 경쟁 조건을 방지하려면 `await`을 사용하세요.

MSW가 활성화되면 브라우저 콘솔에 확인 메시지가 표시됩니다.

```bash
[MSW] Mocking enabled.
```

## 옵션

### `serviceWorker`

#### `url`

- **유형:** 문자열
- **기본값:** `"/mockServiceWorker.js"`

서비스 워커 등록 URL 사용자 정의. 워커 스크립트를 사용자 지정 경로에서 제공하는 경우 이 옵션을 사용하세요.

```tsx
worker.start({
  serviceWorker: {
    url: "/assets/mockServiceWorker.js",
  },
});
```

> 서비스 워커는 해당 수준 또는 하위 수준의 네트워크만 제어할 수 있으므로 워커는 항상 루트에 등록하는 것이 좋습니다.

#### `options`

- **유형:** 서비스 워커 등록 옵션

서비스 워커 등록 자체를 수정하는 옵션으로, MSW와 직접 관련이 없습니다.

```tsx
worker.start({
  serviceWorker: {
    options: {
      // 워커가 제어할 수 있는 페이지 범위를 좁힙니다.
      scope: "/product",
    },
  },
});
```

### `findWorker`

- **유형:** 함수, 반환 유형: Boolean

서버에서 워커 스크립트를 찾기 위한 사용자 정의 함수입니다. 애플리케이션이 프록시 뒤에서 실행되거나 동적 호스트 이름을 사용하는 경우 사용하세요.

```tsx
worker.start({
  findWorker(scriptUrl, mockServiceWorkerUrl) {
    return scriptUrl.includes("mockServiceWorker");
  },
});
```

### `quiet`

- **유형:** Boolean
- **기본값:** `false`

라이브러리의 모든 로깅을 비활성화합니다(예: 활성화 메시지, 가로챈 요청 메시지).

```tsx
worker.start({
  quiet: true,
});
```

### `onUnhandledRequest`

- **유형:** 문자열 또는 함수
- **기본값:** `"warn"`

일치하는 요청 핸들러가 없는 요청에 대해 반응하는 방식을 결정합니다.

#### 표준 모드

|    처리 모드    |                               설명                               |
| :-------------: | :--------------------------------------------------------------: |
| `warn` (기본값) | 브라우저 콘솔에 경고 메시지를 출력하고 요청을 그대로 수행합니다. |
|     `error`     |               오류를 발생시키고 요청을 중단합니다.               |
|    `bypass`     |      아무 메시지도 출력하지 않고 요청을 그대로 수행합니다.       |

#### 사용자 정의 `onUnhandledRequest` 함수

```tsx
worker.start({
  onUnhandledRequest(request, print) {
    // URL에 "cdn.com"이 포함된 요청은 무시합니다.
    if (request.url.includes("cdn.com")) {
      return;
    }

    // 그렇지 않으면 처리되지 않은 요청 경고를 출력합니다.
    print.warning();
  },
});
```

### `waitUntilReady`

- **유형:** Boolean
- **기본값:** `true`

서비스 워커 등록 중 발생하는 애플리케이션 요청을 지연시킵니다.

> 이 옵션을 비활성화하는 것은 권장하지 않습니다. 비활성화하면 워커 등록과 애플리케이션 실행 간 경쟁 조건이 발생할 수 있습니다.
