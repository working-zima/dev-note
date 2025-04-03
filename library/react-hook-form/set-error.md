# `setError` - 수동으로 에러 설정하기

`setError(name, error, config?)`는 수동으로 입력 필드에 에러를 설정하는 함수입니다.\
특히 API 응답을 기반으로 한 에러 처리에 유용하며, 폼 제출 중 발생하는 서버 유효성 검사에 적합합니다.

## 함수 시그니처

```ts
setError(
  name: string,
  error: {
    type: string,
    message?: string,
    types?: MultipleFieldErrors
  },
  config?: {
    shouldFocus?: boolean
  }
): void
```

## 매개변수 설명

| 이름     | 타입                         | 설명                                       |
| -------- | ---------------------------- | ------------------------------------------ |
| `name`   | `string`                     | 에러를 설정할 입력 필드 이름               |
| `error`  | `{ type, message?, types? }` | 에러 유형, 메시지 또는 다중 메시지         |
| `config` | `{ shouldFocus?: boolean }`  | 에러 발생 시 포커스 이동 여부 (기본 false) |

## 사용 규칙

- 해당 필드가 `register`에 의해 등록되어 있어야 포커스 기능이 작동합니다.
- 입력값이 `register`의 규칙을 통과하면, 해당 필드의 에러는 사라집니다.
- 등록되지 않은 필드(`notRegisteredInput`)는 `clearErrors()`를 호출해야 에러 제거됩니다.
- `root` 키를 사용하면 글로벌 에러도 처리할 수 있습니다.

## 사용 예시

### 단일 필드 에러 (Single Error)

```tsx
React.useEffect(() => {
  setError("username", {
    type: "manual",
    message: "아이디는 멋져야 합니다!",
  });
}, []);
```

### 다중 필드 에러 (Multiple Errors)

```tsx
<button
  onClick={() => {
    const errors = [
      { name: "username", message: "확인해주세요", type: "manual" },
      { name: "firstName", message: "한 번 더 확인해주세요", type: "manual" },
    ];

    errors.forEach(({ name, type, message }) => {
      setError(name, { type, message });
    });
  }}
>
  이름 에러 발생
</button>
```

### 단일 필드, 다중 에러 (Single Field, Multiple Errors)

```tsx
useForm({ criteriaMode: "all" });

React.useEffect(() => {
  setError("lastName", {
    types: {
      required: "필수입니다.",
      minLength: "최소 길이 부족",
    },
  });
}, []);
```

```tsx
{
  errors.lastName?.types?.required && <p>{errors.lastName.types.required}</p>;
}
{
  errors.lastName?.types?.minLength && <p>{errors.lastName.types.minLength}</p>;
}
```

### 서버 에러 (Server / Global Error)

```tsx
const onSubmit = async () => {
  const res = await fetch(...);
  if (res.status > 200) {
    setError("root.serverError", {
      type: res.status.toString(),
    });
  }
};
```

```tsx
{
  errors.root?.serverError?.type === "400" && <p>서버 오류가 발생했습니다.</p>;
}
```

## 요약

| 상황             | 설명                                                                |
| ---------------- | ------------------------------------------------------------------- |
| 필드 에러 설정   | `setError("username", { type: "manual", message: "..." })`          |
| 다중 에러        | `types: { required: "...", minLength: "..." }`                      |
| 서버/글로벌 에러 | `setError("root.serverError", { type: "400" })`                     |
| 수동 제거 필요   | `clearErrors("username")` 사용해야 제거됨 (특히 등록되지 않은 필드) |
