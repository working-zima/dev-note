# src/utils/filterRestaurants.ts

카테고리 버튼을 클릭하거나 텍스트 검색을 사용하면 해당되는 결과물을 걸러주는 함수\
결과물 데이터 객체를 배열에 담아 반환`[{...}, {...}, {...}]`

```tsx
import Restaurant from '../types/Restaurant';

function normalize(text: string) {
  return text.trim().toLocaleLowerCase();
}

type FilterConditions = {
  filterText: string;
  filterCategory: string;
}

export default function filterRestaurants(
  restaurants: Restaurant[],
  { filterText, filterCategory }: FilterConditions,
): Restaurant[] {
  const match = (restaurant: Restaurant) => (restaurant.category === filterCategory);

  const filteredRestaurants = filterCategory === '전체'
    ? restaurants
    : restaurants.filter(match);

  const query = normalize(filterText);

  if (!query) {
    return filteredRestaurants;
  }

  const contains = (restaurant: Restaurant) => (
    normalize(restaurant.name).includes(query)
  );

  return filteredRestaurants.filter(contains);
}

```

## filterRestaurants.test.ts

`fixtures`의 `restaurants`에는 '한식', '중식', '일식' 시당이 모두 각각 하나씩 있음\
`filterText`는 검색어, `filterCategory`는 선택 카테고리\
`filterText`와 `filterCategory`에 따라 여러 경우가 나올 수 있기 때문에 DCI 패턴을 사용

```tsx
import fixtures from '../../fixtures';

import filterRestaurants from './filterRestaurants';

const context = describe;

describe('filterRestaurants', () => {
  // given
  const { restaurants } = fixtures;

  context('with “전체” category', () => {
    it('returns all restaurants', () => {
      // when
      const filteredRestaurants = filterRestaurants(
        restaurants,
        { filterText: '', filterCategory: '전체' },
      );

      // then
      expect(filteredRestaurants.length).toBe(restaurants.length);
    });
  });

  context('with “한식” category', () => {
    it('returns selected category restaurants', () => {
      // when
      const filteredRestaurants = filterRestaurants(
        restaurants,
        { filterText: '', filterCategory: '한식' },
      );

      // then
      expect(filteredRestaurants.length).toBe(1);
    });
  });

  context('with “중식” category', () => {
    it('returns selected category restaurants', () => {
      // when
      const filteredRestaurants = filterRestaurants(
        restaurants,
        { filterText: '', filterCategory: '중식' },
      );

      // then
      expect(filteredRestaurants.length).toBe(1);
    });
  });

  context('with “일식” category', () => {
    it('returns selected category restaurants', () => {
      // when
      const filteredRestaurants = filterRestaurants(
        restaurants,
        { filterText: '', filterCategory: '일식' },
      );

      // then
      expect(filteredRestaurants.length).toBe(1);
    });
  });

  context('with filter text', () => {
    it('returns filtered restaurant', () => {
      // given
      const filterText = '메가반점';

      // when
      const filteredRestaurants = filterRestaurants(
        restaurants,
        { filterText, filterCategory: '전체' },
      );

      const filteredRestaurant = filteredRestaurants[0];

      // then
      expect(filteredRestaurants.length).toBe(1);

      expect(filteredRestaurant.name).toBe(filterText);
    });
  });
});

```

### Given

- 식당 데이터인 `restaurants`을 가져옴

### 테스트 케이스 01

filterRestaurants with “전체” category returns all restaurants

#### when 01

- `filterText`는 없고 `filterCategory`가 '전체'인 `filterRestaurants`을 호출

#### then 01

- `restaurants`과 `filterRestaurants`의 반환 데이터의 길이가 같은지 확인

### 테스트 케이스 02

filterRestaurants with “한식” category returns selected category restaurants

#### when 02

- `filterText`는 없고 `filterCategory`가 '한식'인 `filterRestaurants`을 호출

#### then 02

- `filterRestaurants`의 반환 데이터의 길이가 하나 인지 확인

### 테스트 케이스 03

filterRestaurants with “중식” category returns selected category restaurants

#### when 03

- `filterText`는 없고 `filterCategory`가 '중식'인 `filterRestaurants`을 호출

#### then 03

- `filterRestaurants`의 반환 데이터의 길이가 하나 인지 확인

### 테스트 케이스 04

filterRestaurants with “일식” category returns selected category restaurants

#### when 04

- `filterText`는 없고 `filterCategory`가 '일식'인 `filterRestaurants`을 호출

#### then 04

- `filterRestaurants`의 반환 데이터의 길이가 하나 인지 확인

### 테스트 케이스 05

filterRestaurants with filter text returns filtered restaurant

#### Given 05

- 변수 filterText에 '메가반점' 텍스트를 초기화

#### when 05

- `filteredRestaurants`는 `filterText`가 '메가반점', `filterCategory`가 '전체'인 `filterRestaurants`을 호출하는 함수

- `filteredRestaurant`에 `filteredRestaurants`에서 반환된 배열의 첫 번째 객체(검색어 '메가반점'이 포함된)를 초기화

#### then 05

- `filteredRestaurants`의 반환 값이 1개 인지 확인

- `filteredRestaurant`의 `name`이 '메가반점'인지 확인
