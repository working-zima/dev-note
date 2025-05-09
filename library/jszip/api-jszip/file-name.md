# file(name)

지정한 이름의 파일을 가져옵니다.\
파일 이름에 폴더를 포함시킬 수 있으며, 폴더 구분자는 슬래시(`/`)입니다.

**Returns** : 파일이 존재하면 [ZipObject]({{site.baseurl}}/documentation/api_zipobject.html) 인스턴스를 반환하고, 존재하지 않으면 `null`을 반환합니다.

**Since**: v1.0.0

## Arguments

| name | type   | description            |
| ---- | ------ | ---------------------- |
| name | string | 가져오려는 파일의 이름 |

**Throws** : 예외 없음

<!-- __Complexity__ : 단순 조회로 **O(1)** 시간 복잡도 -->

## Example

```js
var zip = new JSZip();
zip.file("file.txt", "content");

zip.file("file.txt").name; // "file.txt"
zip.file("file.txt").async("string"); // "content"를 반환하는 Promise
zip.file("file.txt").dir; // false
```

```js
// UTF-8 문자열 예시
var zip = new JSZip();
zip.file("amount.txt", "€15");

zip.file("amount.txt").async("string"); // "€15"
zip.file("amount.txt").async("arraybuffer"); // €15를 utf-8로 인코딩한 ArrayBuffer
zip.file("amount.txt").async("uint8array"); // €15를 utf-8로 인코딩한 Uint8Array
```

```js
// 폴더 포함 경로 예시
zip.folder("sub").file("file.txt", "content");

zip.file("sub/file.txt"); // 파일 접근
// 또는
zip.folder("sub").file("file.txt"); // 동일한 파일
```
