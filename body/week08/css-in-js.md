# 3. CSS in JS

## 학습 키워드

- CSS in JS 란
- CSS

## CSS-in-JS 란

JavaScript를 사용하여 구성 요소의 스타일을 작성하고 관리하는 방식을 가리킵니다.\
일반적으로 활용되는 CSS-in-JS는 아래와 같은 스타일을 말합니다.

```tsx
import styled from 'styled-components';
// Create a component that renders a <p> element with blue text
const BlueText = styled.p`
  color: blue;
`;

<BlueText>My blue text</BlueText>
```

### CSS를 대규모로 사용할 때 발생하는 여러 가지 문제점

#### Global Namespace

CSS에서 클래스 및 변수를 전역으로 정의할 경우, 다른 부분에서의 충돌 문제가 발생할 수 있습니다.\
이를 해결하기 위해 전역 변수 대신 로컬 변수를 사용하고, CSS 언어를 확장하여 클래스 이름에 지역성을 부여하는 방안이 있습니다.

#### Dependencies

프로젝트가 커지면서 CSS 파일도 많아지고, 이에 따라 파일 간의 의존성을 관리해야 합니다.\
이를 위해 정적 분석을 통해 필요한 CSS 파일을 자동으로 포함시키는 방안이 있습니다.

#### Dead Code Elimination

용되지 않는 CSS 코드는 유지보수를 어렵게 만들고, 파일 크기를 증가시킬 수 있습니다.\
이를 해결하기 위해 사용되지 않는 클래스를 식별하고 제거하는 방법이 있습니다.

#### Minification

CSS 및 JS 파일을 압축하여 파일 크기를 최소화하고 성능을 향상시키는 방법이 있습니다.\
또한 클래스 이름을 압축하여 사용자에게 빠르게 전달할 수 있도록 합니다.

#### Sharing Constants

CSS와 JS 간에 상수를 공유해야 하는 경우가 있습니다.\
이를 해결하기 위해 CSS의 var 문법을 활용하여 상수를 공유하는 방법이 있습니다.

#### Non-deterministic Resolution

일반적으로 CSS는 단일 파일로 작성되며, 동일한 요소에 대해 여러 개의 규칙이 정의될 경우, 마지막으로 나타나는 규칙이 우선시됩니다.\
이는 CSS 파일이 순차적으로 읽히기 때문에 발생합니다.\
하지만 이러한 동작 방식은 여러 CSS 파일이 동적으로 로드되는 상황에서 문제를 일으킬 수 있습니다.\
이러한 문제를 해결하기 위한 전통적인 방법은 클래스에 대한 스타일을 보다 구체적으로 지정함으로써 선택자의 우선순위를 높이는 방법이 있습니다.

그러나 이 방법은 유지보수가 어려울 수 있고, 브라우저 성능에도 영향을 줄 수 있습니다.\
따라서 이 문제를 해결하기 위해 선택자의 우선순위를 높이는 대신, 더 명확한 규칙을 도입하는 방안이 고려됩니다.

#### Isolation

핵심 컴포넌트를 사용할 때 외부에서 컴포넌트의 내부 동작을 이해하지 않은 상태로 스타일을 변경할 수 있는 가능성이 있습니다.\
이러한 변경이 예기치 않은 결과를 초래한다면 이러한 문제로 인해 핵심 컴포넌트의 유지보수가 어려워집니다.\
이를 해결하기 위해 엄격한 컴포넌트 API를 유지하고, 외부에서의 스타일 수정을 방지하는 방법이 있습니다.

### CSS in JS의 장점

#### Scoped(범위가 지정된) styles

CSS를 구조화하는 일은 오랫동안 어려운 문제였습니다. 다양한 방법론이 시도되었지만, BEM이 가장 널리 사용되고 있는 방법 중 하나입니다.\

BEM은 스타일의 `class`를 `.Block__element--modifier` 패턴으로 제한하는 이름 만들기 규칙(naming convention)입니다.\
CSS-in-JS 라이브러리들은 BEM 기반 사고방식을 따르면서도 각각의 UI에 스타일을 지정하는 방식을 채택합니다.

