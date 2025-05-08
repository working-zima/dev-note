# How to write a file / give it to the user

## 브라우저에서

JavaScript만으로 파일을 다운로드하려면 **최신 브라우저**가 필요합니다.\
**IE 10 미만 브라우저**에서는 작동하지 않으며, 이런 경우엔 Flash 기반 polyfill을 사용해야 합니다.

### 가장 간단한 방법: Blob URL + FileSaver.js

최신 브라우저에서는 `FileSaver.js` 라이브러리를 사용하는 것이 가장 쉬운 방법입니다.

```js
zip.generateAsync({ type: "blob" }).then(function (blob) {
  saveAs(blob, "hello.zip");
});
```

- Chrome, IE10+: `FileSaver API`의 `saveAs()` 사용

- Firefox: 내부적으로 Blob URL을 사용하여 다운로드 처리

> 참고: FileSaver.js는 [여기](https://github.com/eligrey/FileSaver.js)에서 확인 가능

### 오래된 브라우저: Data URI 방식

`data:` URI를 사용하는 방법입니다 (구형 브라우저 일부 지원).

```js
zip.generateAsync({ type: "base64" }).then(function (base64) {
  location.href = "data:application/zip;base64," + base64;
});
```

#### 문제점

- Firefox: `a5sZQRsx.zip.part` 같은 랜덤한 이름 생성됨

- Safari: `Unknown`이라는 이름으로 다운로드됨

- 다운로드 파일명 제어가 어려움

| 브라우저  | 파일 이름 결과                     |
| --------- | ---------------------------------- |
| Opera     | `"default.zip"`                    |
| Firefox   | 무작위 `.part` 확장자 파일         |
| Safari    | `"Unknown"` (확장자 없음)          |
| Chrome    | `"download.zip"` 또는 `"download"` |
| IE (< 10) | 지원 안 됨                         |

### Flash 기반: Downloadify (대안)

Flash 기반의 파일 다운로드 방식입니다. 파일명을 직접 설정할 수 있습니다.

```js
zip = new JSZip();
zip.file("Hello.", "hello.txt");

zip.generateAsync({ type: "base64" }).then(function (base64) {
    Downloadify.create('downloadify', {
        ...
        data: function () {
            return base64;
        },
        ...
        dataType: 'base64'
    });
});
```

> 참고: Flash 기반이라 현재는 거의 사용되지 않으며, **비추천**.

### 구글 Gears (지원 종료됨)

JSZip과 Google Gears를 함께 사용하는 튜토리얼이 있었으나, Google Gears는 현재 **지원 종료**되었습니다.\
(과거 사용 사례 참조용)

## Node.js에서

JSZip은 `Buffer`를 생성할 수 있기 때문에, 다음과 같이 zip 파일을 생성하여 저장할 수 있습니다:

```js
const fs = require("fs");
const JSZip = require("jszip");

const zip = new JSZip();
// zip.file("file", content);
// 기타 조작...

zip
  .generateNodeStream({ type: "nodebuffer", streamFiles: true })
  .pipe(fs.createWriteStream("out.zip"))
  .on("finish", function () {
    // "end" 이벤트가 아닌 "finish" 이벤트에 주의
    console.log("out.zip written.");
  });
```

## 요약

| 방식                | 브라우저 지원 | 파일명 제어 | 특징                    |
| ------------------- | ------------- | ----------- | ----------------------- |
| FileSaver.js        | 최신 브라우저 | 가능        | 가장 안정적인 방식      |
| Data URI            | 구형 일부     | 불가능      | 간단하지만 파일명 이상  |
| Downloadify (Flash) | 구형 브라우저 | 가능        | Flash 필요, 현재는 비추 |
| Node.js (Buffer)    | 서버환경      | 가능        | 파일 스트림 저장 가능   |
