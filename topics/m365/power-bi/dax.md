# 데이터 분석 식 (Data Analysis Expression)

## 집계 함수

### DISTINCTCOUNT

```dax
DISTINCTCOUNT(<column>)
```

해당 열(column)에 들어 있는 값들 중에서 "중복을 제거한 고유한 값의 개수"를 세는 함수

## 필터 함수

### CALCULATE

```dax
CALCULATE(<expression>[, <filter1> [, <filter2> [, …]]])
```

특정 조건(필터)을 적용한 상태에서 계산하는 함수

- expression: 어떤 값을 계산할지
- filter: 어떤 조건을 적용할지

### FILTER

```dax
FILTER(<table>,<filter>)
```

조건에 맞는 행만 남겨서 새로운 표를 반환하는 함수

- table: 필터링할 테이블 또는 테이블을 생성하는 식
- filter: 테이블의 각 행에 대해 계산할 boolean 식
