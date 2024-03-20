# 5. props와 attrs

## 학습 키워드

- props
- attrs

## Props 활용

활성화 여부를 표현하거나, 특정 스타일을 잡아주고 싶을 때 유용합니다.

```tsx
import { useBoolean } from 'usehooks-ts';

import styled from 'styled-components';

type ButtonProps = {
  active: boolean;
}

function background(props: ButtonProps) {
  return props.active ? '#00F' : '#FFF';
}

const Button = styled.button<ButtonProps>`
  background: ${background};
  color: #000;
  border: 1px solid #888;
`;

export default function Switch() {
  const { value: active, toggle } = useBoolean(false);

  return (
    <Button
      type="button"
      onClick={toggle}
      active={active}
    >
      On/Off
    </Button>
  );
}
```

사용할 함수를 바로 넣을 수도 있습니다.

```tsx
import { useBoolean } from 'usehooks-ts';

import styled from 'styled-components';

type ButtonProps = {
  active: boolean;
}

function background(props: ButtonProps) {
  return props.active ? '#00F' : '#FFF';
}

const Button = styled.button<ButtonProps>`
  background: ${(props) => (props.active ? '#00F' : '#FFF';)};
  color: #000;
  border: 1px solid #888;
`;

export default function Switch() {
  const { value: active, toggle } = useBoolean(false);

  return (
    <Button
      type="button"
      onClick={toggle}
      active={active}
    >
      On/Off
    </Button>
  );
}
```

### css 함수

prop에 따라 바꾸고 싶은 CSS 속성이 위와 같이 하나가 아니라 여러 개일 경우가 있습니다.\
Styled Components에서 제공하는 css 함수를 사용하면 여러 개의 CSS 속성을 묶어서 정의할 수 있습니다.

```tsx
import { useBoolean } from 'usehooks-ts';

import styled, { css } from 'styled-components';

type ParagraphProps = {
  active?: boolean;
}

// props.active가 있다면 css로 정의된 스타일 적용 (font-weight: bold)
const Paragraph = styled.p<ParagraphProps>`
  color: ${(props) => (props.active ? '#F00' : '#888')};

  ${(props) => props.active && css`
    font-weight: bold;
  `}
`;

export default function Greeting() {
  const { value: active, toggle } = useBoolean(false);

  // active props 전달
  return (
    <div>
      <Paragraph>
        Inactive
      </Paragraph>
      <Paragraph active>
        Active
      </Paragraph>
      <Paragraph active={active}>
        Hello, world
        {' '}
        <button type="button" onClick={toggle}>
          Toggle
        </button>
      </Paragraph>
    </div>
  );
}
```

## 속성 추가

`attrs` 메서드로 기본 속성을 추가할 수 있습니다.\
불필요하게 반복되는 속성을 처리할 때 유용한데, 버튼(`type="button"`) 등을 만들 때 적극 활용합니다.

```tsx
import styled from 'styled-components';

const Button = styled.button.attrs({
  type: 'button',
})`
  border: 1px solid #888;
  background: transparent;
  cursor: pointer;
`;

export default Button;
```

`Button`의 `type`을 `submit` 또는 `reset`으로 지정할 수 있도록 할 수 있습니다.\
지정해주지 않으면 `type`은 `button`이 됩니다.

```tsx
import { useBoolean } from 'usehooks-ts';

import styled, { css } from 'styled-components';

type ButtonProps = {
  type?: 'button' | 'submit' | 'reset';
  active: boolean;
}

// 제네릭 타입 ButtonProps을 두 번 쓰는 이유는
// 1. attrs 메서드는 ButtonProps 타입을 갖음
const Button = styled.button.attrs<ButtonProps>((props) => ({
  type: props.type ?? 'button',
  // 2. styled component는 ButtonProps을 갖음
}))<ButtonProps>`
  background: #FFF;
  color: #000;
  border: 1px solid #888;

  ${(props) => props.active && css`
    background: #00F;
    color: #FFF;
  `}
`;

export default function Switch() {
  const { value: active, toggle } = useBoolean(false);

  return (
    <Button
      onClick={toggle}
      active={active}
    >
      On/Off
    </Button>
  );
}
```

## 참고 자료

- [Passed props](https://styled-components.com/docs/basics#passed-props)
- [Styled Components로 React 컴포넌트 스타일하기](https://www.daleseo.com/react-styled-components/)
- [Attaching additional props](https://styled-components.com/docs/basics#attaching-additional-props)
