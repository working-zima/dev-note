# forEach(callback)

현재 폴더 수준에 있는 항목들에 대해 지정한 콜백 함수를 하나씩 호출합니다.

**Returns** : 반환값 없음

**Since**: v3.0.0

## Arguments

| name     | type     | description      |
| -------- | -------- | ---------------- |
| callback | function | 호출할 콜백 함수 |

콜백 함수는 다음과 같은 시그니처를 가집니다:  
`function (relativePath, file) { ... }`

| 이름         | 타입      | 설명                                                                                              |
| ------------ | --------- | ------------------------------------------------------------------------------------------------- |
| relativePath | string    | 현재 폴더를 기준으로 한 상대 경로의 파일 이름                                                     |
| file         | ZipObject | 현재 파일 객체. 자세한 내용은 [ZipObject]({{site.baseurl}}/documentation/api_zipobject.html) 참조 |

## Examples

```js
var zip = new JSZip();
zip.file("package.json", "...");
zip.file("lib/index.js", "...");
zip.file("test/index.html", "...");
zip.file("test/asserts/file.js", "...");
zip.file("test/asserts/generate.js", "...");

zip.folder("test").forEach(function (relativePath, file) {
  console.log("iterating over", relativePath);
});

// 출력 결과:
// iterating over index.html
// iterating over asserts/
// iterating over asserts/file.js
// iterating over asserts/generate.js
```
