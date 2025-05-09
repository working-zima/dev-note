# filter(predicate)

지정한 조건 함수(predicate)에 따라 하위 파일/폴더를 필터링합니다.

**Returns** : 조건에 맞는 `ZipObject` 들의 배열을 반환합니다.

**Since**: v1.0.0

## Arguments

| name      | type     | description          |
| --------- | -------- | -------------------- |
| predicate | function | 필터링에 사용할 함수 |

predicate 함수는 다음 시그니처를 가집니다:  
`function (relativePath, file) { ... }`

| 이름         | 타입      | 설명                                                                                     |
| ------------ | --------- | ---------------------------------------------------------------------------------------- |
| relativePath | string    | 현재 폴더를 기준으로 한 상대 경로의 파일 이름                                            |
| file         | ZipObject | 검사 중인 파일 객체. [ZipObject]({{site.baseurl}}/documentation/api_zipobject.html) 참고 |

predicate 함수는 파일을 포함할 경우 `true`, 제외할 경우 `false` 를 반환해야 합니다.

## Examples

```js
var zip = new JSZip().folder("dir");
zip.file("readme.txt", "content");

zip.filter(function (relativePath, file) {
  // relativePath == "readme.txt"
  // file = { name: "dir/readme.txt", options: {...}, async: function }
  return true; // 또는 false
});
```
