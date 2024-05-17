# handleSubmit

```tsx
((data: Object, e?: Event) => return Promise<void>, (errors: Object, e?: Event) => return void) =>
  return Promise<void>
```

이 함수는 폼 검증이 성공하면 폼 데이터를 받게 됩니다.\
`handleSubmit`은 `validation`과`preventDefault()`같은 것들을 담당합니다.

## 예시

`form`을 `submit`하게 되면 `handleSubmit`은 `input`에 입력 값이 10글자 이상이거나 `required`인지 확인 후 유효할 때만 `onValid`를 호출합니다.

```tsx
const { register, handleSubmit } = useForm();

const onValid = (data: any) => {
  console.log(data)
  // email: "입력한 데이터"
}

<div>
  <form onSubmit={handleSubmit(onValid)}>
    <input {...register("email", { required: true, minLength:10 })} placeholder="Email" />
  <form/>
<div/>
```

### 잡담

위의 예시에서 `<input {...register("email", { minLength:10  })} required placeholder="Email" />`가 아닌 `<input {...register("email", { required: true, minLength:10  })} placeholder="Email" />`으로 사용하는 이유는 첫 번째 코드는 브라우저의 html 소스 코드에서 `required`를 지울 수 있기 때문입니다.

## Props

Name | Type | Description
:-: | :-: | :-:
SubmitHandler | `(data: Object, e?: Event) => Promise<void>` | 성공 시 호출되는 callback
SubmitErrorHandler | `(errors: Object, e?: Event) => Promise<void>` | 에러 시 호출되는 callback

## RULES

`handleSubmit`을 사용하여 폼을 비동기적으로 쉽게 제출할 수 있습니다.

```tsx
handleSubmit(onSubmit)()

// 비동기 검증을 위해 비동기 함수를 전달할 수 있습니다.
handleSubmit(async (data) => await fetchAPI(data))
```

비활성화된 입력 필드는 폼 값에서 undefined 값으로 나타납니다. 사용자가 입력을 업데이트하지 못하게 하면서 폼 값을 유지하려면 readOnly를 사용하거나 전체 `<fieldset />`을 비활성화할 수 있습니다.

handleSubmit 함수는 onSubmit 콜백 내부에서 발생한 오류를 삼키지 않으므로 비동기 요청 내부에서 try와 catch를 사용하여 오류를 처리하는 것을 권장합니다.

```tsx
const onSubmit = async () => {
  // 오류가 발생할 수 있는 비동기 요청
  try {
    // await fetch()
  } catch (e) {
    // 오류 처리
  }
};

<form onSubmit={handleSubmit(onSubmit)} />
```
