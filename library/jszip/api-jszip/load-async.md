# loadAsync(data [, options])

기존 zip 파일을 읽어 현재 JSZip 인스턴스의 현재 폴더 수준에 병합합니다.\
이미 해당 이름을 가진 항목이 존재할 경우, 불러온 파일이 기존 파일을 덮어씁니다.

즉, **zip 파일을 파싱하여 JSZip이 이해할 수 있는 파일 객체로 변환해주는 함수**입니다.

JSZip은 이 File 객체를 내부적으로 파싱하여 내부 구조(폴더, 파일 이름, 경로 등)를 읽고, 각 항목에 대해 `JSZipObject` 형태의 파일 객체를 생성합니다.\
이때 각 파일의 데이터는 여전히 압축된 상태로 있으며, 해당 파일의 내용을 요청 (`.async(...)`)할 때 비로소 압축이 해제됩니다.\
구조 파싱도 연산 비용 때문에 시간이 걸릴 수 있으므로, 동기(synchronous) 로 하면 브라우저가 멈춥니다.\
그래서 JSZip은 `loadAsync()`라는 비동기 API를 제공합니다.

**Returns** : 업데이트된 JSZip 객체의 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)를 반환합니다.  
유효하지 않은 zip 데이터이거나, 지원되지 않는 기능(멀티볼륨, 암호화 등)을 포함할 경우 실패합니다.

**Since**: v3.0.0

v3.8.0부터는 zip slip 공격을 방지하기 위해 상대 경로(`..`)가 포함된 파일명을 자동 정리합니다.  
예: `../../../example.txt` → `example.txt`,  
`src/images/../file.txt` → `src/file.txt`  
원래 파일명은 `unsafeOriginalName` 필드로 접근할 수 있습니다.

## Arguments

| name    | type                                                                | description                 |
| ------- | ------------------------------------------------------------------- | --------------------------- |
| data    | String / Array / ArrayBuffer / Uint8Array / Buffer / Blob / Promise | zip 데이터                  |
| options | object                                                              | zip 로딩에 사용할 옵션 객체 |

### options 필드 설명

`loadAsync(data, options)` 메서드는 기존 ZIP 파일을 비동기적으로 읽어 `JSZip` 인스턴스에 병합하는 기능을 제공합니다\
이 메서드는 다양한 옵션을 통해 ZIP 파일의 로딩 방식을 세부적으로 제어할 수 있습니다.\
아래는 주요 옵션들의 설명입니다.

| 이름                     | 타입       | 기본값            | 설명                                          |
| ------------------------ | ---------- | ----------------- | --------------------------------------------- |
| `base64:`                | `boolean`  | `false`           | base64로 인코딩된 경우 `true`                 |
| `checkCRC32:`            | `boolean`  | `false`           | CRC32 무결성 체크 활성화 여부                 |
| `optimizedBinaryString:` | `boolean`  | `false`           | 입력이 마스크된 바이너리 문자열일 경우 `true` |
| `createFolders:`         | `boolean`  | `false`           | 폴더 경로를 실제 폴더로 생성할지 여부         |
| `decodeFileName:`        | `function` | UTF-8 디코딩 함수 | 파일명/주석 디코딩용 사용자 정의 함수         |

> 전달된 data는 그대로 유지되므로, 이후 해당 데이터를 수정하면 zip 내용도 영향을 받습니다.

## 지원하는 zip 기능

- DEFLATE 압축
- data descriptor
- ZIP64
- UTF-8 파일명 및 내용

## 지원하지 않는 zip 기능

- 암호화된 zip 파일
- 멀티볼륨 zip

### base64 옵션

base64는 바이너리를 텍스트처럼 다루고 싶을 때 쓰는 인코딩 방식입니다.\
Base64로 인코딩된 ZIP 데이터를 로딩하며, base64 옵션을 `true`로 설정하여 올바르게 디코딩합니다.

```js
import JSZip from "jszip";

const zip = await JSZip.loadAsync("UEsDBAoDAAAAAJxs8T...AAAAAA==", {
  base64: true,
});
```

### checkCRC32 옵션

checkCRC32는 파일이 중간에 손상됐는지 확인하는 간단한 무결성 검사 도구입니다.

```js
zip.loadAsync(bin).then(success, error); // CRC32 체크 안함 → 성공
zip.loadAsync(bin, { checkCRC32: true }).then(success, function (e) {
  // CRC32 불일치 → 오류 발생
  console.error("오류:", e);
});
```

### createFolders 옵션

`createFolders: true` 옵션을 설정하면 경로상의 폴더들도 `zip.files`에 명시적으로 포함됩니다.\

특정 방식으로 생성된 ZIP 포맷은 압축할 때 실제 폴더가 존재하더라도, ZIP 파일에는 반드시 그 폴더가 따로 저장되는 건 아닙니다.\
zip 포맷은 단지 파일 경로를 슬래시(`/`)로 포함한 문자열로만 저장해도 유효합니다.

zip 내부에는 "`folderA/folderB/file.txt`" 하나만 존재해도 압축 해제 후 실제 파일 시스템은 파일 경로에 따라 폴더를 생성합니다.

