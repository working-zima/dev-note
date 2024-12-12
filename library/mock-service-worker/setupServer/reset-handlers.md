# resetHandlers()

요청 핸들러를 초기 목록으로 재설정합니다.

## 호출 시그니처

```typescript
server.resetHandlers();
```

## 동작 설명

- `server.resetHandlers()` 메서드는 인자를 받지 않고 호출할 수 있습니다.
- 이 경우, `server.use()`를 통해 추가된 모든 런타임 요청 핸들러가 제거됩니다.

### 사용 예시: 초기 핸들러 유지

```tsx
const server = setupServer(http.get("/resource", resolver));

// 런타임 요청 핸들러 추가
server.use(http.post("/user", resolver));

// 런타임 핸들러 제거 및 초기 상태 복원
server.resetHandlers();

// "POST /user" 런타임 핸들러는 제거되며,
// 초기 핸들러인 "GET /resource"만 남습니다.
```

### 사용 예시: 새로운 핸들러 목록 설정

- 선택적으로 요청 핸들러 목록을 인자로 전달할 수 있습니다.
- 인자를 전달하면 `setupServer()`에 전달된 초기 요청 핸들러도 제거되며, 제공된 요청 핸들러 목록이 초기 핸들러로 작동합니다.

```tsx
const server = setupServer(http.get("/resource", resolver));

// 런타임 요청 핸들러 추가
server.use(http.post("/user", resolver));

// 모든 기존 핸들러 제거 및 새 핸들러 설정
server.resetHandlers(http.patch("/book/:bookId", resolver));

// "POST /user"와 "GET /resource"는 제거되고,
// "PATCH /book/:bookId" 요청 핸들러만 남습니다.
```

## 관련 자료

- **네트워크 동작 재정의 (Network behavior overrides)**
- **`worker.resetHandlers()`**
