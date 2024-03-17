# 테스트

## 7주차 학습 목표

React에서 라우팅 처리에 대해 배워봅시다.

## 목차

- [Routing](./routing.md)
- [Routes](./routes.md)
- [Router](./router.md)
- [Navigation](./navigation.md)

## 주간 회고

### 배운 점

크게 새롭게 배운 내용은 없었습니다.\
react-route가 새롭게 바뀐 부분에 대해 코드를 한번 작성해봤습니다.

### 고민해야할 점

학습 내용과 별도로 useHook-ts를 사용해서 해결하던 부분들을 자바스크립트 코드들로 대체하려고 시도가 있었던 주였습니다.\
useFetch를 대체하던 과정에서 Generic을 사용해서 해결했는데, 시간도 오래걸리고 맞게 사용한지 잘 모르겠습니다.\
여기저기서 터져가는 타입에러 때문에 막아가며 되는데로 해결했기 때문에 어디를 더 손 봐야 할지 모르겠습니다.

```tsx
import { useEffect, useState } from 'react';

export interface FetchReturn<T> {
  data: T;
  isError: boolean;
  reFetch: () => void;
}

function useFetch<T>(url: string, option?: RequestInit): FetchReturn<T> {
  const [data, setData] = useState<T>({} as T);
  const [isError, setIsError] = useState<boolean>(false);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch(url, option);
        const responseData = await response.json();

        if (!response.ok) {
          throw new Error('Could not fetch data.');
        }

        setData(responseData);
      } catch (err) {
        setIsError(true);
      }
    };

    fetchData();
  }, [url, option]);

  const reFetch = async () => {
    try {
      const response = await fetch(url);
      const responseData = await response.json();

      if (!response.ok) {
        throw new Error('Could not fetch data.');
      }

      setData(responseData);
    } catch (err) {
      setIsError(true);
    }
  };

  return {
    data,
    isError,
    reFetch,
  };
}

export default useFetch;
```
