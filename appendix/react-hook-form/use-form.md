# useForm

useForm은 폼 관리를 쉽게 도와주는 커스텀 훅(custom hook)입니다.\
이 훅은 선택적 인수로 객체 하나를 받습니다.

```tsx
useForm({props})
```

## Generic props

- `mode`: 제출 전 유효성 검사 전략.
- `reValidateMode`: 제출 후 유효성 재검사 전략.
- `defaultValues`: 폼의 기본값.
- `values`: 폼 값을 업데이트하는 반응형 값.
- `errors`: 폼 오류를 업데이트하는 반응형 오류.
- `resetOptions`: 새로운 폼 값을 업데이트할 때 폼 상태를 리셋하는 옵션.
- `criteriaMode`: 모든 유효성 검사 오류를 보여줄지, 아니면 하나씩만 보여줄지.
- `shouldFocusError`: 내장된 포커스 관리를 활성화하거나 비활성화.
- `delayError`: 오류가 즉시 나타나는 것을 지연.
- `shouldUseNativeValidation`: 브라우저 내장 폼 제약 API 사용.
- `shouldUnregister`: 마운트 해제 후 입력 언레지스터를 활성화하거나 비활성화.

## Schema validation props

- `resolver`: 선호하는 스키마 유효성 검사 라이브러리와 통합.
- `context`: 스키마 유효성 검사를 위해 제공되는 컨텍스트 객체.

## Props

### mode: onChange | onBlur | onSubmit | onTouched | all = 'onSubmit'

form의 유효성 검사를 어느 동작때 시행할지 설정하는 props 입니다.\
유효성 검사는 handleSubmit 함수를 호출하여 트리거되는 onSubmit 이벤트 중에 발생합니다.

이름 | 타입 | 설명
:-: | :-: | :-:
`onSubmit`(default) | string | 유효성 검사가 제출 이벤트에서 트리거되며, 입력란은 자신을 다시 유효성 검사하기 위해 onChange 이벤트 리스너를 연결합니다.
`onBlur` | string | 유효성 검사가 blur 이벤트에서 트리거됩니다.
`onChange` | string | 유효성 검사가 각 입력 요소의 change 이벤트에서 트리거되어 다시 렌더링됩니다. 경고: 이는 종종 성능에 상당한 영향을 미칩니다.
`onTouched` | string | 유효성 검사가 초기 blur 이벤트에서 트리거되며, 그 후에는 모든 change 이벤트에서 트리거됩니다.
`all` | string | 유효성 검사가 blur 및 change 이벤트에서 모두 트리거됩니다.

```tsx
useForm({mode: 'onSubmit'});
```

### reValidateMode: onChange(default) | onBlur | onSubmit = 'onChange'

사용자가 폼을 제출한 후(제출 이벤트 및 handleSubmit 함수 실행 후) 오류가 있는 입력란이 다시 유효성을 검사할 때 어느 동작에 시행할지 설정하는 props 입니다.\
기본적으로 재검증은 입력 변경 이벤트 중에 발생합니다.

Note: Controller와 함께 사용할 때, onBlur를 렌더 프롭(render prop)과 함께 연결하는 것을 확인하세요.

```tsx
useForm({reValidateMode: 'onChange'});
```

### defaultValues: `FieldValues | () => Promise<FieldValues>`

전체 폼을 기본값을 세팅할때 설정하는 props 입니다.\
동기적 및 비동기적으로 기본값을 할당할 수 있습니다.\
입력의 기본값은 `defaultValue` 또는 `defaultChecked`를 사용하여 설정할 수 있지만 (공식 React 문서에 자세히 설명되어 있음), 전체 폼에 대해 `defaultValues`를 사용하는 것이 권장됩니다.

```tsx
useForm({
  defaultValues: {
    firstName: '',
    lastName: ''
  }
})

// set default value async
useForm({
  defaultValues: async () => fetch('/api-endpoint');
})
```

#### 주의사항

- 제어 컴포넌트의 기본 상태와 충돌하기 때문에 기본값으로 undefined를 제공하는 것은 피해야 합니다.
- defaultValues는 캐시됩니다. 이를 재설정하려면 reset API를 사용하세요.
- defaultValues는 기본적으로 제출 결과에 포함됩니다.
- defaultValues로 Moment 또는 Luxon과 같은 프로토타입 메서드를 포함하는 사용자 지정 객체를 사용하는 것을 피하는 것이 권장됩니다.
- 폼 데이터를 포함하는 다른 옵션도 있습니다:

```tsx
<input {...register("hidden", { value: "data" })} type="hidden" />

// onSubmit에서 데이터 포함
const onSubmit = (data) => {
  const output = {
    ...data,
    others: "others",
  }
}
```

