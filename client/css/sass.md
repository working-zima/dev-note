# SASS (Syntactically Awesome Style Sheets)

CSS를 더 효율적으로 관리하기 위해 많이 사용하는 도구 중 하나가 Sass입니다.\
Sass는 CSS 전처리기이며, 두 가지 문법 형태인 SCSS와 SASS를 제공합니다.

## SCSS vs SASS 차이

| 항목   | SCSS (`.scss`)        | SASS (`.sass`)                       |
| ------ | --------------------- | ------------------------------------ |
| 문법   | CSS와 거의 동일       | 들여쓰기 기반 (중괄호 X, 세미콜론 X) |
| 가독성 | CSS 경험자에게 익숙함 | 더 간결하지만 익숙하지 않으면 불편   |
| 확장자 | `.scss`               | `.sass`                              |
| 추천도 | 더 많이 사용됨        | 상대적으로 드묾                      |

### SCSS 예시

```scss
$main-color: #333;

body {
  font: 100% Helvetica, sans-serif;
  color: $main-color;

  .container {
    padding: 20px;
  }
}
```

### SASS 예시

```scss
$main-color: #333

body
  font: 100% Helvetica, sans-serif
  color: $main-color

  .container
    padding: 20px
```

> 대부분의 프론트엔드 프로젝트에서는 `SCSS` 문법을 사용합니다. CSS와 거의 동일하기 때문에 진입 장벽이 낮습니다.

## Sass 설치 방법 (Node.js 기반)

### React, Next.js, Vite 등 Node.js 기반

프레임워크가 React, Next.js, Vite 등 Node.js 기반이라면 아래 명령어만 실행하면 됩니다.

```bash
npm install sass
```

설치되는 것은 Dart Sass이며, 현재 공식적으로 권장되는 Sass 구현입니다.

| 설치 방식        | 명령어                        | 용도                  | 지금은?           |
| ---------------- | ----------------------------- | --------------------- | ----------------- |
| 로컬 설치        | `npm install sass`            | 프로젝트 내에서 사용  | 권장              |
| X 루비 기반 Sass | `gem install sass`            | 옛날 Rails, Jekyll 등 | 사용 중단         |
| X 전역 CLI 설치  | `brew install sass/sass/sass` | 수동 SCSS 변환용      | 일반적으론 불필요 |

### 정적 HTML + SCSS 프로젝트

정적 HTML + SCSS 프로젝트에서 CLI로 직접 SCSS를 변환하려면 전역(global) 설치가 필요합니다.

```bash
npm install -g sass
```

### 아래와 같은 명령어는 더 이상 필요하지 않습니다

- `gem install sass` → 루비 Sass (지원 종료)
- `brew install sass/sass/sass` → 전역 CLI 설치 (대부분 필요 없음)

### VSC 확장

![VSC SASS 확장](./img/sass.png)

## SCSS → CSS로 바꾸는 법

| 상황                      | `sass` 명령어 필요 여부 | 설명                         |
| ------------------------- | ----------------------- | ---------------------------- |
| React, Vite, Next.js 등   | X 필요 없음             | `.scss` 파일을 자동으로 변환 |
| 정적 HTML + SCSS 프로젝트 | 필요함                  | 직접 CSS로 컴파일 필요       |

### 1. React, Vite, Next.js 같은 프레임워크 사용 시

`sass` 명령어는 필요 없습니다.\
빌드 도구가 알아서 `.scss`를 감지하고 CSS로 자동 컴파일해줍니다.

```tsx
// App.tsx
import "./App.scss";
```

이렇게 `import`만 하면 끝입니다.\
내부적으로 Webpack이나 Vite가 변환을 처리합니다.

### 2. HTML + SCSS만 있는 정적 프로젝트일 경우

이 경우에는 `sass` 명령어로 직접 CSS로 변환해야 합니다.

