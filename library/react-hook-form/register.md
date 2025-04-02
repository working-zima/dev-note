# register

```tsx
(name: string, RegisterOptions?) => ({ onChange, onBlur, name, ref });
```

이 메서드를 사용하면 입력 또는 선택 요소를 등록하고 React Hook Form에 유효성 검사 규칙을 적용할 수 있습니다.\
유효성 검사 규칙은 모두 HTML 표준을 기반으로 하며 사용자 정의 유효성 검사 방법도 허용합니다.

`register` 함수에 입력 요소의 이름을 전달하면 다음과 같은 속성들을 반환합니다

| Props      | 타입             | 설명                                              |
| ---------- | ---------------- | ------------------------------------------------- |
| `onChange` | `ChangeHandler`  | 입력 변화 이벤트를 구독합니다.                    |
| `onBlur`   | `ChangeHandler`  | 입력 블러 이벤트를 구독합니다.                    |
| `ref`      | `React.Ref<any>` | 해당 입력을 Hook Form에 등록하기 위한 참조입니다. |
| `name`     | `string`         | 등록 중인 입력의 이름입니다.                      |

## 입력 이름 예시와 제출 결과

| 입력 이름                      | 제출 결과                            |
| ------------------------------ | ------------------------------------ |
| `register("firstName")`        | `{ firstName: 'value' }`             |
| `register("name.firstName")`   | `{ name: { firstName: 'value' } }`   |
| `register("name.firstName.0")` | `{ name: { firstName: ['value'] } }` |

```tsx
const { register } = useForm();

console.log(register("test"));
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

## Register 옵션

### required

`required: true` 인 경우 입력값이 있어야하며, 없을 경우 error를 나타내는 문자열 메시지를 할당할 수 있습니다.

```tsx
// validation
<input {...register("test", { required: true })} />

// validation + 에러 메시지
<input {...register("test", { required: "필수 항목입니다." })} />
```

### `maxLength` / `minLength`

문자열 길이 제한을 설정합니다.

```js
// validation
<input {...register("test", { maxLength: 2 })} />

// validation + 에러 메시지
<input {...register("test", {
  maxLength: {
    value: 10,
    message: "10자 이하로 입력해주세요."
  }
})} />

// validation
<input {...register("test", { minLength: 1 })} />

// validation + 에러 메시지
<input {...register("test", {
  minLength: {
    value: 2,
    message: "2자 이상 입력해주세요."
  }
})} />
```

### `max` / `min`

숫자 최대/최소 값을 설정합니다.

```js
// validation
<input type="number" {...register("test", { max: 3 })} />

// validation + 에러 메시지
<input
  type="number"
  {...register("test", {
    max: {
      value: 100,
      message: "100 이하 숫자만 입력 가능합니다."
    }
  })}
/>

// validation
<input type="number" {...register("test", { min: 3 })} />

// validation + 에러 메시지
<input
  type="number"
  {...register("test", {
    min: {
      value: 10,
      message: "10 이상 숫자만 입력 가능합니다."
    }
  })}
/>
```

### pattern

자바스크립트 정규식을 적용시키는 option 입니다.\

```tsx
// validation
<input
  {...register("test", {
    pattern: /[A-Za-z]{3}/,
  })}
/>

// validation + 에러 메시지
<input
  {...register("username", {
    pattern: {
      value: /^[A-Za-z]{3,}$/,
      message: "3자리 이상의 영문자만 입력 가능합니다."
    }
  })}
/>
```

### validate

콜백함수를 전달하여, required 속성에 포함된 다른 유효성 검사 규칙에 의존하지 않고 독립적으로 실행됩니다.

```tsx
// validation
<input
  {...register("test", {
    validate: (value, formValues) => value === '1'
  })}
/>

// validation + 에러 메시지
<input
  {...register("password", {
    validate: value =>
      value === "secret" || "비밀번호가 일치하지 않습니다."
  })}
/>

