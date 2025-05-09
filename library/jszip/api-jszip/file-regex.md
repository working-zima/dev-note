# file(regex)

현재 폴더 및 하위 폴더 내에서 [정규 표현식](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)을 사용해 파일을 검색합니다.\
정규식은 상대 경로 기준 파일 이름에 대해 테스트됩니다.

**Returns** : 조건에 맞는 파일들의 배열을 반환합니다 (없으면 빈 배열).\
각 파일은 [ZipObject]({{site.baseurl}}/documentation/api_zipobject.html) 인스턴스입니다.

**Since**: v1.0.0

## Arguments

| name  | type   | description               |
| ----- | ------ | ------------------------- |
| regex | RegExp | 검색에 사용할 정규 표현식 |

## Example

```js
var zip = new JSZip();
zip.file("file1.txt", "content");
zip.file("file2.txt", "content");

zip.file(/file/); // 크기 2의 배열 반환

// 상대 경로 예시
var folder = zip.folder("sub");
folder
  .file("file3.txt", "content") // 폴더 기준 상대 경로: file3.txt
  .file("file4.txt", "content"); // 폴더 기준 상대 경로: file4.txt

folder.file(/file/); // 크기 2의 배열 반환
folder.file(/^file/); // 크기 2의 배열 반환 (상대 경로가 "file"로 시작)

// 반환되는 배열의 항목 예시:
// {name: "file2.txt", dir: false, async : function () {...}, ...}
```
