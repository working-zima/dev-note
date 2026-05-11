# M 언어

데이터를 필터링하고 결합할 때 사용되는 언어입니다.\
데이터 쿼리 탭에서 적용하는 필터는 M 언어의 프리셋이라고 이해하면 쉽습니다.

## 식, 값 및 let 식

### 기본 구조

`let`: 중간 결과(단계)를 정의하는 곳
`in`: 최종적으로 반환할 값을 지정하는 곳

```Power Query
let
  단계1 = ...,
  단계2 = ...,
  단계3 = ...
in
  단계3
```

#### 식, 값 및 let 식 예제

```Power Query
let
    Source = Excel.Workbook(File.Contents("sales.xlsx")),
    SalesSheet = Source{[Item="Sales",Kind="Sheet"]}[Data],
    PromotedHeaders = Table.PromoteHeaders(SalesSheet),
    ChangedType = Table.TransformColumnTypes(
        PromotedHeaders,
        {{"Date", type date}, {"Amount", type number}}
    ) // 마지막은 쉼표 없음
in
    ChangedType
```

1. `Source` → 엑셀 파일 불러오기
2. `SalesSheet` → 특정 시트 선택
3. `PromotedHeaders` → 첫 행을 열 이름으로
4. `ChangedType` → 데이터 형식 변경
5. `in ChangedType` → 이 테이블이 최종 결과

### 중요한 규칙 5가지

1. let 안의 단계는 순서대로 실행
   - 아래 단계는 위 단계 결과를 사용 가능
2. 단계 이름은 변수
   ```Power Query
    단계명 = 값
   ```
3. `let` 블록은 반드시 in으로 끝나야 함
   - 없으면 오류 발생
4. `in` 에는 딱 하나의 값만
   - 보통 마지막 단계 이름
   - `let` 안의 모든 단계가 결과로 나가는 건 아니라 `in` 에 지정된 것만 출력
5. M 언어는 대소문자 구분
   - ChangedType ≠ changedtype

#### `#`

`#""`는 Power Query M에서 특수한 단계 이름을 쓰기 위한 문법

```Power Query
Removed Top Rows   // 공백 있음 → 오류

#"Removed Top Rows" // 공백 사용 시 #"" 사용
```

## each

단일 인자를 받는 익명 함수 축약 표기입니다.

```m
(row) => Date.DayOfWeekName(row[Order Date])

// 위는 아래와 같음

each Date.DayOfWeekName([Order Date])
```

## 함수

### 숫자 함수

#### Number.Sign

```m
Number.Sign(null도 허용하는 숫자 타입 값)
```

`number`가 양수이면 `1`을 반환하고, 음수이면 `-1`, `0`이면 `0`을 반환합니다.\
`number`이 `null`이면 `Number.Sign` `null`을 반환합니다.

### 텍스트 함수

#### Text.Upper

```m
Text.Upper(null도 허용하는 텍스트 타입 값, 옵션: null도 허용하는 국가별 언어코드)
```

입력 텍스트를 대문자로 바꿔서 반환합니다.

### 테이블 함수

#### Table.FromList

```m
Table.FromList(
  list 타입 값,
  optional splitter as nullable function,
  optional columns as any,
  optional default as any,
  optional extraValues as nullable number
) as table
```

목록(list)을 입력받아서 테이블(table)로 만들어 주는 함수입니다.

##### splitter as nullable function 옵션

리스트 안의 값이 “여러 값”일 때 열로 나누는 함수

```m
Splitter.SplitByNothing() // 나누지 않음 (명시적)
Splitter.SplitTextByDelimiter(",") // 구분자로 나눔
Splitter.SplitTextByWhitespace() //공백 기준
```

```m
Table.FromList(
  {"A,B", "C,D"},
  Splitter.SplitTextByDelimiter(",")
)

// 결과
Column1 | Column2
A       | B
C       | D
```

##### columns as any 옵션

```m
Table.FromList(
  {"A,B"},
  Splitter.SplitTextByDelimiter(",")
)

// 결과
Column1 | Column2
A       | B
```

```m
Table.FromList(
  {"A,B"},
  Splitter.SplitTextByDelimiter(","),
  {"First", "Second"}
)

// 결과
First | Second
A     | B
```

##### default as any 옵션

값이 부족할 때를 대비하여 default 값 사용

```m
// default 가 없을 때
Table.FromList(
  {"A,B", "C"},
  Splitter.SplitTextByDelimiter(","),
  {"Col1", "Col2"}
)

// 결과
Expression.Error: There were not enough elements...

// default 가 있을 때
Table.FromList(
  {"A,B", "C"},
  Splitter.SplitTextByDelimiter(","),
  {"Col1", "Col2"},
  "N/A"
)

// 결과
Col1 | Col2
A    | B
C    | N/A
```

default는 "N/A", null, 0, {} 등 어떤 타입도 가능

##### extraValues as nullable number 옵션

|          값          |         의미          |
| :------------------: | :-------------------: |
| `ExtraValues.Error`  |  오류 발생 (기본값)   |
| `ExtraValues.Ignore` |     초과 값 버림      |
|  `ExtraValues.List`  | 초과 값 리스트로 보관 |

```m
// 문제 상황
Table.FromList(
  {"A,B,C"},
  Splitter.SplitTextByDelimiter(","),
  {"Col1", "Col2"},
  null
)

// ExtraValues.Error
Table.FromList(
  {"A,B,C"},
  Splitter.SplitTextByDelimiter(","),
  {"Col1", "Col2"},
  null,
  ExtraValues.Error
)


// 결과
Expression.Error: Too many values

// ExtraValues.Ignore
Table.FromList(
  {"A,B,C"},
  Splitter.SplitTextByDelimiter(","),
  {"Col1", "Col2"},
  null,
  ExtraValues.Ignore
)

// 결과
Col1 | Col2
A    | B

// ExtraValues.List
Table.FromList(
  {"A,B,C"},
  Splitter.SplitTextByDelimiter(","),
  {"Col1", "Col2"},
  null,
  ExtraValues.List
)

// 결과
Col1 | Col2
A    | {B, C}
```

#### Table.TransformColumns

```m
Table.TransformColumns(
  변환할 테이블,
  transformOperations as list,
  optional defaultTransformation as nullable function,
  optional missingField as nullable number
) as table
```

##### transformOperations as list

어떤 열에, 어떤 변환을 할지 정의

```m
{
  { "컬럼명", 변환함수, 새타입? },
  { "다른컬럼", 변환함수 }
}
```

##### defaultTransformation 옵션

transformOperations에 명시되지 않은 열에 공통으로 적용할 변환

##### missingField 옵션

지정한 열이 테이블에 없을 경우 동작

|          값          |      의미      |
| :------------------: | :------------: |
|  MissingField.Error  | 오류 (기본값)  |
| MissingField.Ignore  | 없는 열은 무시 |
| MissingField.UseNull |  null로 처리   |
