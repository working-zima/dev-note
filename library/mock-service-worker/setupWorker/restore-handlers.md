# restoreHandlers()

사용된 일회성 요청 핸들러를 미사용 상태로 표시합니다.

## 호출 시그니처

```tsx
const worker = setupWorker(
  http.get("/resource", () => HttpResponse.json({ id: "abc-123" }), {
    once: true,
  })
);

// 첫 번째 "GET /resource" 요청은 위의 요청 핸들러에 의해 가로채지고,
// 모킹된 JSON 응답이 반환됩니다.
await fetch("/resource");

// 위 요청 핸들러는 `{ once: true }`로 설정되었으며,
// 이미 일치하는 요청을 처리했기 때문에 이후 요청은 가로채지 않고
// 원래 응답이 반환됩니다.
await fetch("/resource");

// "worker.restoreHandlers()" 메서드를 호출하여 모든 사용된
// 일회성 요청 핸들러를 미사용 상태로 표시합니다.
// 이제 요청 핸들러가 다시 네트워크 요청에 영향을 미칩니다.
worker.restoreHandlers();

// 일치하는 일회성 요청 핸들러가 복원되었으므로 다시 모킹된 응답이 반환됩니다.
// 이 시점 이후로 핸들러는 다시 사용된 것으로 표시됩니다.
await fetch("/resource");
```

## 관련 자료

- 네트워크 동작 재정의
