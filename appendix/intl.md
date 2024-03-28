# Intl API

각 국가별 사용자가 이해할 수 있는 형식으로 날짜와 같은 데이터 포맷팅 문제를 해결해줍니다.\
Intl 객체를 통해 사용이 가능하며, 이 Intl 객체는 여러 개의 생성자로 구성되어 있습니다.

어떤 종류의 데이터를 포맷팅하느냐에 따라서 그에 해당하는 생성자를 사용해야하며, 모든 생성자는 공통적으로 2개의 인자를 받습니다.
첫번째 인자는 locale이라는 언어와 지역 정보를 표준화 시킨 코드입니다.
두번째 인자는 추가적인 포맷팅 옵션을 명시할 수 있는 객체입니다.

```typescript
// Intl.생성자(locale).format(포맷팅 옵션)

new Intl.DateTimeFormat("ko", { dateStyle: "long" }).format(new Date())
// '2024년 3월 27일'(한국의 현재 날짜)
```

## Intl.NumberFormat

통화(currency), 백분율, 무게, 길이, 속도, 온도와 같이 단위가 있는 숫자 데이터를 다룰 때 사용합니다.\
`NumberFormat()`의 인자를 비워두면 기본 로케일에 따라 숫자가 형식화됩니다.\
브라우저의 설정에 따라 자동으로 로케일이 설정되며, 이에 따라 해당 로케일에 맞는 숫자 형식으로 출력됩니다.

### percent

```typescript
> new Intl.NumberFormat('ko', { style: 'percent' }).format(0.7)
'70%'
> new Intl.NumberFormat('ko', { style: 'percent' }).format(1 / 4)
'25%'
```

### currency

```typescript
> new Intl.NumberFormat('ko', { style: 'currency', currency: 'KRW' }).format(50000)
'₩50,000'
> new Intl.NumberFormat('ko', { style: 'currency', currency: 'USD' }).format(40.56)
'US$40.56'
```

### unit

```typescript
> new Intl.NumberFormat('ko', { style: 'unit', unit: 'kilogram' }).format(50)
'50kg'
> new Intl.NumberFormat('ko', { style: 'unit', unit: 'pound' }).format(110)
'110lb'
```

## 참고 자료

- [자바스크립트의 Intl API로 국제화하기](https://www.daleseo.com/js-intl-api/)
