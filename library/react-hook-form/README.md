# React Hook Form

`react-hook-form` 패키지로 부터 `useForm()` 훅(hook) 함수를 불러 사용합니다.

## API

- [useForm](./use-form.md)
  - [register](./register.md)
  - [control](./control.md)
- [useController](./use-controller.md)

## 시작하기

React Hook Form을 사용하면 간단하게 폼 유효성 검사를 구현할 수 있습니다.

## 설치

React Hook Form 설치는 명령어 한 줄이면 완료되며, 바로 사용할 수 있습니다.

```bash
npm install react-hook-form
```

## React Hook Form 함수 사용법 요약

### `register("name")`

"name"이라는 이름으로 이 input을 등록해서 값 추적하고 검증

#### register 사용 예시

```tsx
<input {...register("name")} />
```

### `watch("email")`

"email" 입력값이 지금 뭔지 실시간으로 확인

#### watch 사용 예시

```ts
const currentEmail = watch("email");
```

### `handleSubmit(onSubmit)`

폼을 제출할 때 유효성 검사를 먼저 하고, 통과하면 onSubmit을 실행

#### handleSubmit 사용 예시

```tsx
<form onSubmit={handleSubmit(onSubmit)} />
```

### `formState.errors["age"]`

"age" 필드에 오류 확인

#### formState.errors 사용 예시

```tsx
{
  errors.age && <span>나이는 필수입니다</span>;
}
```

### `setValue("username", "kim123")`

"username" 값을 직접 "kim123"으로 바꿔줌

#### setValue 사용 예시

```ts
setValue("username", "kim123");
```

### `control`

Controller 컴포넌트를 쓸 때 폼 상태를 연결해주는 핵심 객체

#### control 사용 예시

```tsx
<Controller control={control} name="category" ... />
```

### `useController({ name: "nickname", control })`

nickname이라는 필드를 제어할 수 있는 Hook 버전의 Controller

#### useController 사용 예시

```ts
const { field } = useController({ name: "nickname", control });
```

### `errors["fieldName"]?.message`

유효성 검사 실패 시 사용자에게 보여줄 에러 메시지를 꺼냄

#### errors["fieldName"]?.message 사용 예시

```tsx
{
  errors.email?.message && <p>{errors.email.message}</p>;
}
```

### `defaultValues`

useForm 훅에서 각 필드의 기본값을 지정해주는 옵션

#### defaultValues 사용 예시

```ts
useForm({ defaultValues: { name: "kim" } });
```

### `resolver`

yup이나 zod 같은 스키마 기반 유효성 검사기를 연결

#### resolver 사용 예시

```ts
useForm({ resolver: yupResolver(schema) });
```

> 이 문서는 React Hook Form을 처음 배우는 사람이 "이 함수는 이렇게 쓰는 거구나"를 직관적으로 이해하도록 돕기 위한 치트시트

## 예제

다음 코드는 React Hook Form의 기본적인 사용법을 보여줍니다.

```tsx
import { useForm, SubmitHandler } from "react-hook-form";

type Inputs = {
  example: string;
  exampleRequired: string;
};

export default function App() {
  const {
    register,
    handleSubmit,
    watch,
    formState: { errors },
  } = useForm<Inputs>();

  const onSubmit: SubmitHandler<Inputs> = (data) => console.log(data);

  console.log(watch("example")); // "example" input의 값을 관찰합니다

  return (
    // "handleSubmit"은 입력값을 먼저 검증한 후 onSubmit을 실행합니다
    <form onSubmit={handleSubmit(onSubmit)}>
      {/* register 함수를 호출해 input을 hook에 등록합니다 */}
      <input defaultValue="test" {...register("example")} />

      {/* 필수 입력 필드 설정 */}
      <input {...register("exampleRequired", { required: true })} />

      {/* 유효성 검사 실패 시 에러 메시지 출력 */}
      {errors.exampleRequired && <span>This field is required</span>}

      <input type="submit" />
    </form>
  );
}
```

## 필드 등록 (Register fields)

React Hook Form의 핵심 개념 중 하나는 컴포넌트를 hook에 등록(register) 하는 것입니다.\
이를 통해 해당 input의 값이 유효성 검사와 제출 처리에 사용될 수 있게 됩니다.

> 참고: 모든 필드는 `name` 속성을 가져야 하며, 이것이 key로 사용됩니다.

