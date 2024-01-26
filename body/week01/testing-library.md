# 4. Testing Library

## Jest

🚀 [**Jest 공식문서**](https://jestjs.io/)

거의 모든 것을 갖춘 테스팅 도구입니다.

Mocha와 Chai처럼 RSpec의 describe-it을 지원하고, expect로 단언(assertion)할 수 있습니다.\
Mocking도 다양한 레벨에서 쉽게 사용할 수 있습니다.

- [BETTER SPECS](https://www.betterspecs.org/) → RSpec 베스트 프랙티스 모음. 그대로 쓸 수는 없지만, 참고하자.
- [Ginkgo - Go 언어 개발자를 위한 BDD 테스팅 프레임워크](https://youtu.be/gfTsSBRvdqI) (Go 언어 사례)
- [JUnit5로 계층 구조의 테스트 코드 작성하기](https://johngrib.github.io/wiki/junit5-nested/) \*\*\*\*(Java 언어 사례)
- [Let’s RSpec](https://github.com/ahastudio/til/blob/main/ruby/20161206-rspec-let.md) → Jest는 RSpec의 let 같은 걸 지원하지 않기 때문에, 핵심 아이디어를 가져와서 적당한 수준에서 잘 써야 한다.

[Given-When-Then](https://www.notion.so/Given-When-Then-deee38000805476fa2ab10adc20424ed?pvs=21)

### 기본적인 테스트 코드

```jsx
test('add', () => {
  expect(add(1, 2)).toBe(3);
});
```

### BDD 스타일의 테스트 코드

```jsx
// 주어
describe('add', () => {
  // 행동
  it('returns sum of two numbers', () => {
    expect(add(1, 2)).toBe(3);
  });
});
```

## React Testing Library

[React Testing Library 공식문서](https://testing-library.com/docs/react-testing-library/intro/)\
[jest-dom](https://testing-library.com/docs/ecosystem-jest-dom/)

UI 테스트에 특화된 라이브러리입니다.\
거의 E2E Test처럼 쓸 수 있습니다.

단, “F/E 테스트 = only React 컴포넌트 테스트”가 되는 상황은 최대한 피하는 게 좋다.\
본질에 집중하지 못하고 너무 많은 테스트 코드를 작성할 위험이 있다.\
유지보수를 돕기 위해 테스트 코드를 작성하는데, 테스트 코드를 잘못 작성하면 오히려 유지보수를 저해할 수 있다.

### 참고 영상

[프론트엔드(Front-end)도 테스트해야 하나요?](https://www.youtube.com/watch?v=-kUmsKRmOnA)\
[Mocking 때문에 테스트 코드를 작성하기 어렵나요](https://www.youtube.com/watch?v=RoQtNLl-Wko)

### 간단한 테스트 코드

```typescript
test('Greeting', () => {
  render(<Greeting name='world' />);

  screen.getByText('Hello, world!');

  screen.getByText(/Hello/);

  expect(screen.queryByText(/Hi/)).not.toBeInTheDocument();
});
```

표현력이 좋아지고, 좀 더 고민할 기회를 제공.
