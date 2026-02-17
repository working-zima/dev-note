# Power Query 편집기

Power BI에서 데이터를 분석하기 전에 정리하고 가공하는 공간입니다.\

## Power Query 편집기 실행 방법

### 1. 데이터를 가져오는 과정에서 ‘데이터 변환’ 버튼을 클릭합니다

![데이터 가져오기](./img/power-query/get-data.png)

![데이터 가져오며 데이터 변환](./img/power-query/transform-data.png)

### 2. Power BI Desktop의 홈 탭에서 데이터 변환을 선택합니다

![데이터 변환](./img/power-query/transform-data01.png)

## Power Query 편집기 분류

![power query 편집기](./img/power-query/query-overview-with-data-connection.png)

### 1. Power Query 편집기 리본 메뉴

#### 리본 메뉴 홈 탭

![리본 메뉴 홈 탭](./img/power-query/ribbon-menu-home00.png)

- 열/행 추가 또는 제거
  - 불필요한 열 삭제
  - 상단/하단 행 제거
  - 빈 행 제거

- 열 분할
  - 구분자(쉼표, 공백 등) 기준 분할
  - 글자 수 기준 분할
  - 주소, 코드값, 이름 분리 등에서 매우 자주 사용

- 첫 행을 머리글로 사용
  - 컬럼명이 데이터 첫 줄에 들어있는 경우 필수
  - CSV/엑셀 파일 불러올 때 거의 항상 사용

Power Query에서 작업을 마쳤다면 반드시 '닫기 및 적용'을 눌러야 변경 내용이 실제 데이터 모델에 반영됩니다.

#### 리본 메뉴 변환 탭

#### 리본 메뉴 열 추가 탭

#### 리본 메뉴 보기 탭

![리본 메뉴 보기 탭](./img/power-query/query-overview-view-ribbon.png)

#### 리본 메뉴 도구 탭

### 2. Power Query 편집기 쿼리 창

![왼쪽 쿼리 창](./img/power-query/query-overview-the-left-pane.png)

### 3. Power Query 편집기 데이터 창

![가운데 데이터 창](./img/power-query/query-overview-the-center-pane.png)

#### 데이터 형식

![데이터 형식](./img/power-query/type.png)

- 숫자 관련
  - 10진수 → 일반 소수 숫자 (예: 12.34)
  - 고정 10진수 → 소수점 자릿수 고정\
    부동소수점 방식으로 계산 하는 컴퓨터(이진법 계산)의 오차를 줄이기 위한 형식으로 금액은 소수점 오차가 발생하면 안 되기 때문에, 고정 10진수를 사용하는 것이 안전
  - 정수 → 소수점 없는 숫자
  - 백분율 → % 형식 숫자

- 날짜/시간 관련
  - 날짜/시간 → 날짜 + 시간
  - 날짜 → 날짜만
  - 시간 → 시간만
  - 날짜/시간/표준 시간대 → 시간대 포함
  - 기간 → 날짜 차이(예: 5일)

- 기타
  - 텍스트 → 문자 데이터
  - True/False → 참/거짓
  - 이진 → 파일, 이미지 등 바이너리 데이터

### 4. Power Query 편집기 쿼리 설정 창

Power Query는 원본 데이터를 직접 고치는 프로그램이 아닙니다.\
대신, 원본 데이터 위에 적용한 단계를 덧씌우는 방식으로 작동합니다.

![Power Query 편집기 쿼리 설정 창](./img/power-query/query-overview-query-settings-pane.png)

이 구조 덕분에 다음이 가능합니다.

1. 중간에 적용한 단계를 다시 수정할 수 있습니다.
2. 특정 단계를 삭제하면 그 단계 이후 작업이 자동으로 다시 계산됩니다.
3. 다른 데이터를 불러와도 동일한 단계를 적용할 수 있습니다.