```bash
# 단일 파일 변환
sass style.scss style.css

# 단일 파일 감시
sass --watch style.scss:style.css

# 폴더 전체 감시 (자동 컴파일)
sass --watch scss/:css/
```

## 중첩 프로퍼티

Scss에서는 CSS 속성들 중 같은 접두어를 가지는 속성들을 중첩해서 표현할 수 있습니다.\
예를 들어 `font`, `border`, `animation`, `transition` 등입니다.

### 중첩 프로퍼티가 없는 CSS

```css
.button {
  font: {
    family: "Arial";
    size: 16px;
    weight: bold;
  }

  border: {
    style: solid;
    width: 1px;
    color: #333;
  }
}
```

### 중첩 프로퍼티가 있는 SCSS

```scss
.button {
  font-family: "Arial";
  font-size: 16px;
  font-weight: bold;

  border-style: solid;
  border-width: 1px;
  border-color: #333;
}
```

## 변수 (variables)

반복적으로 사용하는 값(색상, 폰트, 크기 등)을 변수로 관리할 수 있습니다.

### 변수 기본 문법

변수 이름 앞에는 `$` 기호를 붙입니다.\
일반적인 CSS 속성 값뿐만 아니라, 색상, 문자열, 숫자, 리스트, 맵 등 다양한 타입을 저장할 수 있습니다.

```scss
$변수이름: 값;
```

변수는 선언된 위치 기준으로 아래에서만 유효합니다.\
따라서 같은 이름의 변수를 다시 선언하면, 해당 범위에서는 새 값이 적용됩니다.

```scss
$color: red;

.header {
  color: $color; // red
}

$color: blue;

.footer {
  color: $color; // blue
}
```

### List

쉼표(`,`) 또는 공백으로 구분된 값들의 나열을 List 변수라고 합니다.\
자바스크립트의 배열처럼 인덱스로 접근 가능합니다. (1부터 시작)

```scss
$colors: red green blue;

.item-1 {
  color: nth($colors, 1); // red
}
.item-2 {
  color: nth($colors, 2); // green
}
```

#### 리스트 관련 함수

| 함수                   | 설명                         |
| ---------------------- | ---------------------------- |
| `length($list)`        | 리스트 길이 반환             |
| `nth($list, n)`        | n번째 요소 반환 (1부터 시작) |
| `join($list1, $list2)` | 리스트 병합                  |
| `append($list, $val)`  | 값 추가                      |

### Maps

맵(Map)은 키-값 쌍(key-value pair) 으로 이루어진 자료구조로, 자바스크립트의 객체와 비슷합니다.

```scss
$colors: (
  primary: #3498db,
  danger: #e74c3c,
);
```

#### 값 꺼내기

```scss
.button {
  background-color: map-get($colors, primary); // #3498db
}
```

또는 Sass 1.3.0 이상에서는

```scss
.button {
  background-color: $colors.primary;
}
```

#### 반복 사용

```scss
$themes: (
  light: #fff,
  dark: #000,
);

@each $name, $value in $themes {
  .theme-#{$name} {
    background-color: $value;
  }
}
```

#### Maps 관련 함수

| 함수                      | 설명                |
| ------------------------- | ------------------- |
| `map-get($map, $key)`     | 해당 키의 값 반환   |
| `map-keys($map)`          | 모든 키 리스트 반환 |
| `map-values($map)`        | 모든 값 리스트 반환 |
| `map-has-key($map, $key)` | 키 존재 여부 확인   |
| `map-merge($map1, $map2)` | 두 맵 병합          |

## 꼭 기억해야 할 점

Sass는 브라우저에서 직접 실행되지 않습니다.\
대신 개발 단계에서만 사용되며, 프로덕션 단계에서는 반드시 일반 CSS로 컴파일해야 합니다.

즉, Sass는 CSS를 더 잘 작성하기 위한 "개발 보조 도구"일 뿐이고, 브라우저가 해석하는 건 언제나 `.css` 파일입니다.
