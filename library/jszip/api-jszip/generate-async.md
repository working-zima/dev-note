# generateAsync(options[, onUpdate])

현재 JSZip 인스턴스에 포함된 파일 및 폴더를 압축하여 zip 파일로 비동기 생성합니다.\
반환값은 지정한 타입(type)에 따라 변환된 Promise 객체입니다.

**Returns** : 생성된 zip 파일의 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)를 반환합니다.

`options.type`에 지정된 형식을 브라우저가 지원하지 않으면 오류가 발생합니다.  
지원 여부는 [JSZip.support]({{site.baseurl}}/documentation/api_jszip/support.html)에서 확인할 수 있습니다.

## Arguments

| name                       | type     | default           | description                                                                                 |
| -------------------------- | -------- | ----------------- | ------------------------------------------------------------------------------------------- |
| options                    | object   |                   | zip 생성을 위한 설정 객체                                                                   |
| options.type               | string   |                   | 생성 결과 형식. 필수 항목. \[아래 참조](#type-option)                                       |
| options.compression        | string   | STORE             | 기본 압축 방식 (STORE 또는 DEFLATE). \[자세히](#compression-and-compressionoptions-options) |
| options.compressionOptions | object   | null              | 압축 옵션. 예: `level: 9`                                                                   |
| options.comment            | string   |                   | zip 파일에 추가할 주석                                                                      |
| options.mimeType           | string   | application/zip   | Blob 생성 시 사용할 MIME 타입                                                               |
| options.platform           | string   | DOS               | 플랫폼 종류 (DOS 또는 UNIX 등)                                                              |
| options.encodeFileName     | function | UTF-8 인코딩 함수 | 파일명 인코딩 커스터마이징                                                                  |
| options.streamFiles        | boolean  | false             | 스트리밍 방식 사용 여부                                                                     |
| onUpdate                   | function |                   | 생성 진행 중 호출되는 콜백 함수                                                             |

### type 옵션

가능한 출력 형식:

- base64: base64 문자열, 텍스트로 전달할 때 (예: data URI)
- binarystring (또는 string): 1바이트당 문자 1개인 문자열 (권장되지 않음)
- array: 숫자 배열 (0~255 범위)
- uint8array: Uint8Array 객체, 전송/압축 처리에 적합
- arraybuffer: Binary 처리용 ArrayBuffer 객체
- blob: 브라우저에서 파일 다운로드용 Blob 객체
- nodebuffer: Node.js의 Buffer 객체

브라우저 호환 여부는 [`JSZip.support`]({{site.baseurl}}/documentation/api_jszip/support.html)로 확인

```js
zip.generateAsync({ type: "uint8array" }).then(function (u8) {
  // 결과 처리
});
```

### compression / compressionOptions

압축 방식:

- STORE: 압축 없음
- DEFLATE: 압축 사용

압축 수준 설정 예시:

```js
zip.generateAsync({
  type: "blob",
  compression: "DEFLATE",
  compressionOptions: {
    level: 9,
  },
});
```

이미 압축된 항목은 compression level을 바꿔도 효과가 없습니다.

### comment 옵션

zip 파일 전체에 주석을 추가합니다.\
인코딩은 UTF-8만 지원하며 비ASCII 문자는 호환되지 않는 압축 프로그램에서 깨질 수 있습니다.

```js
zip.generateAsync({
  type: "blob",
  comment: "zip 파일 설명",
});
```

### mimeType 옵션

mimeType은 파일 형식 라벨입니다.

| 파일 이름     | MIME Type               |
| ------------- | ----------------------- |
| `image.png`   | `image/png` → 이미지    |
| `hello.txt`   | `text/plain` → 텍스트   |
| `resume.pdf`  | `application/pdf` → PDF |
| `archive.zip` | `application/zip` → ZIP |

Blob 객체의 MIME 타입을 지정합니다.\
결과로 반환되는 Blob 객체의 `.type` 값을 설정합니다.\
확장자가 `.ods` 등일 경우 유용합니다.

브라우저에서 파일 다운로드 시 올바른 MIME으로 인식되게 하려는 목적으로 사용합니다.
실제 내용은 변하지 않으며, 프로그램에 따라 MIME만 다르게 인식됩니다.

#### mimeType 예시

아래의 예시대로 하면 브라우저에서 다운로드하거나 서버에 업로드할 때 "ODS 파일(무료 오피스 프로그램에서 쓰는 엑셀 형식)처럼 보이도록 MIME을 속일 수 있습니다.

예:

- `.ods`: `application/ods`
- `.epub`: `application/epub+zip`
- `.xlsx`: `application/vnd.openxmlformats-officedocument.spreadsheetml.sheet`

```js
zip.file("mimetype", "application/vnd.oasis.opendocument.spreadsheet");
zip.folder("META-INF").file("manifest.xml", "<xml...>");

const odsBlob = await zip.generateAsync({
  type: "blob",
  mimeType: "application/ods",
});
console.log(odsBlob.type); // → "application/ods"
```

Blob은 브라우저 안에서 메모리 위에 임시로 만들어진 파일이지만, 브라우저는 그게 무슨 파일인지 확실히 알 수 없습니다.
Blob 객체를 다운로드 링크에 붙일 때, 파일 형식을 명확히 지정하지 않으면 `.bin` 또는 `.download`로 저장될 수 있습니다.\
이때 `mimeType`을 지정해주면 브라우저가 올바르게 인식할 수 있습니다.

```js
const blob = await zip.generateAsync({
  type: "blob",
  mimeType: "application/epub+zip",
});
const url = URL.createObjectURL(blob);

const a = document.createElement("a");
a.href = url;
a.download = "book.epub";
a.click();
```

### platform 옵션

압축된 파일의 권한(perms)을 어떤 운영체제 기준으로 저장할지를 지정하는 옵션입니다.\
압축된 파일을 리눅스 서버에 배포할 때 실행 권한이 유지되지 않으면, 실행이 안 될 수 있습니다.

| 값                 | 설명                                        | 사용 권한 필드    |
| ------------------ | ------------------------------------------- | ----------------- |
| `"DOS"` (기본값)   | 윈도우용 zip 스타일                         | `dosPermissions`  |
| `"UNIX"`           | 유닉스 계열 스타일 (`rwx`)                  | `unixPermissions` |
| `process.platform` | 현재 Node.js의 플랫폼 (`win32`, `linux` 등) | JSZip이 자동 매핑 |

주의: Windows에서 `fs.stats.mode`는 실행 권한이 없기 때문에 UNIX 플랫폼으로 강제하면 비정상 작동 가능

```js
import fs from "fs/promises";
import { statSync } from "fs";
import JSZip from "jszip";

const zip = new JSZip();

const path = "./scripts/run.sh";
const content = await fs.readFile(path);
const stat = statSync(path); // Node에서 권한 정보 가져오기

zip.file("run.sh", content, {
  date: stat.mtime, // 수정일
  unixPermissions: stat.mode, // 권한 유지
});

const buffer = await zip.generateAsync({
  type: "nodebuffer",
  platform: "UNIX", // 권한 정보를 제대로 저장
});
```

| 운영체제     | 권장 platform                       |
| ------------ | ----------------------------------- |
| macOS, Linux | `"UNIX"` 또는 `process.platform`    |
| Windows      | `"DOS"` (기본값)만 사용하는 게 안전 |

### encodeFileName 옵션

파일명 및 주석에 사용하는 인코딩 방식을 커스터마이징할 수 있습니다.\
기본은 UTF-8이며, zip 파일에는 인코딩 정보가 저장되지 않기 때문에 비UTF-8 인코딩을 사용할 경우 호환성 문제가 발생할 수 있습니다.

기본적으로 zip 파일은 UTF-8을 쓰는데, 일부 오래된 압축 프로그램(특히 Windows 계열, 일본/중국/러시아에서 만든 zip 등)은 UTF-8을 제대로 인식하지 못해서 파일명이 깨져보일 수 있습니다.\
그래서 인코딩을 예전 방식(예: EUC-KR, Shift-JIS, CP866)으로 강제로 저장할 수 있도록 이 옵션이 존재합니다.

#### encodeFileName를 사용하여 EUC-KR로 저장 예시

```js
import JSZip from "jszip";
import iconv from "iconv-lite";

const zip = new JSZip();

zip.file("한글파일.txt", "내용");

const blob = await zip.generateAsync({
  type: "blob",
  encodeFileName: (name) => iconv.encode(name, "euc-kr"),
});

saveAs(blob, "legacy.zip");
```

### streamFiles 옵션

기본적으로는 모든 파일 데이터를 메모리에 유지하여 zip 파일을 생성합니다.\
이 옵션을 `true`로 설정하면 각 파일을 스트리밍하여 메모리 사용량을 줄일 수 있지만, 일부 zip 프로그램은 스트리밍 방식에서 사용하는 data descriptor를 인식하지 못할 수 있습니다.

#### streamFiles 예시

JSZip은 모든 데이터를 한 번에 압축하고 메모리 상에 보관하면서 zip 전체 생성하기 때문에 파일이 많거나 크면 메모리 폭발 위험 있습니다.

```js
const zip = new JSZip();
zip.file("big.dat", bigData); // 100MB짜리 파일이라면…

const result = await zip.generateAsync({ type: "uint8array" });
```

파일 하나씩 스트림 단위로 압축하고 저장 메모리 점유량이 줄어듭니다.\
대신 일부 zip 앱에서 정상적으로 압축 해제가 안 되는 문제가 발생할 수 있습니다.

```js
const zip = new JSZip();
zip.file("big.dat", largeTextData);

const result = await zip.generateAsync(
  {
    type: "uint8array",
    streamFiles: true, // 메모리 절약
  },
  (metadata) => {
    console.log(`진행률: ${metadata.percent.toFixed(1)}%`);
  }
);
```

streamFiles를 사용하면 JSZip은 zip 포맷의 data descriptor 기능을 활용합니다.\
일부 오래된 unzip 도구는 이 포맷을 지원하지 않아서 압축 해제 시 오류가 나거나, 파일이 아예 보이지 않을 수 있습니다.

매우 많은 파일 또는 대용량 zip(100MB~ 이상)을 배포 대상이 구형 윈도우 사용자가 아닐 때, 내부 API 또는 자동화 용도로 사용하면 좋습니다.

### onUpdate 콜백

zip 파일 생성 중 진행 상황을 알 수 있는 콜백 함수입니다.\
내부적으로 처리된 청크마다 호출되며, 다음과 같은 메타데이터를 받습니다.

| name          | type     | 설명                                            |
| ------------- | -------- | ----------------------------------------------- |
| `percent`     | `number` | 전체 zip 생성 작업의 진행률 (0~100 사이의 실수) |
| `currentFile` | `string` | 현재 처리 중인 파일명 (있을 경우)               |

```js
const blob = await zip.generateAsync({ type: "blob" }, (metadata) => {
  console.log(`진행률: ${metadata.percent.toFixed(2)}%`);
  if (metadata.currentFile) {
    console.log(`현재 처리 중: ${metadata.currentFile}`);
  }
});
```

## 브라우저에서 zip 파일을 만들어 다운로드 예시

Blob은 브라우저 메모리 안의 파일 객체고 `saveAs()`는 이를 진짜 다운로드로 바꿔주는 트리거입니다.

```js
import JSZip from "jszip";
import { saveAs } from "file-saver";

const zip = new JSZip();
zip.file("hello.txt", "안녕하세요!");

const blob = await zip.generateAsync({ type: "blob" }); // zip 파일을 브라우저에서 사용할 수 있는 Blob 객체로 생성
saveAs(blob, "hello.zip"); // Blob을 다운로드 링크 없이 곧바로 저장할 수 있게 해줌
```

### base64로 만든 후 다운로드 링크 만들기

#### 브라우저

```js
const base64 = await zip.generateAsync({ type: "base64" }); // zip 파일을 base64 문자열 형태로 생성

const a = document.createElement("a");
a.href = "data:application/zip;base64," + base64; // base64 데이터를 데이터 URI로 만들어 브라우저에 바로 다운로드 지시
a.download = "file.zip";
a.click(); // 클릭하면 다운로드를 트리거
```

#### Node.js에서 zip 생성 후 파일로 저장

```js
import fs from "fs/promises";
import JSZip from "jszip";

const zip = new JSZip();
zip.folder("folder_1").folder("folder_2").file("hello.txt", "hello");

const buffer = await zip.folder("folder_1")!.generateAsync({
  type: "nodebuffer", // Node.js에서 사용 가능한 Buffer 객체로 zip 생성
});

await fs.writeFile("hello.zip", buffer); // zip 파일을 디스크에 저장
```