특히, scoped 스타일을 사용하는 일부 라이브러리들은 CSS 선택자를 자동으로 생성하여 스타일의 범위를 지정합니다.\
라이브러리가 레퍼런스를 관리하므로 전역에서 클래스 이름이 충돌할 일이 없다는 것은 직접 클래스에 BEM과 같은 규칙을 바탕으로 접두어를 붙일 필요가 없다는 말과 같습니다.\
이는 코드를 더 모듈화하고 유연하게 만들어줍니다.

일부 라이브러리들에서 이러한 방식은 컴포넌트와 스타일을 너무 강하게 결합시킬 수 있다는 우려가 있습니다.\
스타일과 컴포넌트가 너무 강하게 연결되어 있기 때문에 스타일 추출, 명칭, 컴포넌트의 재사용이 가지는 중요성을 놓치는 것처럼 보였습니다.\
이를 해결하기 위해 `styled-components`와 같은 라이브러리가 등장했는데, 스타일시트를 생성하는 대신 컴포넌트에 스타일이 직접 연결된 형태로 만들도록 강제합니다.

CSS 스타일링을 효율적으로 구조화하는 것은 중요합니다.\
CSS-in-JS 라이브러리들은 이를 해결하기 위한 다양한 방법을 제시하고 있으며, 이를 통해 스타일의 범위를 지정하고 코드를 더 모듈화할 수 있습니다.

#### Critical(필수적인) CSS

필수적인 CSS의 개념은 초기 로딩 시간을 단축하기 위해 페이지의 첫 화면 렌더링에 반드시 필요한 최소한의 스타일만 포함하는 것입니다.

필수적인 CSS를 추출하고 인라인에 적용하는 Addy Osmani의 critical와 같은 도구가 있지만, 이러한 작업을 자동화하고 유지하기는 어렵습니다.\
까다로운 선택적인 성능 최적화 프로세스이기 때문에 대부분의 프로젝트에서는 이 단계를 생략합니다.

CSS-in-JS는 이와 완전히 다릅니다.\
서버 렌더링된 애플리케이션을 개발할 때 필수적인 CSS를 추출하는 것은 단순한 최적화 과정이 아닙니다.\
CSS-in-JS는 기본적으로 필수적인 CSS가 우선적으로 작동해야 한다고 요구합니다.

#### Smarter optimisations(더 똑똑한 최적화)

CSS를 새로운 방식으로 구성하는 방법들이 등장했습니다.\
이 방법들은 각 클래스가 하나의 특정한 스타일을 나타내는 클래스를 사용하여 스타일을 적용하는 것을 중시합니다.\
이런 방식을 사용하면 CSS 파일의 크기를 줄일 수 있고, 스타일을 재사용하기 쉽습니다.

```tsx
<div class="Bgc(#0280ae.5) C(#fff) P(20px)">
 Atomic CSS
</div>
```

이런 최적화를 위해 CSS-in-JS나 CSS Modules와 같은 도구들이 사용됩니다.\
이 도구들은 클래스를 직접 입력하는 대신 자동으로 생성된 값을 사용합니다.

최적화는 이제 수작업이 아니라 도구를 통해 자동으로 이루어집니다.\
이러한 방식은 이제 선택할 수 있는 옵션 중 하나로 여겨집니다.

#### Package management

과거에는 CSS를 공유하기 위해 수동으로 파일을 다운로드하거나 Bower와 같은 패키지 관리자를 사용했습니다.\
그러나 현재는 Browserify나 webpack과 같은 도구를 통해 npm을 통해 CSS 코드를 공유합니다.\
그러나 CSS 커뮤니티에서는 여전히 CSS를 수동으로 관리하는 경우가 많습니다.

이렇게 된 이유 중 하나는 CSS에 대한 패키지 관리의 어려움 때문입니다.\
CSS에 대한 패키지 관리의 어려움을 해결하기 위해서는 CSS를 대상으로 한 적절한 패키지 매니저가 필요합니다.\
이러한 도구를 사용하면 CSS를 자바스크립트 모듈과 같이 사용할 수 있고, 패키지를 더 쉽게 관리할 수 있습니다.

