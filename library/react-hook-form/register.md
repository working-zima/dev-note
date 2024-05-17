# register

```tsx
(name: string, RegisterOptions?) => ({ onChange, onBlur, name, ref })
```

이 메서드를 사용하면 입력 또는 선택 요소를 등록하고 React Hook Form에 유효성 검사 규칙을 적용할 수 있습니다.\
유효성 검사 규칙은 모두 HTML 표준을 기반으로 하며 사용자 정의 유효성 검사 방법도 허용합니다.

```tsx
const { register } = useForm();

console.log(register("test"))
// {name: 'test', onChange: f, onBlur: f, ref: f}
```

register 함수를 호출하고 입력의 이름을 제공하면 다음 메서드를 받게 됩니다:

## Return

```tsx
const { onChange, onBlur, name, ref } = register('firstName');
// 제공한 이름과 필드 경로에 대한 타입 검사를 포함합니다.

<input
  name={name} // name 속성 할당 (현재 name은 'firstName')
  onChange={onChange} // onChange 이벤트 할당
  onBlur={onBlur} // onBlur 이벤트 할당
  ref={ref} // ref 속성 할당
/>

// 위와 동일한 역할
<input {...register('firstName')} />
```

- `name`: 입력 요소의 이름입니다.
- `onChange`: 입력 값이 변경될 때 호출되는 함수입니다.
- `onBlur`: 입력 요소가 포커스를 잃을 때 호출되는 함수입니다.
- `ref`: 입력 요소의 참조입니다.

## RegisterOptions

### required

`required: true` 인 경우 입력값이 있어야하며, 없을 경우 error를 나타내는 문자열 메시지를 할당할 수 있습니다.

```tsx
<input
  {...register("test", {
    required: true
  })}
/>
```

### maxLength

허용될 수 있는 입력 값의 최대 길이를 지정합니다.

```tsx
<input
  {...register("test", {
      maxLength: 2
  })}
/>
```

### minLength

허용될 수 있는 입력 값의 최소 길이를 지정합니다.

```tsx
<input
  {...register("test", {
    minLength: 1
  })}
/>
```

### max

form이 숫자값을 입력받는 경우, 최댓값을 설정하는 option 입니다.

```tsx
<input
  type="number"
  {...register('test', {
    max: 3
  })}
/>
```

### min

form이 숫자값을 입력받는 경우, 최솟값을 설정하는 option 입니다.

```tsx
<input
  type="number"
  {...register("test", {
    min: 3
  })}
/>
```

### pattern

자바스크립트 정규식을 적용시키는 option 입니다.\

```tsx
<input
  {...register("test", {
    pattern: /[A-Za-z]{3}/
  })}
/>
```

### validate

콜백함수를 전달하여, required 속성에 포함된 다른 유효성 검사 규칙에 의존하지 않고 독립적으로 실행됩니다.

```tsx
<input
  {...register("test", {
    validate: (value, formValues) => value === '1'
  })}
/>
// object of callback functions
<input
  {...register("test1", {
    validate: {
      positive: v => parseInt(v) > 0,
      lessThanTen: v => parseInt(v) < 10,
      validateNumber: (_, values) =>
        !!(values.number1 + values.number2),
      checkUrl: async () => await fetch(),
    }
  })}
/>
```

참고: 객체나 배열 입력 데이터의 경우 대부분의 규칙이 문자열, 문자열[], 숫자 및 부울 데이터 유형에 적용되므로 검증을 위해 validate 함수를 사용하는 것이 권장됩니다.

### valueAsNumber

일반적으로 숫자를 반환합니다.\
잘못된 경우 NaN이 반환됩니다.

- valueAs 프로세스는 유효성 검사 전에 진행됩니다.
- `<input type="number" />`에만 적용되고 지원되지만, 공백이나 다른 데이터 조작 없이도 숫자 유형으로 변환됩니다.
- `defaultValue` 또는 `defaultValues를` 변환하지 않습니다.

```tsx
<input
  type="number"
  {...register("test", {
    valueAsNumber: true,
  })}
/>
```

### valueAsDate

일반적으로 Date 객체를 반환합니다.\
잘못된 경우 Invalid Date가 반환됩니다.

- valueAs 프로세스는 유효성 검사 전에 진행됩니다.
- `<input />`에만 적용됩니다.
- defaultValue 또는 defaultValues를 변환하지 않습니다.

```tsx
<input
  type="date"
  {...register("test", {
    valueAsDate: true,
  })}
/>
```

### setValueAs

입력 값을 반환합니다.

- valueAs 프로세스는 유효성 검사 전에 진행됩니다.\
또한 valueAsNumber 또는 valueAsDate 중 하나라도 true인 경우 setValueAs는 무시됩니다.
- 텍스트 입력에만 적용됩니다.
- defaultValue 또는 defaultValues를 변환하지 않습니다.

```tsx
<input
  type="number"
  {...register("test", {
    setValueAs: v => parseInt(v),
  })}
/>
```

### disabled

true로 disabled를 설정하면 입력 값이 undefined가 되고 입력 제어가 비활성화됩니다.\
Disabled prop은 또한 내장된 유효성 검사 규칙을 무시합니다.\
스키마 유효성 검사를 위해 입력이나 컨텍스트 객체에서 반환된 undefined 값을 활용할 수 있습니다.

```tsx
<input
  {...register("test", {
    disabled: true
  })}
/>
```

### onChange

```tsx
register('firstName', {
  onChange: (e) => console.log(e)
})
```

### onBlur

```tsx
register('firstName', {
  onBlur: (e) => console.log(e)
})
```

### value

첫 렌더링시, form 초기 입력값을 가지도록 설정하는 option입니다.\
defaultValues와 달리, value option을 이용하면  form에 값이 실제로 입력됩니다.

```tsx
register('firstName', { value: 'bill' })
```

### shouldUnregister

form이 unmount되면 입력값이 유지되는지 설정하는 option입니다.
만약 true로 설정과 unmount시키고 나서 submit하면, 값이 전달되지 않는다.

참고: 이 prop은 useFieldArray와 함께 사용할 때 unregister 함수가 입력이 마운트/다시 마운트되고 재정렬된 후에 호출되므로 피해야 합니다.

```tsx
<input
  {...register("test", {
    shouldUnregister: true,
  })}
/>
```

### deps

register로 등록된 form만들 추적한다.\
의존 입력에 대해 유효성 검사가 트리거됩니다.\
trigger가 아니라 register API에만 해당됩니다.

```tsx
<input
  {...register("test", {
    deps: ['inputA', 'inputB'],
  })}
/>
```
