# 클린코드 for 자바스크립트(Browser&WebAPI)

## 1. NodeList

### Node

문서내에 모든 객체

### Element

Tag로 둘러싸인 요소

![Element](./img/tags.png)

html 문서에서 `body`, `main` 등은 node, `>`는 node를 이어주는 edge가 됩니다.

`querySelector`는 일반적인 요소를 가져오지만 `querySelectorAll`은 NodeList 형태로 가져옵니다.
NodeList를 배열로 형 변환 하면 일반적인 배열로 볼 수 있게됩니다.

```javascript
const anchor = document.querySelector("a");
anchor; // <a href='#'></a>

const arr = document.querySelectorAll("a");
arr; // NodeList [...]

const arrFromNode = [...arr];
arrFromNode; // [...]
```

## 2. innerHTML

`innerHTML` 은 요소(element) 내에 포함 된 HTML 또는 XML 마크업을 가져오거나 설정합니다.
`innerHTML`을 사용하면 간편하게 HTML을 추가하거나 변경할 수 있지만, XSS (Cross-Site Scripting) 공격에 취약합니다.

따라서 `innerHTML` 대신 `insertAdjacentHTML` 사용을 권장합니다.

### insertAdjacentHTML

`insertAdjacentHTML` 메서드는 지정된 텍스트를 HTML 혹은 XML로 파싱하고 결과 노드들을 지정된 위치의 DOM 트리에 삽입합니다.

```html
<html>
  <body>
    <main></main>
  </body>
  <script>
    <!-- innerHTML -->
    document.querySelector("main").innerHTML = "<h1>Hello World</h1>";

    <!-- insertAdjacentHTML -->
    document
      .querySelector("main")
      .insertAdjacentHTML("afterbegin", "<h1>Hello World</h1>");
  </script>
</html>
```

### insertAdjacentText

만약 문자열만 렌더링하고 싶다면 `innerHTML` 대신 `insertAdjacentText`를 사용하는 것이 명시적으로도 좋습니다.

```html
<html>
  <body>
    <main></main>
  </body>
  <script>
    <!-- innerHTML -->
    document.querySelector("main").innerHTML = "Hello World";

    <!-- innerText -->
    document
      .querySelector("main")
      .insertAdjacentText("afterbegin", "Hello World");
  </script>
</html>
```

## 3. Data Attributes

HTML의 속성(Attributes)은 태그에 추가 정보를 제공하는 데 사용됩니다.

### 비표준 속성

존재하지 않는 속성을 강제로 사용하여도 문제없이 동작합니다.
그렇기 때문에 프로그래머들이 본인만의 속성을 사용하며 문제가 되었습니다.

```html
<html>
  <body>
    <h1 안녕="하세요">Hello World</h1>
    <main>main</main>
  </body>
</html>
```

#### 데이터 속성

비표준 속성을 의미있게 사용할 수 있도록 제안 해준 속성입니다.
`data`라는 prefix 뒤에 케밥 케이스로 사용합니다.

```html
<html>
  <body>
    <h1 안녕="하세요">Hello World</h1>
    <main
      id="electriccars"
      data-columns="3"
      data-index-number="1234"
      data-parent-world="cars"
    >
      main
    </main>
  </body>
</html>
```

데이터 속성은 `dataset`으로 접근할 수 있습니다.
자바스크립트에서는 케밥 케이스를 사용하지 않기 때문에 카멜케이스로 가져오면 됩니다.
또한 `delete`를 사용해서 DOM에서 지울 수 있습니다.

```html
<html>
  <body>
    <h1 안녕="하세요">Hello World</h1>
    <main
      id="electriccars"
      data-columns="3"
      data-index-number="1234"
      data-parent-world="cars"
    >
      main
    </main>
  </body>
  <script>
    const main = document.querySelector("main");

    console.log(main.dataset.columns); // '3'
    console.log(main.dataset.indexNumber); // '12314'
    delete main.dataset.parentWorld;
  </script>
</html>
```

데이터 속성은 CSS에서도 사용할 수 있으며 개발환경에서 테스트할 때 `data-cy`라는 데이터 속성을 자주 사용하기도 합니다.

## 4. Black Box Event Listener

Black Box Event Listener는 추상화가 너무 과하게 되거나 명시적인 코드가 아닌 경우, 내부 구현이 어떻게 동작될지 예측할 수 없는 `Event Listener`를 말합니다.

### 예시1

```javascript
const button = document.querySelector("button");

button.addEventListener("click", onClick);
```

위의 코드의 경우 버튼을 클릭하면 `onClick`이 반응형으로 실행되는데 `onClick` 이라는 네이밍은 함수가 어떤 역할을 하는지 알 수가 없습니다.
(버튼을 클릭하면 클릭함수를 실행합니다.)

`getLog`라는 네이밍을 사용하면 이벤트가 어떤 역할을 하는지 알기 쉽습니다.
(버튼을 클릭하면 로그를 가져오는 함수를 실행합니다.)

```javascript
const button = document.querySelector("button");

button.addEventListener("click", getLog);
```

### 예시2

searchbar를 만들때 `handleClick`이라는 네이밍을 지으면, 무엇을 제출하는지 엔터에 반응을 하는지에 대한 정보가 없습니다.
`handleSearch`라는 네이밍을 지으면 버튼을 클릭하거나 keyup이 된다면 검색을 한다는 정보를 제공할 수 있습니다.

```javascript
const button = document.querySelector('button')

const handleSearch = () => {
  1. input을 받는 코드
  2. 유효성을 검사하는 코드
  3. form을 전송하는 코드
}

button.addEventListener('click', handleSearch)
button.addEventListener('keyup', handleSearch)
form.addEventListener('onsubmit', handleSearch)
```

## 참고

[클린코드 자바스크립트(JavaScript)](https://www.udemy.com/course/clean-code-js/)
