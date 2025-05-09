# remove(name)

지정한 파일 또는 폴더를 삭제합니다.  
폴더인 경우 내부 항목도 모두 재귀적으로 삭제됩니다.

**Returns** : 현재 JSZip 객체를 반환합니다 (체이닝 가능).

**Since**: v1.0.0

## Arguments

| name | type   | description                  |
| ---- | ------ | ---------------------------- |
| name | string | 삭제할 파일 또는 폴더의 이름 |

## Examples

```js
var zip = new JSZip();
zip.file("Hello.txt", "Hello World\n");
zip.file("temp.txt", "nothing").remove("temp.txt");
// 결과: Hello.txt만 남음

zip.folder("css").file("style.css", "body {background: #FF0000}");
zip.remove("css");
// 결과: zip은 비어 있음
```