### values: FieldValues

첫 렌더링시, form 초기 입력값을 가지도록 설정하는 option 입니다.\
values 속성은 변경 사항에 반응하여 폼 값을 업데이트합니다.\
이는 폼이 외부 상태나 서버 데이터에 의해 업데이트되어야 할 때 유용합니다.

```tsx
// 동기적인 기본값 설정
function App({ values }) {
  useForm({
    values, // values props가 업데이트될 때마다 업데이트됩니다.
  })
}

// 비동기적인 기본값 설정
function App() {
  const values = useFetch("/api")

  useForm({
    defaultValues: {
      firstName: "",
      lastName: "",
    },
    values, // values가 반환될 때마다 업데이트됩니다.
  })
}
```

### errors: FieldErrors

errors 속성은 변경 사항에 반응하여 서버 오류 상태를 업데이트합니다.\
이는 폼이 외부 서버에서 반환된 오류로 업데이트되어야 할 때 유용합니다.

```tsx
function App() {
  const { errors, data } = useFetch("/api")

  useForm({
    errors, // errors가 반환될 때마다 업데이트됩니다.
  })
}
```

### resetOptions: KeepStateOptions

이 속성은 value 업데이트 동작과 관련이 있습니다.\
`values` 또는 `defaultValues`가 업데이트되면 내부적으로 reset API가 호출됩니다.\
값 또는 `defaultValues가` 비동기적으로 업데이트된 후 원하는 동작을 명시하는 것이 중요합니다.\
구성 옵션 자체는 reset 메서드의 옵션에 대한 참조입니다.

```tsx
// 기본적으로 비동기적으로 value 또는 defaultValues가 업데이트되면 폼 값이 재설정됩니다.
useForm({ values })
useForm({ defaultValues: async () => await fetch() })

// 동작 구성을 위한 옵션
// 예: 사용자 상호작용/더티 값 유지 및 사용자 오류 제거하지 않음
useForm({
  values,
  resetOptions: {
    keepDirtyValues: true, // 사용자 상호작용 입력값이 유지됩니다.
    keepErrors: true, // 값 업데이트와 함께 입력 오류가 유지됩니다.
  },
})
```

### context: object

유효성 검사 라이브러리인 Yup 의 context로 사용되는 props 입니다.\
context 객체는 가변적이며 `resolver`의 두 번째 인수 또는 Yup 유효성 검사의 컨텍스트 객체에 주입됩니다.

### criteriaMode: firstError | all

`firstError` (default): 각 필드의 첫 번째 오류만 수집됩니다.
`all`: 각 필드의 모든 오류가 수집됩니다.

### shouldFocusError: boolean = true

유효성 검사를 시행하고 나서, 에러가 발생한 form의 focus를 시행할지 설정하는 props 입니다.\
focus는 register를 등록한 순서(위에서 아래로) 진행됩니다.

- `true` (default): 사용자가 유효성 검사에 실패하는 양식을 제출하면 오류가 있는 첫 번째 필드에 포커스가 설정됩니다.

#### 참고

- 참조와 함께 등록된 필드에만 작동합니다. 사용자 지정 등록 입력은 적용되지 않습니다.\
예: register('test') // 작동하지 않음
- 포커스 순서는 등록 순서에 기반합니다.

### delayError: number

에러메시지를 표시하기까지 지연시간을 설정하는 props 입니다.\
이 구성은 지정된 밀리초 단위로 오류 상태를 최종 사용자에게 표시하는 것을 지연시킵니다.\
사용자가 오류 입력을 수정하면 오류가 즉시 제거되고  바로 사라집니다.

### shouldUnregister: boolean = false

기본적으로 입력이 제거될 때 입력 값은 유지됩니다.\
그러나 입력이 마운트 해제될 때 입력을 등록 해제하려면 shouldUnregister를 true로 설정할 수 있습니다.

- 이는 하위 수준 구성을 재정의하는 전역 구성입니다.\
개별 동작을 설정하려면 useForm이 아니라 구성 요소 또는 훅 수준에서 구성을 설정하세요.

- 기본적으로 shouldUnregister: false는 마운트 해제된 필드가 내장 유효성 검사에 의해 유효성이 검사되지 않음을 의미합니다.

- shouldUnregister를 useForm 레벨에서 true로 설정하면 defaultValues가 제출 결과와 병합되지 않습니다.

