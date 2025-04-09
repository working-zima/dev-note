# 미래에도 사용할 수 있는 Modern CSS

## CSS 변수

CSS 변수는 재사용하는 값을 변수로 사용할 수 있게 합니다.

변수는 마지막에 전체 문서나 모든 요소를 참조하는 루트 의사 선택자(root pseudo selector)에서 정의됩니다.

```css
:root {
  --black-color: #000000;
}

.element {
  color: var(--black-color);
}
```

### fallback (대안)

사용자가 정의한 파일을 가져오는 걸 잊었다거나 하는 경우처럼 어떤 이유로 변수가 설정되지 않을 때를 대비한 대체 값입니다.

```css
:root {
  --black-color: #000000;
}

.element {
  color: var(--black-color, #000000);
}
```

## 벤더별 접두어 (Vendor Prefixes)

벤더별 접두어(Vendor Prefix)는 브라우저 공급자가 새로운 기능을 정식으로 제공하기 전에 구 버전의 브라우저에서도 그 기능을 무사히 구현하도록 합니다.

만약 벤더별 접두어를 사용하지 않는다면, 표준이 바뀌거나 최종 확정되어 전부 동일한 명세(specification)로 구현할 경우 flex 값이 갑자기 덮어 쓰여서 예전과 다르게 작동하면서 초기에 이 기술을 적용한 여러 웹사이트가 망가질 수 있습니다.\
초기에 접두어 버전을 채택해 기능을 적용했다면 표준이 바뀌거나 최종 확정된 최종 버전에서도 변경할 필요 없이 그대로 구현할 수 있습니다.

### 예시

flex 표준이 갓 나와서 개발 중일 때 flex 값의 접두어 버전을 구현하는 식으로 브라우저가 flexbox를 하나씩 구현했습니다.

접두어 버전은 각각 특정 브라우저에만 작동합니다.

```css
.container {
  display: flex;

  /* Safari, Android */
  display: -webkit-box;

  /* Internet Explorer, Edge */
  display: -ms-flexbox;

  /* Chrome */
  display: -webkit-flex;

  /* Firefox */
  display: -moz-box;
}
```

#### 실제 사용 예시

아래와 같이 작성한다면 flex를 지원하지 않는 구 버전도 flexbox를 사용할 수 있습니다.\
코드는 위에서 아래로 해석되기 때문에 구 버전이 새 버전을 덮어 쓰지 않게 기본값은 맨 아래에 둡니다.

```css
.main-header {
  width: 100%;
  position: fixed;
  top: 0;
  left: 0;
  background: #2ddf5c;
  padding: 0.5rem 1rem;
  z-index: 60;

  /* 브라우저가 구 버전을 이해했다면 구 버전을 사용 */
  /* 브라우저가 새 버전을 이해했다면 구 버전도 이해했을 것이기에 구 버전을 새 버전으로 덮어 씀 */
  display: -webkit-box;
  display: -ms-flexbox;
  display: -webkit-flex;
  display: -moz-box;
  display: flex;

  align-items: center;
  justify-content: space-between;
}
```

### prefix 리소스

- [What CSS to prefix?](https://shouldiprefix.com/)

- [CSS autoprefixer](https://autoprefixer.github.io/)

## @supports (지원 쿼리)

만약 어떤 기능은 특정 브라우저에서 구현이 안 되는 경우 지원쿼리를 사용하여 해결할 수 있습니다.

`@supports`는 프로퍼티와 값을 확인해 브라우저가 이를 지원할 경우 사용하고, 지원하지 않는다면 브라우저는 중괄호 내의 코드를 실행하지 않습니다.

```css
body {
  font-family: "Montserrat", sans-serif;
  margin: 0;
  padding-top: 3.5rem;
}

@supports (display: grid) {
  body {
    display: grid;
    grid-template-rows: 3.5rem auto fit-content(8rem);
    grid-template-areas:
      "header"
      "main"
      "footer";
    padding-top: 0;
    height: 100%;
  }
}
```

## Polyfills

폴리필은 브라우저에서 지원되지 않는 특정 CSS 기능을 구동할 수 있게 하는 JavaScript 패키지입니다.\
접두어를 쓸 수 없는 경우가 있고 대안(fallback)을 구현하기 싫은 경우에 사용합니다.

JavaScript 패키지를 다운로드하여 코드로 가져오면(`import`) CSS 코드를 구문 분석하고 JavaScript 로직에 맞게 페이지를 적절히 구현합니다.

폴리필은 HTML 파일로 가져오는 JavaScript 패키지이기 때문에 DOM을 구문 분석하고 스타일을 조정하게 하려면 사용자들 또한 폴리필 파일을 다운로드해야 하고, 이는 페이지의 성능에도 영향을 줍니다.
