# src/components/TextField.tsx

```tsx
import React, { useRef } from 'react';

type TextFiledProps = {
  label: string;
  placeholder: string;
  text: string;
  setText: (value: string) => void;
};

export default function TextField({
  label, placeholder, text, setText,
}: TextFiledProps) {
  const id = useRef(`input-${Math.random()}`);

  const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    const { value } = event.target;
    setText(value);
  };

  return (
    <div>
      <label htmlFor={id.current}>
        {label}
      </label>
      <input
        id={id.current}
        type="text"
        placeholder={placeholder}
        value={text}
        onChange={handleChange}
      />
    </div>
  );
}
```

## TextField.test.tsx

`fireEvent.change()`는 입력 요소(input, textarea, select 등)의 값이 변경되었다고 시뮬레이트하기 위한 이벤트를 발생시키는 함수\
`fireEvent.change(변경할 입력 요소, { target: { value: '변경할 값' } });`

```tsx
import { render, screen, fireEvent } from '@testing-library/react';

import TextField from './TextField';

const context = describe;

// given
describe('TextField', () => {
  const label = 'Name';
  const text = 'Tester';

  const setText = jest.fn();

  beforeEach(() => {
    jest.clearAllMocks();
  });

  function renderTextField() {
    render((
      <TextField
        label={label}
        placeholder="Input your name"
        text={text}
        setText={setText}
      />
    ));
  }

  // when
  it('renders elements', () => {
    renderTextField();

  // then
    screen.getByLabelText(label);
    screen.getByPlaceholderText(/Input/);
    screen.getByDisplayValue(text);
  });

  // when
  context('when user enters name', () => {
    it('calls “setText” handler', () => {
      renderTextField();

      fireEvent.change(screen.getByLabelText(label), {
        target: { value: 'New Name' },
      });

      // then
      expect(setText).toBeCalledWith('New Name');
    });
  });
});
```

### Given

- `TextField` 컴포넌트가 props로 받는 'label, text' 초기화

- `TextField` 컴포넌트가 props로 받는 'setText' mocking

- 테스트 케이스 테스트 전 mock 함수 초기화

- `renderTextField`는 `TextField` 컴포넌트 렌더링하는 함수

### 테스트 케이스 01

TextField renders elements

#### when 01

- `TextField` 컴포넌트 렌더링

#### then 01

- 화면에 `TextField` 컴포넌트에서 렌더링하는 `label`태그에 'Name' 텍스트가 나오는지 확인

- 화면에 `input`의 placeholder에 'Input' 텍스트가 나오는지 확인

- 화면에 입력 요소인 input의 value에 'Tester'가 나오는지 확인

### 테스트 케이스 02

TextField when user enters name calls “setText” handler

#### when 02

- `TextField` 컴포넌트 렌더링

- `label`을 사용하는 입력 요소인 `input`의 value를 'New Name'으로 변경

#### then 02

- `input`의 value를 변경하면 `handleChange()`로 인해 `setText(value)`가 호출됨\
이때 `setText`가 'New Name'을 인자로 호출이 되는지 확인
