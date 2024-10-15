# XSS

HTML, JavaScript 등에서 사용되는 XSS(크로스 사이트 스크립팅) 공격을 방어하기 위한 필터링 도구입니다.\
이는 클라이언트와 서버 모두에서 사용할 수 있으며, HTML과 JavaScript 코드 내에서 악성 스크립트를 제거하여 안전한 출력을 보장합니다.\
이를 통해 XSS 공격을 방지할 수 있습니다.

## 주요 사용 방법

### Node.js에서 사용

```js
var xss = require('xss');
var html = xss('<script>alert("xss");</script>');
console.log(html);  // <alert("xss");>
```

`xss()` 함수는 입력된 HTML을 필터링하여 악성 코드(예: `<script>` 태그)를 제거합니다.

### 브라우저에서 사용

```html
<script src="https://raw.github.com/leizongmin/js-xss/master/dist/xss.js"></script>
<script>
  var html = filterXSS('<script>alert("xss");</scr' + 'ipt>');
  alert(html);  // alert("xss");가 실행되지 않음
</script>
```

`filterXSS()` 함수는 브라우저 환경에서 XSS 공격을 필터링합니다.

### AMD 모듈 사용

```html
<script>
require.config({
  baseUrl: './'
});
require(['xss'], function (xss) {
  var html = xss('<script>alert("xss");</scr' + 'ipt>');
  alert(html);
});
</script>
```

AMD 방식으로 `xss`를 로드해 사용할 수 있습니다.

## 커스터마이징 옵션

`xss()` 함수는 다양한 커스터마이징 옵션을 지원합니다.

### 사용자 정의 필터 규칙

```js
options = {};  // 커스텀 규칙
html = xss('<script>alert("xss");</script>', options);
```

사용자 정의 규칙을 지정하여 필터링 동작을 세부적으로 제어할 수 있습니다.

### 화이트리스트

```js
var options = {
  whiteList: {
    a: ['href', 'title', 'target']
  }
};
html = xss('<a href="#" onclick="hello()"><i>Hello</i></a>', options);
// 결과: <a href="#">Hello</a>
```

지정된 태그와 속성만 허용되며, 그 외의 태그와 속성은 제거됩니다.

### 화이트리스트에 없는 태그 제거

```js
code:<script>alert(/xss/);</script>
stripIgnoreTag: true로 설정하면, 화이트리스트에 없는 태그가 제거됩니다.
```

### 화이트리스트에 없는 태그 및 태그 내용 제거

```js
code:<script>alert(/xss/);</script>
stripIgnoreTagBody: ['script']로 설정하면, <script> 태그와 그 안의 내용이 모두 제거됩니다.
```

### HTML 주석 제거

```js
code:<!-- something --> END
```

`allowCommentTag: false`로 설정하면, HTML 주석이 제거됩니다.

### 핸들러 함수

### 매칭된 태그에 대한 처리 함수

```js
function onTag (tag, html, options) {
  // 태그에 대한 사용자 정의 처리
}
```

이 함수는 화이트리스트에 맞는 태그나 무시된 태그를 커스텀 처리할 수 있습니다.

### 매칭된 속성에 대한 처리 함수

```js
function onTagAttr (tag, name, value, isWhiteAttr) {
  // 속성에 대한 사용자 정의 처리
}
```

### 화이트리스트에 없는 태그 처리 함수

```js
function onIgnoreTag (tag, html, options) {
  // 무시된 태그에 대한 처리
}
```

### 화이트리스트에 없는 속성 처리 함수

```js
function onIgnoreTagAttr (tag, name, value, isWhiteAttr) {
  // 무시된 속성에 대한 처리
}
```

### HTML 이스케이프 처리 함수

```js
function escapeHtml (html) {
  return html.replace(/</g, '&lt;').replace(/>/g, '&gt;');
}
```

HTML 이스케이프 처리 함수는 기본적으로 제공되며, 필요하면 수정 가능합니다.

### 속성 값 이스케이프 처리 함수

```js
function safeAttrValue (tag, name, value) {
  // 속성 값에 대한 사용자 정의 이스케이프 처리
}
```

## 명령줄 도구

명령줄 도구를 사용하여 파일을 처리할 수 있습니다:

```bash
xss -i <input_file> -o <output_file>
```

또한 설정 파일을 통해 커스텀 필터링 옵션을 사용할 수 있습니다:

```json
{
  "whiteList": {
    "p": ["id", "style"]
  },
  "stripIgnoreTag": true,
  "stripIgnoreTagBody": true
}
```

이 도구는 테스트 모드를 제공하여 실시간으로 입력된 HTML 코드의 필터링 결과를 확인할 수 있습니다:

```bash
xss -t
```

이를 통해 XSS 공격을 쉽게 방어할 수 있습니다.

## 자료

[XSS](https://jsxss.com/en/index.html)