```tsx
import ReactDOM from "react-dom";
import { useForm, SubmitHandler } from "react-hook-form";

enum GenderEnum {
  female = "female",
  male = "male",
  other = "other",
}

interface IFormInput {
  firstName: string;
  gender: GenderEnum;
}

export default function App() {
  const { register, handleSubmit } = useForm<IFormInput>();
  const onSubmit: SubmitHandler<IFormInput> = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <label>First Name</label>
      <input {...register("firstName")} />

      <label>Gender Selection</label>
      <select {...register("gender")}>
        <option value="female">female</option>
        <option value="male">male</option>
        <option value="other">other</option>
      </select>

      <input type="submit" />
    </form>
  );
}
```

## 유효성 검사 적용 (Apply validation)

React Hook Form은 기존 HTML의 유효성 검사 방식과 잘 맞도록 설계되어 있어서, 폼 유효성 검사를 매우 쉽게 구현할 수 있습니다.

지원하는 유효성 검사 규칙은 다음과 같습니다:

- required
- min / max
- minLength / maxLength
- pattern
- validate

> 각 규칙에 대한 자세한 설명은 `register` 섹션에서 확인할 수 있습니다.

```tsx
import { useForm, SubmitHandler } from "react-hook-form";

interface IFormInput {
  firstName: string;
  lastName: string;
  age: number;
}

export default function App() {
  const { register, handleSubmit } = useForm<IFormInput>();
  const onSubmit: SubmitHandler<IFormInput> = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      {/* 필수 입력값 + 최대 길이 20자 */}
      <input {...register("firstName", { required: true, maxLength: 20 })} />

      {/* 정규표현식으로 영문자만 허용 */}
      <input {...register("lastName", { pattern: /^[A-Za-z]+$/i })} />

      {/* 숫자 범위 설정: 최소 18세, 최대 99세 */}
      <input type="number" {...register("age", { min: 18, max: 99 })} />

      <input type="submit" />
    </form>
  );
}
```

## 기존 폼 통합 (Integrating an existing form)

React Hook Form은 기존에 작성된 폼과 쉽게 통합할 수 있습니다.\
중요한 점은 input의 `ref`를 등록하고, 관련 props를 넘겨주는 것입니다.

```tsx
import { Path, useForm, UseFormRegister, SubmitHandler } from "react-hook-form";

interface IFormValues {
  "First Name": string;
  Age: number;
}

type InputProps = {
  label: Path<IFormValues>;
  register: UseFormRegister<IFormValues>;
  required: boolean;
};

// 기존 Input 컴포넌트를 예시로 구성
const Input = ({ label, register, required }: InputProps) => (
  <>
    <label>{label}</label>
    <input {...register(label, { required })} />
  </>
);

// React.forwardRef를 통해 ref도 전달 가능
const Select = React.forwardRef<
  HTMLSelectElement,
  { label: string } & ReturnType<UseFormRegister<IFormValues>>
>(({ onChange, onBlur, name, label }, ref) => (
  <>
    <label>{label}</label>
    <select name={name} ref={ref} onChange={onChange} onBlur={onBlur}>
      <option value="20">20</option>
      <option value="30">30</option>
    </select>
  </>
));

const App = () => {
  const { register, handleSubmit } = useForm<IFormValues>();

  const onSubmit: SubmitHandler<IFormValues> = (data) => {
    alert(JSON.stringify(data));
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Input label="First Name" register={register} required />
      <Select label="Age" {...register("Age")} />
      <input type="submit" />
    </form>
  );
};
```

## UI 라이브러리 통합 (Integrating with UI libraries)

React Hook Form은 외부 UI 컴포넌트 라이브러리와 쉽게 통합할 수 있습니다.\
만약 해당 UI 컴포넌트가 `ref`를 직접 노출하지 않는다면, `Controller` 컴포넌트를 사용해서 등록 및 값을 제어할 수 있습니다.

