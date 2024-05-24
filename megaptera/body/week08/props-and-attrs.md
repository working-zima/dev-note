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

`prop`에 따라 바꾸고 싶은 CSS 속성이 위와 같이 하나가 아니라 여러 개일 경우가 있습니다.\
Styled Components에서 제공하는 `css` 함수를 사용하면 여러 개의 CSS 속성을 묶어서 정의할 수 있습니다.

#### css 함수를 사용하지 않은 경우

```tsx
import { useBoolean } from 'usehooks-ts';

import styled, { css } from 'styled-components';

type ParagraphProps = {
  active?: boolean;
}

// props.active가 있다면 css로 정의된 스타일 적용 (font-weight: bold)
const Paragraph = styled.p<ParagraphProps>`
  color: ${(props) => (props.active ? '#F00' : '#888')};
  font-weight: ${(props) => (props.active ? 'bold' : 'normal')}; // 조건부 스타일링
  border: ${(props) => (props.active ? '1px solid #888' : 'none')}; // 조건부 스타일링
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

#### css 함수를 사용한 경우

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
    border: 1px solid #888;
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

## 속성 추가 (attrs)

스타일 속성뿐만 아니라 `attrs` 메서드로 기본 속성도 추가할 수 있습니다.\
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

### HTML attribute warnings

위의 코드대로 작성했을 경우 HTML 속성 경고가 발생할 수 있습니다.\

```bash
Warning: Received "true" for a non-boolean attribute
```

위 경고는 styled-component에서 `<div>` 또는 `<a>`와 같은 HTML DOM 요소에 Boolean attribute가 아닌 비표준 속성에 boolean값을 전달하면 발생합니다.

해결 방법으로 두 가지가 있습니다.

#### 문제 예시

`red`속성에 대한 경고가 발생합니다.

```tsx
const Link = props => (
  <a {...props} className={props.className}>
    {props.text}
  </a>
)

const StyledComp = styled(Link)`
  color: ${props => (props.red ? 'red' : 'blue')};
`

<StyledComp text="Click" href="https://www.styled-components.com/" red />
```

위의 코드는 아래와 같은 HTML 요소를 렌더링합니다.\
React는 `<a>`요소에 대해 유효한 HTML 속성이 아닌 `red` 같은 비표준 속성이 첨부되는 경우 경고를 합니다.

```html
<a text="Click" href="https://www.styled-components.com/" red="true" class="[generated class]">Click</a>
```

#### 해결 방법1 - transient props(일시적인 props)를 사용

문제가 되는 속성에 prefix `$`를 붙여 사용합니다.

```tsx
const Link = ({ className, text, ...props }) => (
  <a {...props} className={className}>
    {text}
  </a>
)

// red 속성에 prefix $를 붙여 사용합니다.
const StyledComp = styled(Link)`
  color: ${props => (props.$red ? 'red' : 'blue')};
`


<StyledComp text="Click" href="https://www.styled-components.com/" $red />
```

#### 해결 방법2 - destructure props(props 구조분해)를 사용

구조분해 할당을 사용하여 속성을 직접 추출하여 사용합니다.

```tsx
// 이전에는 Link 컴포넌트의 props를 인자로 받았지만,
// 여기서는 className, red, text 속성을 직접 추출하여 사용합니다.
const Link = ({ className, red, text, ...props }) => (
  <a {...props} className={className}>
    {text}
  </a>
)


const StyledComp = styled(Link)`
  color: ${props => (props.red ? 'red' : 'blue')};
`


<StyledComp text="Click" href="https://www.styled-components.com/" red />
```

위의 코드는 아래와 같은 HTML 요소를 렌더링합니다.\
`red` 속성이 보이지 않습니다.

```html
<a href="https://www.styled-components.com/" class="[generated class]">Click</a>
```

#### Boolean attribute (HTML)

`disabled`, `checked`, `readonly`, `required`가 있습니다.

## 참고 자료

- [Passed props](https://styled-components.com/docs/basics#passed-props)
- [Styled Components로 React 컴포넌트 스타일하기](https://www.daleseo.com/react-styled-components/)
- [Attaching additional props](https://styled-components.com/docs/basics#attaching-additional-props)