JavaScript 객체를 사용하여 스타일을 정의하고 필요한 곳에 적용할 수 있습니다.\
이 방법은 코드를 조합하고 공유하는 데 도움이 됩니다.\
이러한 방식을 통해 작은 CSS 패키지를 조합하여 큰 스타일 컬렉션을 구축할 수 있습니다.\
이것이 JavaScript와 같은 수준의 활동을 가능하게 합니다.\
이러한 접근 방식은 Sass와 같은 전처리기보다 더 뛰어나며 JavaScript 패키지 생태계를 활용할 수 있습니다.

#### Non-browser styling

최근에는 CSS를 JavaScript 안에서 작성하는 방법들이 떠오르고 있습니다.\
이는 CSS를 더욱 동적으로 사용하고 관리하기 쉽게 만들어줍니다.

React Native는 웹이 아닌 환경에서 React 컴포넌트를 사용할 수 있게 해줍니다.\
이를 통해 CSS와 유사한 스타일 시스템을 사용할 수 있습니다.

React Native에서는 StyleSheet API를 사용하여 스타일을 정의하고 관리합니다.\
이는 JavaScript를 사용하여 스타일을 정의하고 관리하는 CSS-in-JS와 유사한 개념입니다.\
StyleSheet API를 사용하면 CSS와 유사한 구문으로 스타일을 정의하고 컴포넌트에 적용할 수 있습니다.

CSS-in-JS와 마찬가지로 StyleSheet API를 사용하면 컴포넌트와 스타일을 느슨하게 결합시킬 수 있고, 동적 스타일링이 가능하며, 컴포넌트 지향적인 접근 방식을 채택할 수 있습니다.\
또한, React Native의 경우 웹 브라우저와는 다른 스타일 시스템을 사용해야 하므로 CSS 대신 JavaScript를 사용하여 스타일을 정의하는 것이 자연스럽습니다.

따라서 비 브라우저 환경에서 스타일을 관리할 때에도 CSS-in-JS와 유사한 접근 방식을 취하는 것이 일반적입니다.

### CSS-in-JS에 관해 알아야 할 모든 것

#### Thinking in components

CSS-in-JS는 CSS 모델을 구성 요소 수준으로 추상화하여 전체 문서가 아닌 구성 요소의 관점에서 생각함으로써 모듈성을 향상시킵니다.

#### CSS-in-JS leverages the full power of the JavaScript ecosystem to enhance CSS

CSS-in-JS는 JavaScript 생태계의 모든 기능을 활용하여 CSS를 향상시킵니다.

#### True rules isolation

CSS-in-JS는 진정한 규칙 격리를 제공하여 상위 요소에서 의도하지 않은 속성 상속을 방지합니다.

#### Scoped selectors

CSS-in-JS는 기본적으로 고유한 클래스 이름을 생성하여 범위 선택기를 보장하고 네임스페이스 충돌을 방지합니다.

#### Vendor Prefixing

CSS 규칙에는 자동으로 공급업체 접두사가 지정되므로 수동으로 접두사를 지정할 필요가 없습니다.

#### Code sharing

CSS-in-JS는 JavaScript와 CSS 간의 상수 및 기능 공유를 용이하게 합니다.

#### Only the styles which are currently in use on your screen are also in the DOM (react-jss)

현재 화면에서 사용 중인 스타일만 DOM에 존재하므로 효율적인 DOM 사용이 보장됩니다.

#### Dead code elimination

사용하지 않는 CSS 코드를 제거하여 성능을 최적화합니다.

#### Unit tests for CSS

CSS-in-JS는 CSS 코드의 단위 테스트를 허용하여 코드 품질과 유지 관리성을 향상시킵니다.

### 가장 인기있는 CSS-in-JS libraries

→ 2023년 1월 기준으로 styled-components, JSS, Emotion 순서.

### 성능 이슈

→ CSS 파일과 JS 파일 로딩의 차이.

