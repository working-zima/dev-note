# listen()

현재 프로세스에서 요청 가로채기를 활성화합니다.

## 호출 시그니처

```typescript
function listen(options?: ListenOptions): void;
```

## 사용법

테스트 프레임워크의 `beforeAll` 훅에서 요청 가로채기를 활성화하는 것이 일반적입니다. 다음은 Jest를 사용하는 예시입니다.

```tsx
import { setupServer } from "msw/node";
import { handlers } from "./handlers";

const server = setupServer(...handlers);

beforeAll(() => {
  server.listen();
});
```

- `worker.start()`와 달리 `.listen()` 메서드는 동기적으로 동작합니다. 이는 서비스 워커 등록이나 실제 연결이 필요하지 않기 때문입니다.

## 옵션

### **`onUnhandledRequest`**

- **유형:** 사전 정의된 전략 또는 사용자 정의 전략 (기본값: `"warn"`)

요청 핸들러와 일치하지 않는 요청에 대한 처리 방식을 지정합니다.

### **사전 정의된 전략**

| **전략 이름** |                 **설명**                  |
| :-----------: | :---------------------------------------: |
|   `"warn"`    | 경고 메시지를 출력하고 요청을 그대로 수행 |
|   `"error"`   |     오류를 발생시키고 요청 실행 중단      |
|  `"bypass"`   | 메시지를 출력하지 않고 요청을 그대로 수행 |

#### 사용 예시

```tsx
server.listen({
  onUnhandledRequest: "error",
});
```

### **사용자 정의 전략**

```tsx
server.listen({
  onUnhandledRequest(request) {
    console.log("Unhandled %s %s", request.method, request.url);
  },
});
```

### **사전 정의된 전략과 사용자 정의 콜백 결합하기**

사전 정의된 전략은 사용자 정의 콜백의 두 번째 인자로 제공되며 재사용할 수 있습니다. 예를 들어, 정적 자산 요청을 무시하고 다른 처리되지 않은 요청에 대해서는 경고를 출력하는 방식은 다음과 같습니다:

```tsx
server.listen({
  onUnhandledRequest(request, print) {
    const url = new URL(request.url);

    // 정적 자산 요청을 무시
    if (url.pathname.includes("/assets/")) {
      return;
    }

    // 그 외 모든 처리되지 않은 요청에 대해 경고 출력
    print.warning();
  },
});
```
