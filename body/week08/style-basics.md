# 2. Style Basics

## 학습 키워드

- className

## className

### Basic: Class

`index.html` 파일에 간단히 CSS 추가.

```html
<style>
  .greeting {
    color: #00F;
  }
</style>
```

자바스크립트의 "class"를 리엑트에서는 “className”으로 지정.

```tsx
export default function Greeting() {
  return (
    <p className="greeting">
      Hello, world!
    </p>
  );
}
```

CSS는 컴포넌트를 전제로 하고 있지 않습니다.\
공통된 부분이 많을 때 재사용하기 쉽습니다.

따라서, 컴포넌트 중심으로 빠르게 작업하려고 하면 불편할 때가 많습니다.\
재사용은 그냥 컴포넌트를 사용하면 됩니다.

절충안으로 아주 작은 스타일 그룹을 클래스로 지정하는 방법도 있습니다.\
(Bootstrap, Tailwind CSS 등의 접근법)

### Basic: Inline Style

`style` 속성 활용.
평범한 JavaScript 객체를 활용하므로 변수, 함수 등을 재사용하기 쉽습니다.
텍스트가 아니라서 실수하거나 불편할 때가 있습니다.
TypeScript의 힘으로 자동완성, 타입 검사 등의 도움을 받을 수 있습니다.

```tsx
export default function Greeting() {
  const style = {
    color: '#00F',
  };

  return (
    <p style={style}>
      Hello, world!
    </p>
  );
}
```

바로 쓸 수도 있습니다.

```tsx
export default function Greeting() {
  return (
    <p
      style={{
        color: '#00F',
      }}
    >
      Hello, world!
    </p>
  );
}
```

## 참고자료

- [스타일링과 CSS](https://ko.legacy.reactjs.org/docs/faq-styling.html)
