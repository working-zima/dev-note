# `FormProvider`

`FormProvider`는 `useForm()`에서 반환된 메서드들을 **Context 형태로 하위 컴포넌트에 전달**해주는 컴포넌트입니다.  
이 컴포넌트로 감싸면, 하위 어디서든 `useFormContext()`를 통해 `register`, `handleSubmit` 등 모든 훅 메서드에 접근할 수 있습니다.

## Props

| 이름       | 타입     | 설명                                                |
| ---------- | -------- | --------------------------------------------------- |
| `...props` | `Object` | `useForm()`이 반환한 모든 메서드를 전달해야 합니다. |

🔹 `useFormContext()`는 별도의 인자를 받지 않으며, `FormProvider` 내부에서만 작동합니다.

## 사용 규칙 (Rules)

- `FormProvider`는 **최상위에서 단 한 번만** 사용해야 합니다.
- **중첩 사용은 금지**되어 있습니다. (Context 충돌 위험)

## 사용 예제

```tsx
import React from "react";
import { useForm, FormProvider, useFormContext } from "react-hook-form";

export default function App() {
  const methods = useForm(); // 모든 훅 메서드 가져오기
  const onSubmit = (data) => console.log(data);

  return (
    <FormProvider {...methods}>
      {" "}
      {/* 모든 메서드를 context로 전달 */}
      <form onSubmit={methods.handleSubmit(onSubmit)}>
        <NestedInput />
        <input type="submit" />
      </form>
    </FormProvider>
  );
}

// 깊은 하위 컴포넌트에서도 접근 가능
function NestedInput() {
  const { register } = useFormContext(); // context에서 register 꺼내기
  return <input {...register("test")} />;
}
```

## 요약

| 항목             | 설명                                    |
| ---------------- | --------------------------------------- |
| `FormProvider`   | `useForm()`의 메서드들을 Context로 전달 |
| `useFormContext` | 하위에서 Context에 접근하여 메서드 사용 |

## 팁

- 다단계 컴포넌트 구조에서 props로 `register`, `errors`, `watch` 등을 일일이 넘기지 않아도 됩니다.
- 팀 프로젝트나 대형 폼 구조에서는 유지보수성 향상에 매우 유리합니다.

원하시면 이 구조를 기반으로 `Controller`, `useController`를 사용하는 예제도 연결해서 안내드릴 수 있습니다.
