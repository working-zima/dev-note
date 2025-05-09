# folder(name)

지정한 이름의 디렉토리를 생성합니다.\
이미 존재하는 경우에는 새로 만들지 않고 해당 경로를 기준으로 한 JSZip 인스턴스를 반환합니다.\
반환된 객체는 해당 폴더를 루트로 간주하며, 이후 파일 추가 시 이 경로를 기준으로 작동합니다.

`file()`의 [dir 옵션]({{site.baseurl}}/documentation/api_jszip/file_data.html)도 참고하세요.

**Returns** : 해당 폴더를 루트로 갖는 새로운 JSZip 객체를 반환합니다 (체이닝 가능).

**Since**: v1.0.0

## Arguments

| name | type   | description                    |
| ---- | ------ | ------------------------------ |
| name | string | 디렉토리 이름 (경로 포함 가능) |

## Examples

```js
zip.folder("images");

zip.folder("css").file("style.css", "body {background: #FF0000}");

// 절대 경로 형태로 지정할 수도 있음
zip.file("css/font.css", "body {font-family: sans-serif}");

// 결과: images/, css/, css/style.css, css/font.css
```
