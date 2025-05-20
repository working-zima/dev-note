# How to write a file / give it to the user

## 브라우저에서

JavaScript만으로 파일을 다운로드하려면 **최신 브라우저**가 필요합니다.\
**IE 10 미만 브라우저**에서는 작동하지 않으며, 이런 경우엔 Flash 기반 polyfill을 사용해야 합니다.

### 가장 간단한 방법: Blob + FileSaver.js

최신 브라우저에서는 [`FileSaver.js`](https://github.com/eligrey/FileSaver.js) 라이브러리를 사용하는 것이 가장 쉬운 방법입니다.

```js
import JSZip from "jszip";
import { saveAs } from "file-saver";

const zip = new JSZip();
zip.file("hello.txt", "안녕하세요!");

const blob = await zip.generateAsync({ type: "blob" });
saveAs(blob, "hello.zip");
```

- `generateAsync({ type: "blob" })`: 메모리에서 zip 파일 생성

- `saveAs(...)`: 브라우저 다운로드 트리거

### 오래된 브라우저에서 대체 방식: data URI (비추천)

구형 브라우저 호환을 위해 base64 문자열을 직접 다운로드할 수도 있습니다.

```js
const base64 = await zip.generateAsync({ type: "base64" });
location.href = "data:application/zip;base64," + base64;
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

### 더 이상 사용하지 않는 방식들

- **Flash 기반 Downloadify**: 현재 브라우저 대부분이 Flash 미지원
- **Google Gears**: 프로젝트 종료됨
- **IE9 이하**: 지원 불가능 (Blob/URL API 없음)

### 브라우저 지원 요약

| 브라우저 | FileSaver.js 방식 지원 | data URI 사용 시 문제    |
| -------- | ---------------------- | ------------------------ |
| Chrome   | ✅ 정상 다운로드       | `"download.zip"` 사용    |
| Firefox  | ✅                     | 무작위 이름 또는 `.part` |
| Safari   | ✅                     | `"Unknown"` 저장됨       |
| IE 10+   | ✅                     | 일부 이름 오류 발생      |
| IE < 10  | ❌ Blob API 없음       | 사용 불가                |

## Node.js에서

JSZip은 `Buffer`를 생성할 수 있기 때문에, 다음과 같이 zip 파일을 만들어 저장할 수 있습니다.  
Node.js에서는 스트림 방식 대신 `fs/promises.writeFile()`과 `generateAsync({ type: "nodebuffer" })`를 사용하는 것이 더 간단하고 널리 사용됩니다.

```js
import { writeFile } from "fs/promises";
import JSZip from "jszip";

const zip = new JSZip();
zip.file("hello.txt", "안녕하세요!");

const buffer = await zip.generateAsync({ type: "nodebuffer" });
await writeFile("out.zip", buffer);

console.log("out.zip written.");
```

- `generateAsync({ type: "nodebuffer" })`: Node.js에서 사용할 수 있는 Buffer 생성
- `writeFile()`로 파일 시스템에 저장

> 예전 방식인 `generateNodeStream()` + `.pipe(fs.createWriteStream(...))`은 스트리밍이 필요할 때만 사용합니다.

## 요약

| 방식                    | 브라우저 지원      | 파일명 제어 | 특징                        |
| ----------------------- | ------------------ | ----------- | --------------------------- |
| FileSaver.js (브라우저) | 최신 브라우저      | ✅ 가능     | 가장 안정적인 브라우저 방식 |
| Data URI (브라우저)     | 구형 일부 브라우저 | ❌ 불가능   | 간단하지만 파일명 제어 불가 |
| Node.js Buffer          | 서버 환경          | ✅ 가능     | 간단한 파일 저장            |
| Node.js 스트림 방식     | 서버 환경          | ✅ 가능     | 대용량 처리 시 유리         |