- shouldUnregister: true로 설정하면 폼이 원시 폼과 더 유사하게 동작합니다.
  - 폼 값은 입력 자체에 저장됩니다.
  - 입력을 마운트 해제하면 해당 값이 제거됩니다.
  - 숨겨진 입력은 숨김 데이터를 저장하기 위해 hidden 속성을 사용해야 합니다.
  - 등록된 입력만 제출 데이터로 포함됩니다.
  - 마운트 해제된 입력은 useForm 또는 useWatch의 useEffect에서 알림을 받아야만 훅 폼이 입력이 DOM에서 마운트 해제되었는지 확인합니다.

```tsx
const NotWork = () => {
  const [show, setShow] = React.useState(false)
  // ❌ 알림을 받지 않음, unregister를 호출해야 함
  return show && <input {...register("test")} />
}

const Work = ({ control }) => {
  const { show } = useWatch({ control })
  // ✅ useEffect에서 알림 받음
  return show && <input {...register("test1")} />
}

const App = () => {
  const [show, setShow] = React.useState(false)
  const { control } = useForm({ shouldUnregister: true })
  return (
    <div>
      // ✅ useForm의 useEffect에서 알림 받음
      {show && <input {...register("test2")} />}
      <NotWork />
      <Work control={control} />
    </div>
  )
}
```

### shouldUseNativeValidation: boolean = false

이 구성은 브라우저의 기본 유효성 검사를 활성화합니다.\
또한 입력 요소를 스타일링하기 쉽게 만드는 :valid와 :invalid CSS 선택자를 활성화합니다.\
클라이언트 측 유효성 검사가 비활성화되어도 이러한 선택자를 여전히 사용할 수 있습니다.

- onSubmit 및 onChange 모드에서만 작동합니다. reportValidity 실행시 오류 입력에 포커스됩니다.
- 각 등록된 필드의 유효성 검사 메시지는 기본 표시를 위해 문자열이어야 합니다.
- 이 기능은 실제 DOM 참조와 연결된 register API 및 useController/Controller와 함께만 작동합니다.

```tsx
import { useForm } from "react-hook-form"

export default function App() {
  const { register, handleSubmit } = useForm({
    shouldUseNativeValidation: true,
  })
  const onSubmit = async (data) => {
    console.log(data)
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input
        {...register("firstName", {
          required: "Please enter your first name.",
        })} // 사용자 정의 메시지
      />
      <input type="submit" />
    </form>
  )
}
```

### resolver: Resolver

resolver는 유효성 검사 라이브러리를 도와주는 props 입니다.\
Yup, Zod, Joi, Vest, Ajv 등과 같은 외부 유효성 검사 라이브러리를 사용할 수 있습니다.\
목표는 선호하는 유효성 검사 라이브러리를 매끄럽게 통합할 수 있도록 하는 것입니다.\
라이브러리를 사용하지 않는 경우에도 폼을 유효성 검사하는 데 필요한 로직을 직접 작성할 수 있습니다.

#### Props

Name | Type | Description
:-: | :-: | :-:
values | `object` | 전체 폼 값이 들어 있는 객체입니다.
context | `object` | useForm 구성에 제공할 수 있는 컨텍스트 객체입니다. 각 재랜더링마다 변경될 수 있는 가변 객체입니다.
options | `{\"criteriaMode": "string", "fields": "object", "names": "string[]"}` | useForm에서 검증된 필드, 이름 및 criteriaMode에 대한 정보를 포함하는 옵션 객체입니다.

#### RULES

- 스키마 유효성 검사는 필드 수준의 오류 보고에 중점을 둡니다. 부모 수준의 오류 확인은 직접적인 부모 수준으로 제한되며, 그룹 체크박스와 같은 구성 요소에 적용됩니다.
- 이 함수는 캐시됩니다.
- 사용자 상호 작용 중에 입력의 재유효성 검사는 한 번에 한 번씩만 발생합니다. 라이브러리 자체가 오류 객체를 평가하여 필요한 경우 다시 렌더링을 트리거합니다.
- resolver는 내장 유효성 검사기 (예: required, min 등)와 함께 사용할 수 없습니다.
- 사용자 정의 resolver를 작성할 때:
  - 값 및 오류 속성이 모두 포함된 객체를 반환하는지 확인하세요. 그들의 기본값은 빈 객체여야 합니다. 예: {}.
  - 오류 객체의 키는 필드의 이름 값과 일치해야 합니다.

## Return

- [register](./register.md)
- [control](./control.md)

## 참고 자료

- [Get Started](https://react-hook-form.com/get-started#Registerfields)
- [React Hook Form 라이브러리 사용법](https://www.daleseo.com/react-hook-form/)
- [공부 | React Hook Form 정리](https://2mojurmoyang.tistory.com/221)
