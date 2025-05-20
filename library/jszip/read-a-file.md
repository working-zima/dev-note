# How to read a file

이 페이지에서는 **기존 zip 파일을 읽는 방법**과 **기존 파일을 zip 파일에 추가하는 방법**을 설명합니다.

## 브라우저에서

### zip 파일 다운로드 후 읽기 (`fetch` 사용 – 권장 방식)

`fetch()`와 `arrayBuffer()`를 사용하면 외부 zip 파일을 간단하고 안전하게 읽을 수 있습니다.  
별도 라이브러리 없이 modern 브라우저에서 작동합니다.

```js
const response = await fetch("path/to/content.zip");
const arrayBuffer = await response.arrayBuffer();

const zip = await JSZip.loadAsync(arrayBuffer);
const text = await zip.file("readme.txt")?.async("string");
console.log(text);
```

- `fetch()`로 zip 파일 다운로드
- `arrayBuffer()`로 바이너리 데이터 추출
- `JSZip.loadAsync()`로 zip 파싱

### 로컬 파일 읽기 (FileReader API)

사용자가 zip 파일을 업로드할 경우, `FileReader`로 읽어들일 수 있습니다.  
JSZip은 `ArrayBuffer`를 읽을 수 있기 때문에 다음과 같이 사용합니다:

```js
const reader = new FileReader();

reader.onload = async (e) => {
  const zip = await JSZip.loadAsync(e.target.result);
  const text = await zip.file("readme.txt")?.async("string");
  console.log(text);
};

reader.readAsArrayBuffer(input.files[0]);
```

- `input` 요소의 파일을 읽고
- zip 파일을 메모리에서 파싱
- 개별 파일은 `.file().async()`로 읽음

## Node.js에서

JSZip은 `Buffer`를 읽을 수 있기 때문에 매우 간단하게 사용할 수 있습니다.  
최신 Node.js에서는 `fs/promises`와 `async/await`을 사용하는 방식이 권장됩니다.

### 로컬 zip 파일 읽기

```js
import { readFile } from "fs/promises";
import JSZip from "jszip";

const buffer = await readFile("test.zip");
const zip = await JSZip.loadAsync(buffer);

const file = await zip.file("hello.txt")?.async("string");
console.log(file);
```

- `readFile()`로 zip 파일을 `Buffer` 형태로 읽고
- `JSZip.loadAsync()`로 zip 구조를 파싱
- `.file().async()`로 원하는 파일 내용 추출

### 기존 파일을 zip에 추가하기

```js
import { readFile } from "fs/promises";
import JSZip from "jszip";

const zip = new JSZip();
const data = await readFile("picture.png");

zip.file("picture.png", data);
```

- 기존 파일을 zip 인스턴스에 직접 추가할 수 있음
- Buffer를 그대로 전달하면 압축 대상에 포함됨

### 파일 스트림으로 추가하기

```js
import fs from "fs";
import JSZip from "jszip";

const zip = new JSZip();
const stream = fs.createReadStream("picture.png");

zip.file("picture.png", stream);
```

- 대용량 파일을 메모리에 올리지 않고 zip에 추가 가능
- 내부적으로 스트리밍 처리됨

> 참고: Node.js에서 zip 파일로 저장하려면 `generateAsync({ type: "nodebuffer" })`를 사용해야 합니다.

## 원격 파일 읽기

Node.js에서는 다양한 HTTP 라이브러리를 사용할 수 있습니다.  
(zip 파일은 가능한 한 **Buffer 또는 ArrayBuffer**로 다운로드하는 것이 성능에 좋습니다.)

### Node.js v18+ `fetch` 사용

Node.js v18 이상에서는 브라우저처럼 `fetch()`를 사용할 수 있습니다.  
zip 파일을 다운로드한 후 `arrayBuffer()`로 읽고, JSZip으로 파싱합니다.

```js
import JSZip from "jszip";

const response = await fetch("http://localhost/.../file.zip");
const arrayBuffer = await response.arrayBuffer(); // 바이너리 데이터를 추출

const zip = await JSZip.loadAsync(arrayBuffer); // zip 내용을 비동기적으로 파싱
const text = await zip.file("content.txt")?.async("string"); // 원하는 파일 내용을 읽기

console.log(text);
```

### axios 사용

```js
import axios from "axios";
import JSZip from "jszip";

const { data } = await axios.get("http://localhost/.../file.zip", {
  responseType: "arraybuffer", // 중요!
});

const zip = await JSZip.loadAsync(data);
const text = await zip.file("content.txt")?.async("string");

console.log(text);
```

- `responseType: "arraybuffer"` 설정이 **반드시 필요**
- `Buffer` 대신 `Uint8Array`/`ArrayBuffer`도 JSZip에서 처리 가능

### http 모듈 사용 (ESM + async/await)

```js
import http from "http";
import JSZip from "jszip";

const getZipBuffer = (url) =>
  new Promise((resolve, reject) => {
    http
      .get(url, (res) => {
        if (res.statusCode !== 200) {
          return reject(new Error("HTTP error: " + res.statusCode));
        }

        const chunks = [];
        res.on("data", (chunk) => chunks.push(chunk));
        res.on("end", () => resolve(Buffer.concat(chunks)));
      })
      .on("error", reject);
  });

const buffer = await getZipBuffer("http://localhost/.../file.zip");
const zip = await JSZip.loadAsync(buffer);
const text = await zip.file("content.txt")?.async("string");

console.log(text);
```

- 기본 내장 모듈만 사용하고 싶을 때
- 옛 코드 리팩토링에도 활용 가능

### 더 이상 추천되지 않는 방식: `request` 모듈 (deprecated)

```js
// 사용 비추천: request는 deprecated 상태입니다.
```

> 대신 `axios`, `node-fetch`, 또는 `http` 내장 모듈을 사용하는 것이 안전합니다.