```tsx
import Select from "react-select";
import { useForm, Controller, SubmitHandler } from "react-hook-form";
import { Input } from "@material-ui/core";

interface IFormInput {
  firstName: string;
  lastName: string;
  iceCreamType: { label: string; value: string };
}

const App = () => {
  const { control, handleSubmit } = useForm({
    defaultValues: {
      firstName: "",
      lastName: "",
      iceCreamType: {},
    },
  });

  const onSubmit: SubmitHandler<IFormInput> = (data) => {
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      {/* Material UI의 Input 사용 */}
      <Controller
        name="firstName"
        control={control}
        render={({ field }) => <Input {...field} />}
      />

      {/* React Select 사용 */}
      <Controller
        name="iceCreamType"
        control={control}
        render={({ field }) => (
          <Select
            {...field}
            options={[
              { value: "chocolate", label: "Chocolate" },
              { value: "strawberry", label: "Strawberry" },
              { value: "vanilla", label: "Vanilla" },
            ]}
          />
        )}
      />

      <input type="submit" />
    </form>
  );
};
```

## Controlled Input 통합 (Integrating Controlled Inputs)

React Hook Form은 기본적으로 uncontrolled 컴포넌트와 네이티브 HTML input을 선호합니다.\
하지만 현실적으로는 shadcn/ui, React-Select, Ant Design, MUI 같은 controlled 컴포넌트도 많이 사용되죠.

이럴 때는 `Controller` 컴포넌트를 사용하면 쉽게 통합할 수 있습니다.

### Component API 방식

```tsx
import { useForm, Controller, SubmitHandler } from "react-hook-form";
import { TextField, Checkbox } from "@material-ui/core";

interface IFormInputs {
  TextField: string;
  MyCheckbox: boolean;
}

function App() {
  const { handleSubmit, control, reset } = useForm<IFormInputs>({
    defaultValues: {
      MyCheckbox: false,
    },
  });
  const onSubmit: SubmitHandler<IFormInputs> = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Controller
        name="MyCheckbox"
        control={control}
        rules={{ required: true }}
        render={({ field }) => <Checkbox {...field} />}
      />
      <input type="submit" />
    </form>
  );
}
```

### Hooks API 방식

```tsx
import * as React from "react";
import { useForm, useController, UseControllerProps } from "react-hook-form";

type FormValues = {
  FirstName: string;
};

function Input(props: UseControllerProps<FormValues>) {
  const { field, fieldState } = useController(props);

  return (
    <div>
      <input {...field} placeholder={props.name} />
      <p>{fieldState.isTouched && "Touched"}</p>
      <p>{fieldState.isDirty && "Dirty"}</p>
      <p>{fieldState.invalid ? "invalid" : "valid"}</p>
    </div>
  );
}

export default function App() {
  const { handleSubmit, control } = useForm<FormValues>({
    defaultValues: {
      FirstName: "",
    },
    mode: "onChange",
  });
  const onSubmit = (data: FormValues) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Input control={control} name="FirstName" rules={{ required: true }} />
      <input type="submit" />
    </form>
  );
}
```

## 글로벌 상태 통합 (Integrating with global state)

React Hook Form은 상태 관리 라이브러리 없이도 작동하지만, 필요한 경우 쉽게 연동할 수 있습니다.\
예를 들어 Redux와도 통합이 가능합니다.

```tsx
import { useForm } from "react-hook-form";
import { connect } from "react-redux";
import updateAction from "./actions";

function App(props) {
  const { register, handleSubmit, setValue } = useForm({
    defaultValues: {
      firstName: "",
      lastName: "",
    },
  });

  // 데이터를 Redux 스토어에 저장
  const onSubmit = (data) => props.updateAction(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register("firstName")} />
      <input {...register("lastName")} />
      <input type="submit" />
    </form>
  );
}

// Redux와 컴포넌트 연결
export default connect(({ firstName, lastName }) => ({ firstName, lastName }), {
  updateAction,
})(App);
```

## 서비스와의 통합 (Integrating with services)

React Hook Form은 폼 제출 처리 기능이 내장되어 있어, API 엔드포인트나 외부 서비스로 데이터를 쉽게 전송할 수 있습니다.

`<Form />` 컴포넌트를 사용하면, 폼 데이터를 `FormData` 형식으로 자동 전송할 수 있어요.

```tsx
import { Form } from "react-hook-form";
import { useForm } from "react-hook-form";

function App() {
  const { register, control } = useForm();

  return (
    <Form
      action="/api/save" // FormData를 포함한 POST 요청
      // encType={'application/json'} 로 전환 가능 (JSON 전송용)
      onSuccess={() => {
        alert("Your application is updated.");
      }}
      onError={() => {
        alert("Submission has failed.");
      }}
      control={control}
    >
      <input {...register("firstName", { required: true })} />
      <input {...register("lastName", { required: true })} />
      <button>Submit</button>
    </Form>
  );
}
```

