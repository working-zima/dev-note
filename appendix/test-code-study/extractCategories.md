# src/utils/extractCategories.ts

음식 카테고리 배열을 반환하는 함수

```tsx
import Restaurant from '../types/Restaurant';

export default function extractCategories(restaurants: Restaurant[]): string[] {
  return restaurants.reduce((acc: string[], restaurant: Restaurant) => {
    const { category } = restaurant;

    return acc.includes(category) ? acc : [...acc, category];
  }, []);
}
```

## extractCategories.test.ts

```tsx
import fixtures from '../../fixtures';
import extractCategories from './extractCategories';


describe('extractCategories', () => {
  it('returns categories from restaurants', () => {
    // given
    const { restaurants } = fixtures;

    // when
    const categories = extractCategories(restaurants);

    // then
    expect(categories).toEqual(['중식', '한식', '일식']);
  });
});
```

### Given

- 식당 데이터인 `restaurants`을 가져옴

### 테스트 케이스 01

extractCategories returns categories from restaurants

#### when 01

- `extractCategories()`를 호출

#### then 01

- `extractCategories()`의 반환 값이 `['중식', '한식', '일식']`와 같은지 테스트
