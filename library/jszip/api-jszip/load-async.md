# loadAsync(data [, options])

기존 zip 파일을 읽어 현재 JSZip 인스턴스의 현재 폴더 수준에 병합합니다.
이미 해당 이름을 가진 항목이 존재할 경우, 불러온 파일이 기존 파일을 덮어씁니다.

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

| 이름                  | 타입     | 기본값            | 설명                                        |
| --------------------- | -------- | ----------------- | ------------------------------------------- |
| base64                | boolean  | false             | base64로 인코딩된 경우 true                 |
| checkCRC32            | boolean  | false             | CRC32 무결성 체크 활성화 여부               |
| optimizedBinaryString | boolean  | false             | 입력이 마스크된 바이너리 문자열일 경우 true |
| createFolders         | boolean  | false             | 폴더 경로를 실제 폴더로 생성할지 여부       |
| decodeFileName        | function | UTF-8 디코딩 함수 | 파일명/주석 디코딩용 사용자 정의 함수       |

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

```js
var zip = new JSZip();
zip.loadAsync("UEsDBAoDAAAAAJxs8T...AAAAAA==", { base64: true });
```

### checkCRC32 옵션

```js
zip.loadAsync(bin).then(success, error); // CRC32 체크 안함 → 성공
zip.loadAsync(bin, { checkCRC32: true }).then(success, function (e) {
  // CRC32 불일치 → 오류 발생
  console.error("오류:", e);
});
```

### createFolders 옵션

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

zip 파일이 UTF-8을 사용하지 않으면 인코딩 오류가 발생할 수 있으며,  
`decodeFileName` 옵션을 사용하여 이를 해결할 수 있습니다.

```js
var iconv = require("iconv-lite");
zip.loadAsync(bin, {
  decodeFileName: function (bytes) {
    return iconv.decode(bytes, "cp866"); // 러시아어 zip 예시
  },
});
```

## 기타 예시

```js
var zip = new JSZip();
zip.loadAsync(zipDataFromXHR);
```

```js
require("fs").readFile("hello.zip", function (err, data) {
  if (err) throw err;
  var zip = new JSZip();
  zip.loadAsync(data);
});
```

### 하위 폴더로 로드

```js
zip
  .folder("subfolder")
  .loadAsync(bin)
  .then(function (zip) {
    console.log(zip.files);
    // subfolder/file1.txt
    // subfolder/folder1/file2.txt
  });
```

### 여러 zip을 순차적으로 병합

```js
zip
  .loadAsync(bin1)
  .then(function (zip) {
    return zip.loadAsync(bin2);
  })
  .then(function (zip) {
    console.log(zip.files);
    // file2.txt → bin2의 항목으로 덮어씌워짐
  });
```

### 경로 정리된 zip 읽기

```js
require("fs").readFile("unsafe.zip", function (err, data) {
  if (err) throw err;
  var zip = new JSZip();
  zip.loadAsync(data).then(function (zip) {
    console.log(zip.files);
    // 상대 경로 정리됨:
    // src/file.txt
    // example.txt
    console.log(zip.files["example.txt"].unsafeOriginalName);
    // "../../example.txt"
  });
});
```
