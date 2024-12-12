# setupServer

Node.js에서 요청 가로채기를 구성합니다.

## 개요

`setupServer` 함수는 Node.js 프로세스에서 요청 가로채기를 설정하는 함수입니다.

- **서버를 생성하지 않음:** 이름에 "서버"가 포함되어 있지만, 별도의 서버를 생성하지 않고 프로세스의 스레드에서 동작합니다.
- **Node.js 전용:** Node.js는 서비스 워커(Service Worker)를 실행할 수 없으므로, 표준 요청 모듈(`http` 등)을 확장하여 요청을 가로채고 모킹된 응답을 반환합니다.

## Node.js 특정 사항

`setupServer`는 브라우저와 Node.js 간 동일한 요청 핸들러를 재사용할 수 있도록 연결 역할을 합니다. 그러나 브라우저 API(`location`, `localStorage` 등)는 Node.js 환경에서 사용할 수 없으므로, 해당 API가 필요한 경우 폴리필을 적용하거나 적절한 테스트 환경을 설정해야 합니다.

## 사용법

`setupServer` 사용은 `setupWorker`와 유사합니다. 요청 핸들러 목록을 전달하고 요청 가로채기를 시작하면 됩니다.

```tsx
import { http, HttpResponse } from "msw";
import { setupServer } from "msw/node";

// 서버 측 API에 요청 핸들러를 제공
const server = setupServer(
  http.get("/user", () => {
    return HttpResponse.json({
      id: "15d42a4d-1948-4de4-ba78-b8a893feaf45",
      firstName: "John",
    });
  })
);

// 요청 가로채기 시작
server.listen();
```

> `setupServer`는 `msw/node`에서 가져오며, 요청 핸들러는 `msw`에서 가져옵니다.

## 테스트 설정 예시 (Jest 프레임워크)

Node.js에서 Mock Service Worker의 일반적인 사용 사례는 통합 테스트입니다. API 모킹을 테스트 설정의 일부로 통합하여 요청 가로채기 로직을 적절하게 시작하고 정리하세요.

```tsx
// jest.setup.js
import { setupServer } from "msw/node";
import { handlers } from "./handlers";

const server = setupServer(...handlers);

beforeAll(() => {
  // 요청 가로채기 시작
  server.listen();
});

afterEach(() => {
  // 개별 테스트에서 추가된 런타임 핸들러 제거
  server.resetHandlers();
});

afterAll(() => {
  // 요청 가로채기 비활성화 및 정리
  server.close();
});
```

## 주의사항

- 전역 테스트 설정의 일부로 요청 가로채기를 설정하는 것을 권장합니다.
- 필요한 경우 개별 테스트에서 `setupServer`를 사용할 수도 있습니다.
- **중요:** 테스트 코드 전반에서 하나의 패턴을 선택하고 일관되게 사용하세요. **여러 번 `setupServer` 호출은 권장하지 않습니다!**
