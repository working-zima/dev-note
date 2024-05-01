# 4. Testing Library

## 학습 키워드

- Jest
- Describe-Context-It 패턴
- React Testing Library

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

소프트웨어가 사양(specification)에 잘 맞는지 맞지 않는지 확인합니다.\
구현을 하기 위해 기능(소프트웨어가 어떻게 동작하는지) 목록을 만듭니다.\
주로 "Given-When-Then"의 템플릿을 사용하여 테스트 케이스를 표현합니다.

#### Given-When-Then

간단한 예를 들어보겠습니다.

전자렌지에 3분만 돌리면 완성되는 기능이 있습니다.\
여기서 여기서 우리가 해야 하는 건 '전자렌지에 3분만 돌리는 것'입니다.\
그럼 완성이 되는 일이 벌어집니다.\
Given-When-Then 템플릿으로 정리해보겠습니다.

```json
// when
전자렌지에 3분만 돌리면

// then
완성
```

더 정확하게는 700와트 전자렌지를 준비하고, 3분이란 시간이 주어졌을 때 주어진 시간동안 전자렌지를 돌리면 요리가 완성 기능이 있습니다.\
Given-When-Then 템플릿으로 정리해보겠습니다.

```json
// Given
파워는 700와트, 기계는 전자렌지, 시간은 3분이 주어짐

// when
주어진 시간동안 전자렌지를 돌리면

// then
요리가 완성
```

#### Describe, It

게임에서 JUMP 기능을 만드려고 하려고 합니다.\
JUMP라는 대상의 행동에 대해 설명해줘야 합니다.\
이 때 `Describe`로 대상을, `It`으로 대상의 행동을 설명합니다.

Describe "JUMP"\
It should push the hero off the floor\
It should put the hero in teh air for 10 seconds

#### 코드 예시

`describe`에 주어를, `it`에 기능을 묘사합니다.

```jsx
// 주어: given (add는)
describe('add', () => {
  // 행동 (두 숫자가 주어지면, 두 숫자의 합을 반환)
  it('returns sum of two numbers', () => {
    // when
    const result = add(1, 2);

    // then ()
    expect(result).toBe(3);
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
