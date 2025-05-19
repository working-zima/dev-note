---
description: 클라이언트 측에서 파일을 저장할 수 있게 해주는 솔루션인 FileSaver.js에 대한 내용을 기록합니다.
---

# FileSaver.js

FileSaver.js는 클라이언트 측에서 파일을 저장할 수 있게 해주는 솔루션이며, 클라이언트에서 파일을 생성하는 웹 애플리케이션에 적합합니다.\
하지만 서버에서 파일이 오는 경우에는 [Content-Disposition][8] 응답 헤더를 사용하는 것이 더 나은 브라우저 호환성을 제공하므로 이를 먼저 시도해보는 것을 추천합니다.

`canvas.toBlob()`을 사용하여 캔버스를 저장하고 싶다면, 다양한 브라우저에서 작동하는 [canvas-toBlob.js][2]를 확인해보세요.

## 대용량 파일 처리

Blob의 크기 제한보다 큰 파일을 저장해야 하거나, RAM이 부족한 경우에는 [StreamSaver.js][7]를 사용하는 것이 좋습니다.  
StreamSaver.js는 새로운 Streams API를 활용하여 하드디스크에 비동기적으로 데이터를 직접 저장할 수 있으며,  
진행 상태 표시, 취소, 저장 완료 여부 확인 기능도 지원합니다.

## 지원 브라우저

| 브라우저         | Blob 지원 방식 | 파일 이름 지원 | 최대 Blob 크기 | 의존성                                        |
| ---------------- | -------------- | -------------- | -------------- | --------------------------------------------- |
| Firefox 20+      | Blob           | 가능           | 800 MiB        | 없음                                          |
| Firefox < 20     | data: URI      | 불가능         | 해당 없음      | [Blob.js](https://github.com/eligrey/Blob.js) |
| Chrome           | Blob           | 가능           | [2GB][3]       | 없음                                          |
| Chrome (Android) | Blob           | 가능           | [RAM/5][3]     | 없음                                          |
| Edge             | Blob           | 가능           | ?              | 없음                                          |
| IE 10+           | Blob           | 가능           | 600 MiB        | 없음                                          |
| Opera 15+        | Blob           | 가능           | 500 MiB        | 없음                                          |
| Opera < 15       | data: URI      | 불가능         | 해당 없음      | [Blob.js](https://github.com/eligrey/Blob.js) |
| Safari 6.1+\*    | Blob           | 불가능         | ?              | 없음                                          |
| Safari < 6       | data: URI      | 불가능         | 해당 없음      | [Blob.js](https://github.com/eligrey/Blob.js) |
| Safari 10.1+     | Blob           | 가능           | 해당 없음      | 없음                                          |

### 기능 지원 여부 감지

```js
try {
  var isFileSaverSupported = !!new Blob();
} catch (e) {}
```

### IE 10 미만

Flash 기반 polyfill 없이도 텍스트 파일 저장이 가능합니다.  
[ChenWenBrian 및 koffsyrup의 `saveTextAs()`](https://github.com/koffsyrup/FileSaver.js#examples)를 참고하세요.

### Safari 6.1 이상

Blob은 저장 대신 열리는 경우가 있으며, 사용자에게 <kbd>⌘</kbd>+<kbd>S</kbd>를 누르도록 안내해야 할 수도 있습니다.  
다운로드를 강제하기 위해 `application/octet-stream` MIME 타입을 사용하는 것은 Safari에서 문제를 일으킬 수 있습니다.  
[관련 이슈](https://github.com/eligrey/FileSaver.js/issues/12#issuecomment-47247096)

### iOS

`saveAs`는 반드시 터치 이벤트나 클릭과 같은 사용자 상호작용 내에서 호출되어야 하며,  
`setTimeout`을 사용하면 작동하지 않을 수 있습니다.  
iOS에서는 `saveAs`가 다운로드 대신 새 창을 여는 문제가 있으며,  
[WebKit 버그 리포트](https://bugs.webkit.org/show_bug.cgi?id=167341)를 통해 해결을 요청할 수 있습니다.

## 문법

### ES 모듈 방식

```js
import { saveAs } from "file-saver";
```

### 함수 서명

```ts
// saveAs(다운로드할 데이터, 데이터 이름, 옵션)
saveAs(blob: Blob | File | string, filename?: string, options?: { autoBom?: boolean })
```

- `autoBom: true`를 전달하면, Blob 타입이 `charset=utf-8`일 경우 Unicode BOM을 자동으로 추가합니다.

## 사용 예시

### 텍스트 저장 (require 방식)

```js
const FileSaver = require("file-saver");

const blob = new Blob(["Hello, world!"], { type: "text/plain;charset=utf-8" });

FileSaver.saveAs(blob, "hello world.txt");
```

### 텍스트 저장 (import 방식)

```js
import { saveAs } from "file-saver";

const blob = new Blob(["Hello, world!"], { type: "text/plain;charset=utf-8" });

saveAs(blob, "hello world.txt");
```

### URL 저장

```js
import { saveAs } from "file-saver";

saveAs("https://httpbin.org/image", "image.jpg");
```

- 같은 origin의 URL이면 `<a download>` 사용
- 다른 origin이면 CORS 확인 후 blob URL로 다운로드 시도

### canvas 저장

```js
import { saveAs } from "file-saver";

const canvas = document.getElementById("my-canvas");

canvas.toBlob(function (blob) {
  saveAs(blob, "pretty image.png");
});
```

※ 모든 브라우저에서 `canvas.toBlob()`이 지원되지는 않으며, [canvas-toBlob.js][6]로 polyfill 가능

### File 객체 저장

```js
import { saveAs } from "file-saver";

const file = new File(["Hello, world!"], "hello world.txt", {
  type: "text/plain;charset=utf-8",
});

saveAs(file);
```

- IE 및 Edge는 `File` 생성자 지원 안 함 → Blob 사용 권장

## 기본적인 Node.JS 설치

```bash
npm install file-saver --save
bower install file-saver
```

### 추가적인 TypeScript 정의 파일

```bash
npm install @types/file-saver --save-dev
```

## 링크

- [FileSaver.js 데모][1]
- [canvas-toBlob.js][2]
- [Chrome Blob 버그 논의][3]
- [MDN Blob 문서][4]
- [Blob.js][5]
- [canvas-toBlob.js (polyfill)][6]
- [StreamSaver.js][7]
- [Content-Disposition 가이드][8]

[1]: http://eligrey.com/demos/FileSaver.js/
[2]: https://github.com/eligrey/canvas-toBlob.js
[3]: https://bugs.chromium.org/p/chromium/issues/detail?id=375297#c107
[4]: https://developer.mozilla.org/en-US/docs/DOM/Blob
[5]: https://github.com/eligrey/Blob.js
[6]: https://github.com/eligrey/canvas-toBlob.js
[7]: https://github.com/jimmywarting/StreamSaver.js
[8]: https://github.com/eligrey/FileSaver.js/wiki/Saving-a-remote-file#using-http-header
