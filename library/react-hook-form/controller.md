# `Controller` - 외부 제어 컴포넌트용 Wrapper

React Hook Form은 기본적으로 비제어 컴포넌트 input을 지향하지만,  
`React-Select`, `MUI`, `AntD`, `Chakra UI` 등 외부 라이브러리의 제어 컴포넌트는 `register`로 직접 다루기 어렵습니다.  
이럴 때 `Controller` 컴포넌트를 사용하면 유효성 검사와 React Hook Form은 해당 필드의 상태를 추적하고 필요한 경우에만 업데이트하는 장점을 이용할 수 있습니다.

## Props 요약

| 이름               | 타입                             | 필수 | 설명                                                               |
| ------------------ | -------------------------------- | ---- | ------------------------------------------------------------------ |
| `name`             | `string`                         | ✅   | 입력의 고유 이름                                                   |
| `control`          | `Control`                        | ⛔️  | `useForm()`에서 반환된 객체, `FormProvider` 사용 시 생략 가능      |
| `render`           | `({ field, fieldState }) => JSX` | ✅   | 렌더 함수. 이벤트와 값을 커스텀 컴포넌트에 연결                    |
| `defaultValue`     | `any`                            | 🔸   | 초기값 지정 (필드 단위 또는 useForm의 defaultValues로 지정해야 함) |
| `rules`            | `RegisterOptions`                | ❌   | `register`에서 사용하는 유효성 검사 옵션                           |
| `shouldUnregister` | `boolean`                        | ❌   | 언마운트 시 등록 해제 여부                                         |
| `disabled`         | `boolean`                        | ❌   | 비활성화 처리. 제출 데이터에서도 제외됨                            |

## `Controller`의 반환값 (`render` 함수에 전달되는 객체들)

### `field` 객체

| 이름       | 타입                   | 설명                                                                                     |
| ---------- | ---------------------- | ---------------------------------------------------------------------------------------- |
| `onChange` | `(value: any) => void` | input의 값을 React Hook Form으로 전달하는 함수입니다. `value`는 `undefined`면 안 됩니다. |
| `onBlur`   | `() => void`           | blur 이벤트를 RHF에 전달하는 함수입니다.                                                 |
| `value`    | `unknown`              | 현재 필드의 값입니다.                                                                    |
| `disabled` | `boolean`              | 비활성화 여부입니다.                                                                     |
| `name`     | `string`               | 등록된 필드 이름입니다.                                                                  |
| `ref`      | `React.Ref`            | 오류 발생 시 포커스를 위해 input에 연결해야 하는 ref입니다.                              |

### `fieldState` 객체

| 이름        | 타입      | 설명                                         |
| ----------- | --------- | -------------------------------------------- |
| `invalid`   | `boolean` | 필드가 유효하지 않으면 true                  |
| `isTouched` | `boolean` | 한 번이라도 포커스되었다가 블러되었으면 true |
| `isDirty`   | `boolean` | 기본값에서 변경되었으면 true                 |
| `error`     | `object`  | 해당 필드의 오류 정보                        |

### `formState` 객체

| 이름                 | 타입      | 설명                                                                                   |
| -------------------- | --------- | -------------------------------------------------------------------------------------- |
| `isDirty`            | `boolean` | 하나 이상의 필드가 변경되었으면 true. `defaultValues`를 설정해야 정확히 동작합니다.    |
| `dirtyFields`        | `object`  | 변경된 필드들의 목록입니다.                                                            |
| `touchedFields`      | `object`  | 유저가 한 번이라도 포커스한 필드들의 목록입니다.                                       |
| `defaultValues`      | `object`  | `useForm`의 기본값 또는 `reset()`으로 설정된 값입니다.                                 |
| `isSubmitted`        | `boolean` | 폼이 제출된 적 있으면 true. `reset()` 시 초기화됩니다.                                 |
| `isSubmitSuccessful` | `boolean` | 마지막 제출이 성공적으로 완료되었으면 true                                             |
| `isSubmitting`       | `boolean` | 현재 제출 중이면 true                                                                  |
| `isLoading`          | `boolean` | 비동기 `defaultValues`를 로딩 중이면 true (`async` 옵션 사용 시 해당)                  |
| `submitCount`        | `number`  | 폼이 제출된 횟수입니다.                                                                |
| `isValid`            | `boolean` | 모든 필드가 유효하면 true. `setError`는 이 값에 영향을 주지 않습니다.                  |
| `isValidating`       | `boolean` | 유효성 검사 중이면 true                                                                |
| `errors`             | `object`  | 전체 필드의 오류 정보를 담고 있는 객체입니다. `ErrorMessage` 컴포넌트로 쉽게 표시 가능 |

## 기본 예제 (React + 외부 컴포넌트)

```tsx
import ReactDatePicker from "react-datepicker";
import { useForm, Controller } from "react-hook-form";

type FormValues = {
  ReactDatepicker: string;
};

function App() {
  const { handleSubmit, control } = useForm<FormValues>();

  return (
    <form onSubmit={handleSubmit(console.log)}>
      <Controller
        name="ReactDatepicker"
        control={control}
        render={({ field }) => (
          <ReactDatePicker
            onChange={field.onChange}
            onBlur={field.onBlur}
            selected={field.value}
          />
        )}
      />
      <input type="submit" />
    </form>
  );
}
```

## React Native 예제

```tsx
import { View, TextInput, Button, Text } from "react-native";
import { useForm, Controller } from "react-hook-form";

export default function App() {
  const {
    control,
    handleSubmit,
    formState: { errors },
  } = useForm({
    defaultValues: {
      firstName: "",
      lastName: "",
    },
  });

  const onSubmit = (data) => console.log(data);

  return (
    <View>
      <Controller
        name="firstName"
        control={control}
        rules={{ required: true }}
        render={({ field }) => (
          <TextInput
            placeholder="First name"
            onChangeText={field.onChange}
            onBlur={field.onBlur}
            value={field.value}
          />
        )}
      />
      {errors.firstName && <Text>필수 입력입니다.</Text>}

      <Controller
        name="lastName"
        control={control}
        rules={{ maxLength: 100 }}
        render={({ field }) => (
          <TextInput
            placeholder="Last name"
            onChangeText={field.onChange}
            onBlur={field.onBlur}
            value={field.value}
          />
        )}
      />
      <Button title="Submit" onPress={handleSubmit(onSubmit)} />
    </View>
  );
}
```

## 주의사항 (Tips)

- `register()`와 `Controller`를 **동시에 사용하면 안 됩니다**  
  ❌ `return <input {...field} {...register('test')} />`  
  ✅ `return <input {...field} />`

- 외부 컴포넌트에서 전달받는 값이 문자열이면, `onChange`에서 직접 변환할 수 있습니다.

```tsx
<Controller
  name="test"
  render={({ field }) => (
    <input
      {...field}
      onChange={(e) => field.onChange(parseInt(e.target.value))}
    />
  )}
/>
```

## 요약

| 항목           | 설명                                                 |
| -------------- | ---------------------------------------------------- |
| `Controller`   | 외부 제어 컴포넌트를 RHF와 연결하는 Wrapper          |
| `render`       | 입력 이벤트와 value를 넘겨주는 함수형 prop           |
| `field`        | RHF가 제공하는 이벤트 핸들러 및 값, ref              |
| `fieldState`   | 현재 필드의 에러 상태, 터치 여부 등                  |
| `rules`        | `register`와 동일한 유효성 검사 규칙 사용 가능       |
| `defaultValue` | 필드에 반드시 초기값 제공해야 비교 및 상태 추적 가능 |
