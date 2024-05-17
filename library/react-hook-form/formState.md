# formState

## formState: Object

이 객체는 전체 폼 상태에 대한 정보를 포함합니다.\
사용자의 폼 애플리케이션 상호작용을 추적하는 데 도움이 됩니다.

## Return

| Name   | Type    | Description |
| :-:    | :-:     | :-          |
`isDirty` | boolean | 사용자가 입력 중에 수정을 하면 true로 설정됩니다.<br/>중요: `useForm`에서 모든 입력의 기본값을 제공하여 폼이 변경되었는지를 비교할 수 있도록 해야 합니다.<br/>파일 형식의 입력은 파일 선택을 취소할 수 있는 능력으로 인해 앱 수준에서 관리해야 합니다.<br/>사용자 지정 객체, 클래스 또는 파일 객체는 지원하지 않습니다.
`dirtyFields` | object | 사용자가 수정한 필드입니다.<br/>`useForm`을 통해 모든 입력의 기본값을 제공하여 라이브러리가 기본값과 비교할 수 있도록 해야 합니다.<br/>중요: `useForm`에서 모든 입력의 기본값을 제공하여 훅 폼이 각 필드의 변경 여부를 비교할 수 있도록 해야 합니다.<br/>변경된 필드는 전체 폼 상태인 `isDirty` `formState`와 달리 필드 수준에서 필드를 표시합니다.<br/>전체 폼 상태를 결정하려면 `isDirty` 대신 `dirtyFields`를 사용하십시오.
`touchedFields` | object | 사용자가 상호작용한 모든 입력을 포함하는 객체입니다.
`defaultValues` | object | `useForm`의 `defaultValues` 또는 reset API를 통해 업데이트된 `defaultValues`에서 설정된 값입니다.
`isSubmitted` | boolean | 폼이 제출된 후 true로 설정됩니다. `reset` 메서드가 호출될 때까지 true로 유지됩니다.
`isSubmitSuccessful` | boolean | 폼이 런타임 오류 없이 성공적으로 제출되었음을 나타냅니다.
`isSubmitting` | boolean | 폼이 현재 제출 중이면 true입니다.<br/>그렇지 않으면 false입니다.
`isLoading` | boolean | 폼이 현재 비동기 기본값을로드 중이면 true입니다.<br/>중요:이 속성은 비동기 기본값에만 적용됩니다.
`submitCount` | number | 폼이 제출된 횟수입니다.
`isValid` | boolean | 폼에 오류가 없으면 true로 설정됩니다.<br/>`setError`는 `isValid formState`에 영향을 주지 않으며, `isValid`는 항상 전체 폼 유효성 검사 결과를 통해 파생됩니다.
`isValidating` | boolean | 검증 중에 true로 설정됩니다.
`validatingFields` | boolean | 비동기 검증을 받는 필드를 캡처합니다.
`errors` | object | 필드 오류가 포함된 객체입니다.<br/>오류 메시지를 쉽게 검색할 수 있는 ErrorMessage 구성 요소도 있습니다.

### isDirty

```tsx
const {
  formState: { isDirty, dirtyFields },
  setValue,
} = useForm({ defaultValues: { test: "" } });


// isDirty: true
setValue('test', 'change')

// isDirty: false because there getValues() === defaultValues
setValue('test', '')
```

### isLoading

```tsx
const {
  formState: { isLoading }
} = useForm({
  defaultValues: async () => await fetch('/api')
});
```

### errors

`input`에 입력 값 없이 `submit` 했을 경우 예시입니다.

```tsx
const { formState: { errors } } = useForm();

// validation과 에러 표시를 해줍니다.
// type은 error가 발생한 이유가 표시됩니다.
// message는 에러 메시지를 표시합니다.
console.log(errors);
// email: { type: "required", message: 'email is required', ref: input }

<div>
  <form onSubmit={handleSubmit(onValid)}>
    <input
      {...register("email", {
        required: "email is required",
        minLength: {
          value: 5,
          message: "Your email is too short"
        },
        pattern: {
          value: /^[A-Za-z0-9._%+-]+@naver.com$/,
          message: "Only naver.com emails allowed"
        }
      })}
      placeholder="Email" />
      <span>{errors.email?.message}</span>
  <form/>
<div/>
```

## Rules

- `formState`는 렌더 성능을 향상시키기 위해 프록시로 래핑되어 있으며, 특정 상태가 구독되지 않은 경우 추가 로직을 건너뜁니다.\
따라서 상태 업데이트를 활성화하려면 렌더 전에 해당 상태를 호출하거나 읽어야 합니다.

- `formState`는 일괄적으로 업데이트됩니다.\
`useEffect`를 통해 `formState`을 구독하려면 전체 `formState`를 선택적 배열에 배치해야 합니다.

### SNIPPET

```tsx
useEffect(() => {
  if (formState.errors.firstName) {
    // 여기에서 로직을 수행합니다.
  }
}, [formState]); // ✅
// ❌ formState.errors는 useEffect를 트리거하지 않습니다.
```

`formState`을 구독할 때 논리 연산자에 주의하세요.

```tsx
// ❌ formState.isValid에 조건부로 액세스되기 때문에 프록시가 해당 상태의 변경 사항을 구독하지 않습니다.
return <button disabled={!formState.isDirty || !formState.isValid} />;

// ✅ 모든 formState 값을 읽어 변경 사항을 구독합니다.
const { isDirty, isValid } = formState;
return <button disabled={!isDirty || !isValid} />;
```