```jsx
// 특정 방법으로 압축했을 때 (터미널(zip CLI)로 파일만 직접 지정한 경우, JSZip으로 직접 만들 때 zip.folder() 없이 .file()만 쓴 경우 등)
📄 folderA/folderB/file.txt


// 압축 풀었을 때 (폴더가 만들어짐)
📁 folderA/
   📁 folderB/
      📄 file.txt
```

그런 특정 zip 파일에 대해서 `createFolders: true`는 JSZip이 파싱할 때 폴더도 객체로 포함시킬지를 설정하는 옵션입니다.

#### createFolders 사용법

```js
// "bin"에는 folder1/folder2/folder3/file1.txt 포함

zip.loadAsync(bin).then(function (zip) {
  console.log(zip.files);
  // folder1/folder2/folder3/file1.txt
});

zip.loadAsync(bin, { createFolders: true }).then(function (zip) {
  console.log(zip.files);
  // folder1/
  // folder1/folder2/
  // folder1/folder2/folder3/
  // folder1/folder2/folder3/file1.txt
});
```

### decodeFileName 옵션

zip 파일 안의 파일 이름이 UTF-8로 인코딩되지 않았을 때, 이름이 깨지는 문제를 해결하기 위한 옵션입니다.\
기본적으로 JSZip은 파일 이름이 UTF-8로 인코딩되어 있다고 가정합니다.\
하지만 예전 zip 파일이나 특정 국가(중국어, 러시아어, 일본어 등)에서 생성된 zip 파일은 다음과 같은 로컬 문자 인코딩을 사용합니다.

| 국가/지역 | 인코딩 예     |
| --------- | ------------- |
| 러시아    | CP866, KOI8-R |
| 일본      | Shift-JIS     |
| 중국      | GB2312, GBK   |
| 한국      | EUC-KR        |

이런 파일을 JSZip으로 열면 파일 이름이 깨져서 `���.txt`처럼 나올 수 있습니다.

#### decodeFileName 사용법

```bash
npm install jszip iconv-lite
```

```js
import JSZip from "jszip";
import iconv from "iconv-lite";

// 바이너리 데이터는 보통 File, Blob, ArrayBuffer 형태
async function loadZipWithCustomEncoding(file: Blob | ArrayBuffer) {
  const zip = await JSZip.loadAsync(file, {
    decodeFileName: (bytes) => iconv.decode(bytes, "cp866"), // 러시아어 예시
  });

  for (const [name, zipEntry] of Object.entries(zip.files)) {
    console.log("파일 이름:", name);
  }
}
```

## 기타 예시

브라우저에서 zip 파일을 Ajax/XHR로 받아올 때 사용하는 예시입니다.

```js
const zip = new JSZip();

zip.loadAsync(zipDataFromXHR);
```

Node.js 환경에서 `fs.readFile()`로 zip 파일을 읽은 후 JSZip으로 파싱하는 예제입니다.

```js
import fs from "fs/promises";
import JSZip from "jszip";

async function loadZipFromDisk() {
  const data = await fs.readFile("hello.zip"); // Buffer
  const zip = await JSZip.loadAsync(data);

  for (const name in zip.files) {
    console.log("파일명:", name);
  }
}
```

### 하위 폴더로 로드

zip 파일 안의 모든 파일들을 `subfolder/` 아래에 위치시킨다는 뜻입니다.\
내부적으로는 `"subfolder/file.txt"`처럼 경로가 자동 붙습니다.\
사용자 zip을 "임시 폴더" 아래에 넣고 병합하려고 할 때 유용합니다.

```js
import JSZip from "jszip";

async function loadZipInSubfolder(bin: Blob | ArrayBuffer) {
  const zip = new JSZip();
  await zip.folder("subfolder")!.loadAsync(bin);

  console.log(Object.keys(zip.files));
  // 출력: ["subfolder/file1.txt", "subfolder/folder1/file2.txt"]
}
```

### 여러 zip을 순차적으로 병합

첫 번째 zip 파일을 로딩하고, 같은 zip 인스턴스에 두 번째 zip을 덮어쓰기 병합합니다.\
여러 개의 zip 파일을 하나로 병합할 때 동일한 파일명이 있을 경우 나중 zip이 우선이 됩니다.

```js
const zip = new JSZip();
await zip.loadAsync(bin1);
await zip.loadAsync(bin2); // bin2에 동일한 파일명이 있으면 bin1 내용을 덮어씀

console.log(Object.keys(zip.files));
```

### 경로 정리된 zip 읽기

ZIP 파일에는 **경로를 악용한 공격(zip slip)**이 있을 수 있습니다.\
(예: `"../../etc/passwd"` 같은 경로를 사용해서 시스템 파일을 덮어씌우려는 공격)

`.loadAsync()`는 이러한 경로를 자동으로 정리하여, `"../../example.txt"` → `"example.txt"`처럼 안전한 경로로 바꿉니다.\
원래 경로는 `.unsafeOriginalName`에 따로 저장합니다.

```js
import fs from "fs/promises";
import JSZip from "jszip";

async function safeZipLoad() {
  const data = await fs.readFile("unsafe.zip");
  const zip = await JSZip.loadAsync(data);

  for (const name in zip.files) {
    console.log("Safe name:", name);
    console.log("Original:", zip.files[name].unsafeOriginalName); // zip slip 방지 전 이름
  }
}
```
