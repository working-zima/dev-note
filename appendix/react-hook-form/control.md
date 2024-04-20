# control

`control` 객체는 React Hook Form에서 컴포넌트를 등록하는 메소드들을 담고 있습니다.\
주로 `<Controller>` 컴포넌트와 함께 사용되어 비제어 컴포넌트를 쉽게 통합하고 관리할 수 있게 도와줍니다.

## 규칙

객체 안에 있는 속성들은 내부적으로만 사용되기 때문에 직접 접근하지 않습니다.

```tsx
import { useForm, Controller } from "react-hook-form"

function App() {
  const { control } = useForm()

  return (
    <Controller
      render={({ field }) => <input {...field} />}
      name="firstName"
      control={control}
      defaultValue=""
    />
  )
}
```
