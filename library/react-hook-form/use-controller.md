# `useController` - 커스텀 제어 컴포넌트를 위한 훅

`useController`는 `Controller` 컴포넌트 내부에서 동작하는 로직을 훅 형태로 분리한 것입니다.  
직접적으로 값을 제어하고, 재사용 가능한 입력 컴포넌트를 만들 때 유용합니다.

## Props

| 이름               | 타입      | 필수 | 설명                                                   |
| ------------------ | --------- | ---- | ------------------------------------------------------ |
| `name`             | `string`  | ✅   | 입력의 고유 이름                                       |
| `control`          | `Control` | ⛔️  | `useForm()`의 반환값. `FormProvider` 사용 시 생략 가능 |
| `defaultValue`     | `any`     | 🔸   | 초기값 (필수, undefined 불가)                          |
| `rules`            | `object`  | ❌   | `register`와 동일한 유효성 검사 규칙                   |
| `shouldUnregister` | `boolean` | ❌   | 언마운트 시 등록 해제 여부                             |
| `disabled`         | `boolean` | ❌   | 비활성화 여부 (제출값에서 제외됨)                      |

## 반환값

```ts
const {
  field, // 입력 관련 핸들러 및 상태
  fieldState, // 해당 필드의 상태 (에러, 터치 여부 등)
  formState, // 폼 전체 상태
} = useController({ name: "test", control });
```

## field 구조

| 속성       | 설명                           |
| ---------- | ------------------------------ |
| `onChange` | 값 변경 → Hook Form에 전달     |
| `onBlur`   | blur 이벤트 → Hook Form에 전달 |
| `value`    | 현재 입력 값                   |
| `name`     | 입력 이름                      |
| `ref`      | ref 연결 (에러 시 포커스 가능) |
| `disabled` | 비활성화 여부                  |

## fieldState 구조

| 속성        | 설명                |
| ----------- | ------------------- |
| `invalid`   | 유효하지 않음       |
| `isTouched` | 포커스가 된 적 있음 |
| `isDirty`   | 값이 변경됨         |
| `error`     | 에러 정보           |

## formState 주요 속성

- `isDirty`: 하나라도 값이 변경된 경우 `true`
- `dirtyFields`: 변경된 필드 목록
- `touchedFields`: 상호작용한 필드 목록
- `isValid`: 유효성 검사 통과 여부
- `isSubmitting`, `submitCount`, `isSubmitSuccessful` 등

> `isDirty` 또는 `dirtyFields`를 정확히 비교하려면 반드시 `useForm({ defaultValues })`를 지정해야 합니다.

## 사용 예시 (MUI 기반 TextField)

```tsx
import { TextField } from "@material-ui/core";
import { useController, useForm } from "react-hook-form";

function Input({ control, name }) {
  const {
    field,
    fieldState: { invalid, isTouched, isDirty },
    formState: { touchedFields, dirtyFields },
  } = useController({
    name,
    control,
    rules: { required: true },
  });

  return (
    <TextField
      {...field}
      error={invalid}
      helperText={invalid ? "필수 입력 항목입니다." : ""}
      inputRef={field.ref}
    />
  );
}
```

## 커스텀 상태와 함께 쓰기

```tsx
const { field } = useController({ name: "age" });
const [value, setValue] = useState(field.value);

<input
  value={value}
  onChange={(e) => {
    const intValue = parseInt(e.target.value);
    field.onChange(intValue); // Hook Form에 값 전달
    setValue(intValue); // UI 상태 업데이트
  }}
/>;
```

## 주의사항

- ❌ `register`와 **함께 쓰면 안 됨**

```tsx
<input {...field} {...register("test")} /> // ❌
<input {...field} /> // ✅
```

- ⚠️ 여러 개의 `useController`를 사용할 땐 `field`에 별칭 지정 권장

```tsx
const { field: input } = useController({ name: 'test' });
const { field: checkbox } = useController({ name: 'test2' });

<input {...input} />
<input {...checkbox} />
```

## 요약

| 항목            | 설명                                        |
| --------------- | ------------------------------------------- |
| `useController` | `Controller`를 대체할 수 있는 훅 형태       |
| `field`         | 제어 컴포넌트에 필요한 이벤트, 값, ref 포함 |
| `fieldState`    | 해당 입력의 에러, 터치 여부 등 상태         |
| `formState`     | 폼 전체의 상태 (dirty, submit 상태 등)      |
| `defaultValue`  | 반드시 지정해야 올바른 상태 추적 가능       |

## 참고 자료

- [React Hook Form](https://www.nextree.io/react-hook-form/)
