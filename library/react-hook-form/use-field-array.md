# `useFieldArray` - 동적 필드 배열을 위한 훅

React Hook Form에서 **동적 필드 배열**(예: 반복되는 입력 그룹, 추가/삭제되는 항목 등)을 다룰 때 사용하는 커스텀 훅입니다.  
최적화된 퍼포먼스와 사용자 경험을 제공합니다.

## Props

| 이름               | 타입      | 필수 | 설명                                                                  |
| ------------------ | --------- | ---- | --------------------------------------------------------------------- |
| `name`             | `string`  | ✅   | 배열 필드의 고유 이름. (동적 이름은 지원하지 않음)                    |
| `control`          | `object`  | ⛔️  | `useForm()`에서 반환된 객체. `FormProvider` 사용 시 생략 가능         |
| `shouldUnregister` | `boolean` | ❌   | 언마운트 시 등록 해제 여부                                            |
| `keyName`          | `string`  | ❌   | 내부적으로 생성되는 `id`의 key 속성 이름. 다음 버전에서 제거 예정     |
| `rules`            | `object`  | ❌   | `register`와 동일한 유효성 검사 옵션 (예: `required`, `minLength` 등) |

## 예제

```tsx
function FieldArray() {
  const { control, register } = useForm();
  const {
    fields,
    append,
    prepend,
    remove,
    insert,
    swap,
    move,
    update,
    replace,
  } = useFieldArray({
    control,
    name: "test",
  });

  return (
    <>
      {fields.map((field, index) => (
        <input
          key={field.id} // index가 아니라 id를 key로 사용해야 함
          {...register(`test.${index}.value`)}
        />
      ))}
      <button onClick={() => append({ value: "" })}>추가</button>
    </>
  );
}
```

## 반환값

| 이름      | 타입                                   | 설명                                                                                               |
| --------- | -------------------------------------- | -------------------------------------------------------------------------------------------------- | ---------------------------------- |
| `fields`  | `{ id: string }[]` 포함된 객체 배열    | 폼 입력을 반복적으로 렌더링할 때 사용. 각 객체에는 고유한 `id`가 포함되어 있어야 `key`로 사용 가능 |
| `append`  | `(obj: object                          | object[], focusOptions?) => void`                                                                  | 마지막에 항목 추가                 |
| `prepend` | `(obj: object                          | object[], focusOptions?) => void`                                                                  | 처음에 항목 추가                   |
| `insert`  | `(index: number, obj: object           | object[], focusOptions?) => void`                                                                  | 특정 위치에 항목 삽입              |
| `swap`    | `(from: number, to: number) => void`   | 두 항목 위치 교환                                                                                  |
| `move`    | `(from: number, to: number) => void`   | 한 항목을 다른 위치로 이동                                                                         |
| `update`  | `(index: number, obj: object) => void` | 특정 위치의 항목을 업데이트 (주의: 필드가 언마운트 & 리마운트 됨)                                  |
| `replace` | `(items: object[]) => void`            | 전체 배열 교체                                                                                     |
| `remove`  | `(index?: number                       | number[]) => void`                                                                                 | 항목 삭제 (index 없으면 전체 삭제) |

### 🛠️ 각 메서드 자세히 보기

#### `append`

```tsx
append({ name: "홍길동" }); // 단일 항목
append([{ name: "김철수" }, { name: "이영희" }]); // 여러 항목
```

- `obj`는 **반드시 초기값을 포함해야 함** (빈 객체 ❌)
- 옵션으로 `{ shouldFocus: true }` 전달 가능
- 추가된 필드는 자동으로 `register`에 등록됨

#### `prepend`

```tsx
prepend({ name: "맨 앞 항목" });
```

- 가장 앞쪽에 항목 삽입
- `append`과 동일한 조건과 제한 적용

#### `insert`

```tsx
insert(2, { name: "세 번째 위치에 삽입" });
```

- 특정 위치에 삽입 가능
- 여러 개도 삽입 가능: `insert(1, [{...}, {...}])`

#### `swap`

```tsx
swap(1, 2); // 1번과 2번 위치를 교환
```

- 항목의 순서를 교체
- 시각적인 순서 변경이나 드래그 앤 드롭 시 사용

#### `move`

```tsx
move(2, 0); // 2번 항목을 맨 앞으로 이동
```

- 항목을 원하는 위치로 이동
- `swap`은 위치 변경만, `move`는 이동

#### `update`

```tsx
update(1, { name: "업데이트된 이름" });
```

- 특정 항목의 값을 업데이트
- 해당 항목이 언마운트 & 리마운트 되므로 **성능에 민감하다면 `setValue`를 대신 사용할 것**

#### `replace`

```tsx
replace([{ name: "새 배열 1" }, { name: "새 배열 2" }]);
```

- 전체 배열을 새로운 값으로 교체
- 기존 필드는 모두 제거됨

#### `remove`

```tsx
remove(1); // 1번 인덱스 제거
remove([0, 2]); // 여러 인덱스 제거
remove(); // 전체 제거
```

- 인덱스가 없으면 전체 필드를 제거함
- 삭제된 항목은 자동으로 `unregister`

### 주의사항 요약

| 주의사항                       | 설명                                       |
| ------------------------------ | ------------------------------------------ |
| `field.id`를 key로 사용해야 함 | `key={field.id}`는 필수. `key={index}` ❌  |
| 빈 객체 `append({})` ❌        | 모든 필드는 기본값 포함 필요               |
| `update()`는 리렌더 발생       | 퍼포먼스 민감 시 `setValue()` 사용 추천    |
| `move`와 `swap`은 순서 제어용  | `move`: 한 쪽으로 이동 / `swap`: 위치 교환 |

