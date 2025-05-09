# folder(regex)

현재 디렉토리와 하위 디렉토리에서 [정규 표현식](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)을 사용하여 서브디렉토리를 검색합니다.\
정규식은 상대 경로를 기준으로 테스트됩니다.

**Returns** : 조건에 맞는 디렉토리들의 배열을 반환합니다 (없으면 빈 배열).\
각 항목은 [ZipObject]({{site.baseurl}}/documentation/api_zipobject.html) 인스턴스입니다.

**Since**: v1.0.0

## Arguments

| name  | type   | description               |
| ----- | ------ | ------------------------- |
| regex | RegExp | 검색에 사용할 정규 표현식 |

## Examples

```js
var zip = new JSZip();
zip.folder("home/Pierre/videos");
zip.folder("home/Pierre/photos");
zip.folder("home/Jean/videos");
zip.folder("home/Jean/photos");

zip.folder(/videos/); // 크기 2의 배열 반환

zip.folder("home/Jean").folder(/^vid/); // 크기 1의 배열 반환
```