리액트같은 컴포넌트 기반 도구를 사용할 때 CSS를 JS안에 포함(CSS-in-JS)시키는 도구를 많이 사용합니다.\
CSS를 JS 안에 작성하면 컴포넌트 파일을 하나로 유지하면서 기능과 스타일을 같이 관리할 수 있어서 선호하는 사람이 많습니다.\
하지만 JS의 크기를 줄이는 것이 성능에 유리하고 결과적으로 CSS는 별도로 분리하는 것이 더 좋습니다.

이런 경우 스타일드 컴포넌트(Styled Components)를 르내리아(Linaria)로 교체하고 빌드할 때 CSS를 뽑아내거나, 스벨트(Svelte)로 CSS를 분리해서 별도 파일로 만들어 빌드하는 방법으로 성능 저하를 피할 수 있습니다.

### 이외에도

#### CSS-in-JS의 좋은 점

1. 지역 스코프 스타일을 사용해 스타일이 관련 없는 요소에 실수로 적용되는 것을 방지할 수 있습니다.
2. 단일 컴포넌트에 관련된 모든 것을 같은 위치에 두는 코로케이션을 쉽게 구현할 수 있습니다.
3. 자바스크립트 변수를 스타일 규칙에 적용하여 상수와 리액트 props 및 state를 스타일에서 활용 수 있습니다.

### CSS-in-JS의 나쁜 점

1. 컴포넌트가 렌더링될 때 CSS-in-JS 라이브러리는 스타일을 document에 삽입할 수 있는 일반 CSS로 직렬화해서 런타임 오버헤드를 발생시킵니다.
2. 번들 크기를 늘립니다.
3. React DevTools를 어지럽힙니다.

### CSS-in-JS의 못난 점

1. CSS 규칙을 자주 삽입하면 브라우저에서 더 많은 추가 작업을 수행해야 합니다.
2. SSR 및 혹은 또는 컴포넌트 라이브러리를 사용할 때 호환성등으로 잘못될 수 있는 부분이 훨씬 더 많습니다.

### 대안

#### [Linaria](https://linaria.dev/)

- CSS를 평범한 텍스트로 작성.
- React에 종속적이지 않지만, React Styled Component도 지원함.

#### [vanilla-extract](https://vanilla-extract.style/)

- CSS를 오브젝트 형태로 표현. React의 Inline Style과 유사함.
- React와 무관하게 사용 가능.

## 참고자료

- [CSS in JS](https://en.wikipedia.org/wiki/CSS-in-JS)
- [React: CSS in JS (2014)](https://blog.vjeux.com/2014/javascript/react-css-in-js-nationjs.html)
- [A Unified Styling Language (2017)](https://megaptera.notion.site/1bda981f17224c058b612598d4323c63)
- [[번역] 통합 스타일링 언어](https://blog.rhostem.com/posts/2017-06-24-unified-styling-language)
- [All You Need To Know About CSS-in-JS (2017)](https://megaptera.notion.site/CSS-in-JS-d5904960ac564b0c8cdf5aa3bc2df5da)
- [[번역] CSS-in-JS에 관해 알아야 할 모든 것](https://d0gf00t.tistory.com/22)
- [Most popular CSS-in-JS libraries](https://npmtrends.com/aphrodite-vs-emotion-vs-glamorous-vs-jss-vs-radium-vs-styled-components-vs-styletron)
- [CSS-in-JS와 성능 (2021)](https://megaptera.notion.site/CSS-in-JS-0afa302408f74f7a9712924ead6318be)
- [CSS-in-JS와 성능 (2021)](https://hyeonseok.com/blog/877)
- [Why We're Breaking Up with CSS-in-JS (2022)](https://megaptera.notion.site/CSS-in-JS-4022ede93d06407ea6474a40be4cd906)
- [(번역) 우리가 CSS-in-JS와 헤어지는 이유](https://junghan92.medium.com/%EB%B2%88%EC%97%AD-%EC%9A%B0%EB%A6%AC%EA%B0%80-css-in-js%EC%99%80-%ED%97%A4%EC%96%B4%EC%A7%80%EB%8A%94-%EC%9D%B4%EC%9C%A0-a2e726d6ace6)