## 유의사항

- `field.id`를 key로 반드시 사용해야 리렌더링 문제가 발생하지 않습니다.

```tsx
// ✅ 올바른 방식
fields.map(field => <input key={field.id} ... />)

// ❌ 잘못된 방식
fields.map((_, index) => <input key={index} ... />)
```

- `append`, `insert`, `update` 등은 **빈 객체 불가**  
  → 반드시 초기값을 포함해야 합니다.

```tsx
append(); ❌
append({}); ❌
append({ firstName: 'bill', lastName: 'luo' }); ✅
```

- 같은 `name`으로 여러 `useFieldArray`를 사용할 수 없습니다.
- `checkbox`나 `radio`와 같이 name이 중복되는 경우는 `Controller`나 `useController`와 함께 사용하세요.
- 중첩 필드 배열의 경우 `name`을 `as const`로 캐스팅해야 합니다.

```tsx
const { fields } = useFieldArray({
  name: `test.${index}.items` as "test.0.items",
});
```

## 전체 예제

```tsx
import React from "react";
import { useForm, useFieldArray, Controller } from "react-hook-form";

function App() {
  const { register, control, handleSubmit } = useForm();
  const { fields, append, remove } = useFieldArray({
    control,
    name: "test",
  });

  return (
    <form onSubmit={handleSubmit((data) => console.log(data))}>
      <ul>
        {fields.map((item, index) => (
          <li key={item.id}>
            <input {...register(`test.${index}.firstName`)} />
            <Controller
              name={`test.${index}.lastName`}
              control={control}
              render={({ field }) => <input {...field} />}
            />
            <button type="button" onClick={() => remove(index)}>
              삭제
            </button>
          </li>
        ))}
      </ul>

      <button
        type="button"
        onClick={() => append({ firstName: "bill", lastName: "luo" })}
      >
        항목 추가
      </button>

      <input type="submit" />
    </form>
  );
}
```

## 요약

| 기능        | 설명                       |
| ----------- | -------------------------- |
| `append`    | 배열 끝에 항목 추가        |
| `prepend`   | 배열 앞에 항목 추가        |
| `insert`    | 지정 위치에 항목 삽입      |
| `remove`    | 항목 삭제                  |
| `swap/move` | 위치 교환 또는 이동        |
| `update`    | 특정 항목 값 수정          |
| `replace`   | 전체 배열 대체             |
| `fields`    | 현재 필드 배열 (`id` 포함) |

## Tips

### Custom Register (입력 없이 register)

`Controller`를 사용하면 실제 `input` 없이도 필드를 등록할 수 있습니다.  
이 방식은 복잡한 데이터 구조를 다루거나, 값이 `input`이 아닌 다른 곳에 저장될 때 유용합니다.

```tsx
import { useForm, useFieldArray, Controller, useWatch } from "react-hook-form";

const ConditionalInput = ({ control, index }) => {
  const value = useWatch({
    name: "test",
    control,
  });

  return (
    <Controller
      control={control}
      name={`test.${index}.firstName`}
      render={({ field }) =>
        value?.[index]?.checkbox === "on" ? <input {...field} /> : null
      }
    />
  );
};

function App() {
  const { control } = useForm();
  const { fields, append, prepend } = useFieldArray({
    control,
    name: "test",
  });

  return (
    <form>
      {fields.map((field, index) => (
        <ConditionalInput key={field.id} {...{ control, index, field }} />
      ))}
    </form>
  );
}
```

#### Custom Register 포인트

- `Controller` 안에서 조건부로 input을 렌더링
- 값은 `useWatch`로 확인
- 필드는 실제 입력 요소가 없어도 등록 가능

### Controlled Field Array (전체 필드를 제어하기)

**`onChange` 시 직접 `fields` 배열의 값을 수정**하고 싶은 경우 사용할 수 있는 패턴입니다.  
`watch()`로 최신 값을 받아와 `fields`와 병합하여 사용합니다.

```tsx
import { useForm, useFieldArray } from "react-hook-form";

export default function App() {
  const { register, control, watch } = useForm();
  const { fields, append } = useFieldArray({
    control,
    name: "fieldArray",
  });

  const watchFieldArray = watch("fieldArray");

  // 실시간으로 변경된 값을 field에 병합
  const controlledFields = fields.map((field, index) => ({
    ...field,
    ...watchFieldArray?.[index],
  }));

  return (
    <form>
      {controlledFields.map((field, index) => (
        <input
          key={field.id}
          {...register(`fieldArray.${index}.name` as const)}
        />
      ))}
    </form>
  );
}
```

#### Controlled Field Array 포인트

- `fields`는 초기값 기준
- `watch("fieldArray")`는 최신값
- 병합하여 `controlledFields`로 실시간 필드 상태를 관리

## Tips 요약

| 사용법                       | 설명                                                    |
| ---------------------------- | ------------------------------------------------------- |
| `Controller` + 조건부 렌더링 | 실제 입력 없이 register 가능, `useWatch`와 함께 사용    |
| `watch + map + merge` 방식   | `fields` + 실시간 상태를 병합해 완전한 제어 구현 가능   |
| `append({})` ❌              | 항상 초기값 포함해서 `append({ name: "" })` 형태로 사용 |
