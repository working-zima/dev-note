# useController

## Controller

React Hook Form은 비제어 컴포넌트를 사용하는 라이브러리입니다.\
Controller 컴포넌트는 외부 제어 컴포넌트와의 작업을 간편하게 만들어 줍니다.\
외부 제어 컴포넌트를 Controller로 감싸면 React Hook Form은 해당 필드의 상태를 추적하고 필요한 경우에만 업데이트하는 장점을 이용할 수 있습니다.

### Props

아래의 table은 contains  Controller의 인수 정보를 포함하고 있습니다.

이름 | Type | Required | Description
:-: | :-: | :-: | :-:
name | FieldPath | ✓ | 입력의 고유한 이름입니다.
`control` | Control | X | `useForm`을 호출하여 얻은 `control` 객체입니다. <br> `FormProvider`를 사용할 때는 선택 사항입니다.
`render` | Function | X | 렌더 함수입니다. <br> 리액트 엘리먼트를 반환하고 컴포넌트에 이벤트와 값을 첨부하는 능력을 제공하는 함수입니다. <br> 이를 통해 비표준 속성 이름을 가진 외부 제어 컴포넌트와 통합하는 것이 간소화됩니다. <br> 자식 컴포넌트에 `onChange`, `onBlur`, `name`, `ref`, `value`를 제공하며, 특정 입력 상태를 포함하는 `fieldState` 객체도 제공합니다.
`defaultValue` | unknown | X | 중요: `useForm`에서 `defaultValue` 또는 `defaultValues`에 undefined를 적용할 수 없습니다. <br> 필드 레벨에서 `defaultValue`를 설정하거나 `useForm`의 `defaultValues`를 사용해야 합니다. <br> undefined는 유효한 값이 아닙니다. 폼이 기본 값으로 `reset`을 호출하는 경우 `useForm`에 `defaultValues`를 제공해야 합니다. <br> `onChange`를 undefined와 함께 호출하는 것은 유효하지 않습니다. <br> 대신 기본/지워진 값으로 null 또는 빈 문자열을 사용해야 합니다.
`rules` | Object | X | 등록 옵션과 동일한 형식의 유효성 검사 규칙입니다. <br> 다음을 포함합니다: `required`, `min`, `max`, `minLength`, `maxLength`, `pattern`, `validate`
`shouldUnregister` | boolean = false | X | 입력이 언마운트된 후 등록 해제되고 `defaultValues`도 제거됩니다. <br> `useFieldArray`와 함께 사용할 때는 이 속성을 피해야 합니다. <br> unregister 함수는 입력이 언마운트/리마운트되고 재정렬될 때 호출됩니다.
`disabled` | boolean = false | X | `disabled` prop는 필드 속성에서 반환됩니다. <br> 제어된 입력이 비활성화되고 해당 값은 제출 데이터에서 제외됩니다.

`useForm`의 return으로 `control`을 받아 사용합니다.

```tsx
import React from "react";
import { useForm, Controller } from "react-hook-form";
import { TextField } from "@material-ui/core";

type FormInputs = {
  firstName: string
}

function App() {
  const { control, handleSubmit } = useForm<FormInputs>();
  const onSubmit = (data: FormInputs) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Controller
        as={TextField}
        name="firstName"
        control={control}
        defaultValue=""
      />

      <input type="submit" />
    </form>
  );
}
```

## 참고 자료

- [React Hook Form](https://www.nextree.io/react-hook-form/)
