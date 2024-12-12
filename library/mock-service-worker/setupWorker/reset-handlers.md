# resetHandlers()

초기 요청 핸들러 목록으로 요청 핸들러를 재설정합니다.

## 호출 시그니처

`worker.resetHandlers()` 메서드는 인자를 받지 않고 호출할 수 있습니다. 이 경우 `worker.use()`를 통해 추가된 모든 런타임 요청 핸들러가 제거됩니다.

```tsx
const worker = setupWorker(http.get("/resource", resolver));
worker.use(http.post("/user", resolver));

worker.resetHandlers();
// "POST /user" 런타임 요청 핸들러가 제거되고,
// 초기 요청 핸들러인 "GET /resource"만 남습니다.
```

`worker.resetHandlers()` 메서드는 선택적으로 요청 핸들러 목록을 스프레드 인자로 받을 수도 있습니다. 이러한 목록이 제공되면 `setupWorker()`에 전달된 초기 요청 핸들러도 제거되고, 제공된 요청 핸들러 목록이 초기 핸들러로 작동합니다.

```tsx
const worker = setupWorker(http.get("/resource", resolver));
worker.use(http.post("/user", resolver));

worker.resetHandlers(http.patch("/book/:bookId", resolver));
// "POST /user" 런타임 요청 핸들러와 초기 요청 핸들러 "GET /resource"가 제거되고,
// "PATCH /book/:bookId" 요청 핸들러만 남습니다.
```

## 관련 자료

- 네트워크 동작 재정의
- `server.resetHandlers()`