## 스키마 기반 유효성 검사 (Schema Validation)

React Hook Form은 다음과 같은 라이브러리와의 스키마 기반 유효성 검사를 지원합니다:

- Yup
- Zod
- Superstruct
- Joi

`useForm` 훅의 설정에 스키마 리졸버를 넘기면, 입력값을 스키마에 따라 자동 검증할 수 있어요.

### 1단계: Yup 설치

```bash
npm install @hookform/resolvers yup
```

### 2단계: Yup 스키마 작성 + `useForm`과 통합

```tsx
import { useForm } from "react-hook-form";
import { yupResolver } from "@hookform/resolvers/yup";
import * as yup from "yup";

// 유효성 검사 스키마 정의
const schema = yup
  .object({
    firstName: yup.string().required(),
    age: yup.number().positive().integer().required(),
  })
  .required();

export default function App() {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm({
    resolver: yupResolver(schema),
  });

  const onSubmit = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register("firstName")} />
      <p>{errors.firstName?.message}</p>

      <input {...register("age")} />
      <p>{errors.age?.message}</p>

      <input type="submit" />
    </form>
  );
}
```

## React Native 통합

React Hook Form은 React Native 환경에서도 동일한 성능 향상과 간결함을 제공합니다.\
입력 컴포넌트를 감싸기 위해 `Controller`를 사용하면 쉽게 통합할 수 있어요.

```tsx
import { Text, View, TextInput, Button, Alert } from "react-native";
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
      {/* firstName 필드 */}
      <Controller
        control={control}
        rules={{ required: true }}
        render={({ field: { onChange, onBlur, value } }) => (
          <TextInput
            placeholder="First name"
            onBlur={onBlur}
            onChangeText={onChange}
            value={value}
          />
        )}
        name="firstName"
      />
      {errors.firstName && <Text>This is required.</Text>}

      {/* lastName 필드 */}
      <Controller
        control={control}
        rules={{ maxLength: 100 }}
        render={({ field: { onChange, onBlur, value } }) => (
          <TextInput
            placeholder="Last name"
            onBlur={onBlur}
            onChangeText={onChange}
            value={value}
          />
        )}
        name="lastName"
      />

      <Button title="Submit" onPress={handleSubmit(onSubmit)} />
    </View>
  );
}
```

## TypeScript

React Hook Form은 TypeScript로 작성되어 있으며, `useForm<FormData>()`와 같은 방식으로 폼 필드의 타입을 정의하면 자동 타입 추론과 빌드 타임 오류 탐지가 가능합니다.

```tsx
import * as React from "react";
import { useForm } from "react-hook-form";

type FormData = {
  firstName: string;
  lastName: string;
};

export default function App() {
  const {
    register,
    setValue,
    handleSubmit,
    formState: { errors },
  } = useForm<FormData>();

  const onSubmit = handleSubmit((data) => console.log(data));
  // firstName과 lastName은 올바른 타입으로 인식됨

  return (
    <form onSubmit={onSubmit}>
      <label>First Name</label>
      <input {...register("firstName")} />

      <label>Last Name</label>
      <input {...register("lastName")} />

      <button
        type="button"
        onClick={() => {
          setValue("lastName", "luo"); // 올바른 사용
          setValue("firstName", true); // ❌ 오류: boolean은 string 아님
          errors.bill; // ❌ 오류: 'bill' 속성 없음
        }}
      >
        SetValue
      </button>
    </form>
  );
}
```

## 디자인 철학 (Design and philosophy)

React Hook Form은 사용자와 개발자 모두에게 우수한 사용 경험을 제공하기 위해 설계되었습니다.

### 사용자 경험 측면

- Proxy 기반의 폼 상태 구독 모델 도입
- 불필요한 연산 제거
- 컴포넌트 렌더링 분리 최적화

폼과 상호작용할 때 더 빠르고 부드러운 UX 제공

### 개발자 경험 측면

- HTML 표준에 부합하는 내장 유효성 검사 지원
- Yup, Zod 등의 스키마 유효성 검사와 자연스럽게 통합
- TypeScript 기반의 강력한 정적 타입 검사 제공

개발 도중 오류를 빠르게 발견하고, 견고한 폼을 설계할 수 있도록 도움
