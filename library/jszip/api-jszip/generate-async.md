# generateAsync(options[, onUpdate])

현재 폴더 수준에서 zip 파일 전체를 비동기적으로 생성합니다.

**Returns** : 생성된 zip 파일의 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)를 반환합니다.

`options.type`에 지정된 형식을 브라우저가 지원하지 않으면 오류가 발생합니다.  
지원 여부는 [JSZip.support]({{site.baseurl}}/documentation/api_jszip/support.html)에서 확인할 수 있습니다.

**Since**: v3.0.0

## Arguments

| name                       | type     | default           | description                                                                                |
| -------------------------- | -------- | ----------------- | ------------------------------------------------------------------------------------------ |
| options                    | object   |                   | zip 생성을 위한 설정 객체                                                                  |
| options.type               | string   |                   | 생성 결과 형식. 필수 항목. [아래 참조](#type-option)                                       |
| options.compression        | string   | STORE             | 기본 압축 방식 (STORE 또는 DEFLATE). [자세히](#compression-and-compressionoptions-options) |
| options.compressionOptions | object   | null              | 압축 옵션. 예: `level: 9`                                                                  |
| options.comment            | string   |                   | zip 파일에 추가할 주석                                                                     |
| options.mimeType           | string   | application/zip   | Blob 생성 시 사용할 MIME 타입                                                              |
| options.platform           | string   | DOS               | 플랫폼 종류 (DOS 또는 UNIX 등)                                                             |
| options.encodeFileName     | function | UTF-8 인코딩 함수 | 파일명 인코딩 커스터마이징                                                                 |
| options.streamFiles        | boolean  | false             | 스트리밍 방식 사용 여부                                                                    |
| onUpdate                   | function |                   | 생성 진행 중 호출되는 콜백 함수                                                            |

### type 옵션

가능한 출력 형식:

- base64: base64 문자열
- binarystring (또는 string): 1바이트당 문자 1개인 문자열 (권장되지 않음)
- array: 숫자 배열 (0~255 범위)
- uint8array: Uint8Array 객체
- arraybuffer: ArrayBuffer 객체
- blob: Blob 객체
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

zip 파일 전체에 주석을 추가합니다. 인코딩은 UTF-8만 지원하며  
비ASCII 문자는 호환되지 않는 압축 프로그램에서 깨질 수 있습니다.

```js
zip.generateAsync({
  type: "blob",
  comment: "zip 파일 설명",
});
```

### mimeType 옵션

Blob 객체의 MIME 타입을 지정합니다. 확장자가 `.ods` 등일 경우 유용합니다.  
실제 내용은 변하지 않으며, 프로그램에 따라 MIME만 다르게 인식됩니다.

```js
zip.file("mimetype", "application/vnd.oasis.opendocument.spreadsheet");
zip.folder("META-INF").file("manifest.xml", "<...>");

zip
  .generateAsync({
    type: "blob",
    mimeType: "application/ods",
    compression: "DEFLATE",
  })
  .then(function (odsFile) {
    // odsFile.type == "application/ods"
  });
```

### platform 옵션

- DOS: `dosPermissions` 사용
- UNIX: `unixPermissions` 사용
- Node.js 환경에선 `process.platform` 사용 가능

주의: Windows에서 `fs.stats.mode`는 실행 권한이 없기 때문에 UNIX 플랫폼으로 강제하면 비정상 작동 가능

```js
zip.file(pathname, content, {
  date: stat.mtime,
  unixPermissions: stat.mode,
});

zip.generateAsync({
  type: "nodebuffer",
  platform: process.platform,
});
```

### encodeFileName 옵션

파일명 및 주석에 사용하는 인코딩 방식을 커스터마이징할 수 있습니다.  
기본은 UTF-8이며, zip 파일에는 인코딩 정보가 저장되지 않기 때문에  
비UTF-8 인코딩을 사용할 경우 호환성 문제가 발생할 수 있습니다.

```js
var iconv = require("iconv-lite");

zip.generateAsync({
  type: "uint8array",
  encodeFileName: function (string) {
    return iconv.encode(string, "your-encoding");
  },
});
```

### streamFiles 옵션

기본적으로는 모든 파일 데이터를 메모리에 유지하여 zip 파일을 생성합니다.  
이 옵션을 true로 설정하면 각 파일을 스트리밍하여 메모리 사용량을 줄일 수 있지만,  
일부 zip 프로그램은 스트리밍 방식에서 사용하는 data descriptor를 인식하지 못할 수 있습니다.

```js
zip.generateAsync({
  type: "uint8array",
  streamFiles: true,
});
```

### onUpdate 콜백

zip 파일 생성 중 진행 상황을 알 수 있는 콜백 함수입니다.  
내부적으로 처리된 청크마다 호출되며, 다음과 같은 메타데이터를 받습니다:

| name        | type   | 설명                              |
| ----------- | ------ | --------------------------------- |
| percent     | number | 진행률 (0~100 사이의 실수)        |
| currentFile | string | 현재 처리 중인 파일명 (있을 경우) |

```js
zip.generateAsync({ type: "blob" }, function updateCallback(metadata) {
  console.log("진행률: " + metadata.percent.toFixed(2) + " %");
  if (metadata.currentFile) {
    console.log("현재 파일: " + metadata.currentFile);
  }
});
```

## 기타 예시

```js
zip.generateAsync({ type: "blob" }).then(function (content) {
  saveAs(content, "hello.zip"); // FileSaver.js 사용
});
```

```js
zip.generateAsync({ type: "base64" }).then(function (content) {
  location.href = "data:application/zip;base64," + content;
});
```

```js
zip.folder("folder_1").folder("folder_2").file("hello.txt", "hello");

zip
  .folder("folder_1")
  .generateAsync({ type: "nodebuffer" })
  .then(function (content) {
    require("fs").writeFile("hello.zip", content, function (err) {
      // 저장 완료
    });
  });
```
