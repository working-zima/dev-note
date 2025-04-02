# `useFormContext` - React Hook Form

`useFormContext`는 **폼 메서드(useForm 반환값)**를 컴포넌트 트리 전체에 공유할 수 있게 해주는 커스텀 훅입니다.  
복잡하거나 깊게 중첩된 구조에서 props로 전달하는 것이 번거로울 때 유용합니다.

## 반환값 (Return)

`useFormContext()`를 호출하면 `useForm()`이 반환하는 모든 메서드와 속성을 사용할 수 있습니다.  
(예: `register`, `handleSubmit`, `formState`, `setValue` 등)

## 사용 규칙 (Rules)

- 반드시 최상위에서 `<FormProvider>`로 감싸야 `useFormContext`가 정상 작동합니다.
- `<FormProvider>`는 `useForm()`의 반환값을 전부 props로 넘겨야 합니다.

## 기본 사용 예시

```tsx
import React from "react";
import { useForm, FormProvider, useFormContext } from "react-hook-form";

export default function App() {
  const methods = useForm(); // useForm 호출
  const onSubmit = (data) => console.log(data);

  return (
    <FormProvider {...methods}>
      {" "}
      {/* 모든 메서드를 context에 전달 */}
      <form onSubmit={methods.handleSubmit(onSubmit)}>
        <NestedInput /> {/* 중첩 컴포넌트에서도 접근 가능 */}
        <input type="submit" />
      </form>
    </FormProvider>
  );
}
```

## 중첩 컴포넌트에서 사용

```tsx
function NestedInput() {
  const { register } = useFormContext(); // useForm의 register 가져오기
  return <input {...register("test")} />;
}
```

## 요약

| 항목               | 설명                                           |
| ------------------ | ---------------------------------------------- |
| `useForm()`        | 폼 메서드들을 생성합니다.                      |
| `FormProvider`     | `useForm()` 결과를 Context로 전달합니다.       |
| `useFormContext()` | Context에서 `useForm()` 메서드들을 불러옵니다. |

## 팁

- 대규모 폼에서 중첩된 입력 필드가 많다면 `useFormContext`로 가독성 및 유지보수성을 높일 수 있습니다.
- React Context를 기반으로 동작하므로 최상단에서 한 번만 `FormProvider`로 감싸면 됩니다.