// 여러 함수 객체로도 사용 가능
<input
  {...register("value", {
    validate: {
      isPositive: v => parseFloat(v) > 0 || "양수만 입력 가능",
      isEven: v => parseInt(v) % 2 === 0 || "짝수만 입력 가능"
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
<input type="number" {...register("price", { valueAsNumber: true })} />
<input type="date" {...register("birth", { valueAsDate: true })} />
<input type="text" {...register("id", { setValueAs: v => v.trim() })} />
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
    setValueAs: (v) => parseInt(v),
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
    disabled: true,
  })}
/>
```

### `onChange`, `onBlur` (옵션으로 커스텀 이벤트 추가)

```js
register("firstName", {
  onChange: (e) => console.log(e),
  onBlur: (e) => console.log(e),
});
```

### value

첫 렌더링시, `form` 초기 입력값을 가지도록 설정하는 `option`입니다.\
`defaultValues`와 달리, `value` option을 이용하면 `form`에 값이 실제로 입력됩니다.

```tsx
// 초기 값
register("firstName", { value: "bill" });
```

### shouldUnregister

form이 unmount되면 입력값이 유지되는지 설정하는 option입니다.
만약 true로 설정과 unmount시키고 나서 submit하면, 값이 전달되지 않는다.

참고: 이 prop은 useFieldArray와 함께 사용할 때 unregister 함수가 입력이 마운트/다시 마운트되고 재정렬된 후에 호출되므로 피해야 합니다.

```tsx
// 언마운트 시 해제
<input
  {...register("test", {
    shouldUnregister: true,
  })}
/>
```

### deps

`register`로 등록된 `form`만 추적한다.\
의존 입력에 대해 유효성 검사가 trigger 됩니다.\
trigger가 아니라 register API에만 해당됩니다.

```tsx
// 의존 관계
<input
  {...register("test", {
    deps: ["inputA", "inputB"],
  })}
/>
```

## 규칙

- `name`은 반드시 유일해야 합니다. (radio/checkbox 제외)
- `dot(.)` 구문만 지원됩니다. (`bracket[]` 구문 불가)

```js
register("test.0.firstName"); // ✅
register("test[0]firstName"); // ❌
```

- `disabled`된 input은 제출 결과에서 제외됩니다. 읽기 전용으로만 하려면 `readOnly` 사용 권장
- 이름이 매 렌더마다 바뀌면 새로 등록됩니다. 이름을 고정하는 것이 좋습니다.
- 언마운트 시 `unregister`를 명시적으로 호출해야 합니다.
- 다음 방식으로는 등록 옵션을 제거할 수 없습니다.

```js
register("test", {}); // ❌
register("test", undefined); // ❌
register("test", { required: false }); // ✅
```

## 전체 예제

```js
import * as React from "react";
import { useForm } from "react-hook-form";

export default function App() {
  const { register, handleSubmit } = useForm({
    defaultValues: {
      firstName: "",
      lastName: "",
      category: "",
      checkbox: [],
      radio: "",
    },
  });

  return (
    <form onSubmit={handleSubmit(console.log)}>
      <input
        {...register("firstName", { required: true })}
        placeholder="First name"
      />
      <input
        {...register("lastName", { minLength: 2 })}
        placeholder="Last name"
      />

      <select {...register("category")}>
        <option value="">Select...</option>
        <option value="A">Category A</option>
        <option value="B">Category B</option>
      </select>

      <input {...register("checkbox")} type="checkbox" value="A" />
      <input {...register("checkbox")} type="checkbox" value="B" />
      <input {...register("checkbox")} type="checkbox" value="C" />

      <input {...register("radio")} type="radio" value="A" />
      <input {...register("radio")} type="radio" value="B" />
      <input {...register("radio")} type="radio" value="C" />

      <input type="submit" />
    </form>
  );
}
```

## Tips: 커스텀 Register 및 ref 처리

React Hook Form은 `register`를 기본으로 제공하지만, **커스텀 입력 컴포넌트**나 **제어 컴포넌트**를 다룰 때는 약간의 주의가 필요합니다.

### 🛠️ Custom Register (커스텀 방식 등록)

`useEffect`에서 `register`를 호출하거나, `setValue`를 직접 사용하여 가상 입력(virtual input)처럼 다룰 수 있습니다.  
**제어 컴포넌트(controlled component)**의 경우, `useController` 또는 `Controller` 컴포넌트를 사용하는 것을 권장합니다.

#### 📌 예시: 수동 등록 + setValue

```ts
register("firstName", { required: true, minLength: 8 });

<TextInput onTextChange={(value) => setValue("lastName", value)} />;
```

### ref, innerRef, inputRef 처리법

**ref가 제대로 연결되지 않은 커스텀 컴포넌트**는 아래처럼 직접 등록 정보를 나눠서 넘겨줘야 합니다.

#### ❌ 잘못된 예시 (ref 연결되지 않음)

```ts
<TextInput {...register("test")} />
```

#### 올바른 예시

```ts
const firstName = register("firstName", { required: true });

<TextInput
  name={firstName.name}
  onChange={firstName.onChange}
  onBlur={firstName.onBlur}
  inputRef={firstName.ref} // 또는 innerRef 등 커스텀 ref prop에 연결
/>;
```

### ref를 직접 전달하는 방법 (React.forwardRef)

커스텀 컴포넌트에서 `ref`를 직접 받을 수 있도록 `forwardRef`를 사용하는 방법입니다.

```tsx
const Select = React.forwardRef(({ onChange, onBlur, name, label }, ref) => (
  <select name={name} ref={ref} onChange={onChange} onBlur={onBlur}>
    <option value="20">20</option>
    <option value="30">30</option>
  </select>
));
```

위와 같이 `ref`를 props로 받아 `input` 또는 `select` 요소에 정확히 연결하면  
`register`에서 제공하는 `ref`를 제대로 전달할 수 있습니다.

### 요약

- 커스텀 입력은 `useController`, `Controller` 사용을 권장
- 커스텀 이벤트 핸들러를 사용할 경우 `setValue`로 값 갱신
- `ref`가 연결되지 않으면 `register().ref`를 수동으로 전달
- `React.forwardRef`로 `ref`를 정확히 연결하면 가장 이상적
