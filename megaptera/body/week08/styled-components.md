# 4. styled-components

## 학습 키워드

- styled-componets

## styled-components

스타일이 적용된 컴포넌트를 쉽게 만들 수 있는 도구입니다.

반드시 VS Code Extension을 설치해서 쓰는것이 좋습니다.\
도구에서 제대로 지원 안 하면 불편하게 사용해야 합니다.

Babel Plugin을 SWC에서 쓸 수 있도록 포팅한 것도 함께 설치하겠습니다.
(SSR 지원 등을 위한 공식 권장사항)

```bash
npm i styled-components
npm i -D @types/styled-components @swc/plugin-styled-components
```

.swcrc 파일 작성.

```json
{
  "jsc": {
    "experimental": {
      "plugins": [
        [
          "@swc/plugin-styled-components",
          {
            "displayName": true,
            "ssr": true
          }
        ]
      ]
    }
  }
}
```

간단한 Styled Component 만들기.\
`p` 태그를 `Paragraph`라는 컴포넌트로 만들었습니다.\
`Paragraph`컴포넌트는 `color: #00F`라는 스타일을 갖습니다.

```tsx
import styled from 'styled-components';

const Paragraph = styled.p`
  color: #00F;
`;

export default function Greeting() {
  return (
    <Paragraph>
      Hello, world!
    </Paragraph>
  );
}
```

nested하게 표현할 수 있습니다.\
`Paragraph` 안의 `strong`만 `font-size: 2em`이 적용됩니다.

```tsx
import styled from 'styled-components';

const Paragraph = styled.p`
  color: #00f;

  strong {
    font-size: 2em;
  }
`;

export default function Greeting() {
  return (
    <Paragraph>
      Hello, world
      <strong>!</strong>
    </Paragraph>
  );
}
```

기존 Styled Component에 추가로 스타일을 입히는 것도 가능합니다.

```tsx
import styled from 'styled-components';

const Paragraph = styled.p`
  color: #00F;
`;

const BigParagraph = styled(Paragraph)`
  font-size: 5rem;
`;

export default function Greeting() {
  return (
    <BigParagraph>
      Hello, world!
    </BigParagraph>
  );
}
```

기존 컴포넌트에 스타일을 입히는 것도 가능합니다.\
단, 기존 컴포넌트가 Class를 잡아줘야 한다는 점에 주의해야 합니다.

```tsx
import styled from 'styled-components';

function HelloWorld({ className }: React.HTMLAttributes<HTMLElement>) {
  return (
    <p className={className}>
      Hello, world!
    </p>
  );
}

const Greeting = styled(HelloWorld)`
  color: #00F;
`;

export default Greeting;
```

## 참고 자료

- [styled-components](https://styled-components.com/)
- [Babel Plugin](https://styled-components.com/docs/tooling#babel-plugin)
- [@swc/plugin-styled-components](https://github.com/swc-project/plugins/tree/main/packages/styled-components)
- [vscode-styled-components extention](https://marketplace.visualstudio.com/items?itemName=styled-components.vscode-styled-components)
