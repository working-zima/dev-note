# file(name, data [,options])

지정한 파일을 zip 파일에 추가하거나 업데이트합니다.\
지원되지 않는 형식의 데이터를 넣는 경우, 나중에 해당 파일에 접근할 때 예외가 발생할 수 있습니다.

**Returns** : 현재 JSZip 인스턴스를 반환합니다 (체이닝 가능).

**Since**: v1.0.0

## Arguments

| name    | type                                                                         | description                                   |
| ------- | ---------------------------------------------------------------------------- | --------------------------------------------- |
| name    | string                                                                       | 파일 이름. 폴더 포함 가능 (폴더 구분자는 `/`) |
| data    | String / ArrayBuffer / Uint8Array / Buffer / Blob / Promise / Node.js stream | 파일의 내용                                   |
| options | object                                                                       | 설정 옵션                                     |

### options 필드 설명

| 이름                  | 타입           | 기본값    | 설명                                                         |
| --------------------- | -------------- | --------- | ------------------------------------------------------------ |
| base64                | boolean        | false     | 데이터가 base64로 인코딩된 경우 true 설정                    |
| binary                | boolean        | false     | 문자열을 바이너리로 처리할지 여부                            |
| date                  | Date           | 현재 시간 | 마지막 수정일                                                |
| compression           | string         | null      | 이 파일에 사용할 압축 방식 지정 (예: `"DEFLATE"`, `"STORE"`) |
| compressionOptions    | object         | null      | 압축 수준 등 설정                                            |
| comment               | string         | null      | 파일에 달 주석                                               |
| optimizedBinaryString | boolean        | false     | 최적화된 바이너리 문자열 입력 시 true                        |
| createFolders         | boolean        | true      | 경로상의 폴더를 자동 생성할지 여부                           |
| unixPermissions       | number(string) | null      | 유닉스 권한 (예: `"755"` 또는 `0o755`)                       |
| dosPermissions        | number         | null      | 도스 권한                                                    |
| dir                   | boolean        | false     | 폴더인지 여부. true면 내용은 무시됨                          |

## data 입력 관련 주의사항

입력된 `data`는 내부에 그대로 저장되므로, 이후 해당 데이터를 수정하면 zip 내부 내용도 바뀔 수 있습니다.

### Promise (v3.0.0부터)

`Promise`를 직접 넘겨서 비동기 데이터 처리를 단순화할 수 있습니다.

```js
// 기존 방식
$.get("url/to.file.txt").then(function (content) {
  zip.file("file.txt", content);
});

// Promise 직접 사용
var promise = $.get("url/to.file.txt");
zip.file("file.txt", promise);
```

```js
// 콜백 기반 Promise 생성
var promise = new Promise(function (resolve, reject) {
  request("url/to.file.txt", function (err, res, body) {
    if (err) reject(err);
    else resolve(body);
  });
});
zip.file("file.txt", promise);
```

### Blob (v3.0.0부터)

Blob 또는 File 객체를 직접 사용할 수 있습니다. FileReader를 사용할 필요가 없습니다.

```js
// <input type="file"> change 이벤트에서 사용
var files = evt.target.files;
for (var i = 0; i < files.length; i++) {
  zip.file(files[i].name, files[i]);
}
```

### Node.js 스트림 (v3.0.0부터)

Node.js의 `Readable Stream`도 사용 가능하지만, 한 번 사용하면 재사용할 수 없습니다.  
(재사용 시 `generateAsync()` 또는 `ZipObject.async()`에서 오류 발생)

## 옵션별 예시

### base64

```js
zip.file("hello.txt", "aGVsbG8gd29ybGQK", { base64: true });
```

### binary

```js
// 일반 텍스트
zip.file("hello.txt", "unicode ♥", { binary: false });

// 바이너리 문자열
zip.file("hello.txt", "unicode \xE2\x99\xA5", { binary: true });
```

### date

```js
zip.file("Xmas.txt", "Ho ho ho !", {
  date: new Date("December 25, 2007 00:00:01"),
});
```

### compression 및 compressionOptions

```js
zip.file("a.png", contentA, {
  compression: "STORE",
});

zip.file("b.txt", contentB, {
  compression: "DEFLATE",
  compressionOptions: { level: 9 },
});

zip.file("c.txt", contentC);

zip.generateAsync({
  type: "blob",
  compression: "DEFLATE",
});
```

### comment

```js
zip.file("a.txt", "내용", {
  comment: "a.txt에 대한 설명",
});
```

### createFolders

```js
zip.file("a/b/c/d.txt", "내용", { createFolders: true });
// zip.files 에는 a/, a/b/, a/b/c/, a/b/c/d.txt 포함

zip.file("a/b/c/d.txt", "내용", { createFolders: false });
// zip.files 에는 a/b/c/d.txt 만 존재
```

### unixPermissions / dosPermissions

```js
zip.file("script.sh", "#!/bin/bash", {
  unixPermissions: "755",
});
```

### dir

```js
zip.file("folder/", null, { dir: true });
```

## 기타 예시

```js
zip.file("Hello.txt", "Hello World\n");

// base64
zip.file(
  "smile.gif",
  "R0lGODdhBQAFAIACAAAAAP/eACwAAAAABQAFAAACCIwPkWerClIBADs=",
  { base64: true }
);

// ArrayBuffer (XHR로부터)
zip.file("smile.gif", arrayBufferData);

// Node.js
zip.file("smile.gif", fs.readFileSync("smile.gif"));

zip.file("Xmas.txt", "Ho ho ho !", {
  date: new Date("December 25, 2007 00:00:01"),
});

zip.file("folder/file.txt", "file in folder");

zip
  .file("animals.txt", "dog,platypus\n")
  .file("people.txt", "james,sebastian\n");

// 결과 파일 목록:
// - Hello.txt
// - smile.gif
// - Xmas.txt
// - animals.txt
// - people.txt
```
