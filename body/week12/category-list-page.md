# 3. 카테고리 관리

데이터를 변경하는 사례를 만들어 보자.\
카테고리는 이름과 표시 여부만 관리하면 돼서 매우 간단하다.

## 카테고리 목록

`CategoryListPage` 자체는 기존과 매우 유사하다.\
추가, 변경 등의 링크가 더 있을 뿐이다.

```tsx
import { Link } from 'react-router-dom';

import styled from 'styled-components';

import useFetchCategories from '../hooks/useFetchCategories';

const Container = styled.div`
  h2 {
    margin-bottom: 2rem;
    font-size: 2rem;
  }

  table {
    th, td {
      padding: .5rem;
    }
  }

  > a {
    display: inline-block;
    margin-block: 1rem;
  }
`;

export default function CategoryListPage() {
  const { categories, loading, error } = useFetchCategories();

  if (loading) {
    return (
      <p>Loading...</p>
    );
  }

  if (error) {
    return (
      <p>Error!</p>
    );
  }

  return (
    <Container>
      <h2>Categories</h2>
      <table>
        <thead>
          <tr>
            <th>이름</th>
            <th>표시</th>
            <th>행동</th>
          </tr>
        </thead>
        <tbody>
          {categories.map((category) => (
            <tr key={category.id}>
              <td>{category.name}</td>
              <td>{category.hidden ? '감춤' : '보임'}</td>
              <td>
                <Link to={`/categories/${category.id}/edit`}>수정</Link>
              </td>
            </tr>
          ))}
        </tbody>
      </table>
      <Link to="/categories/new">
        카테고리 추가
      </Link>
    </Container>
  );
}
```

`useFetchCategories` 훅이 `createCategory` 함수를 제공하게 하는데, 여기서 SWR의 캐시 초기화(사실상 refetch 요청)를 위해 mutate 함수를 호출하게 된다.
SWR 공식 문서의 Bound Mutate 항목을 참고하자.

[SWR - Bounce Mutate](https://swr.vercel.app/ko/docs/mutation#bound-mutate)

```tsx
import useFetch from './useFetch';

import { apiService } from '../services/ApiService';

import { Category } from '../types';

export default function useFetchCategories() {
  const {
    data, error, loading, mutate,
  } = useFetch<{
    categories: Category[];
  }>('/categories');

  return {
    categories: data?.categories ?? [],
    error,
    loading,
    async createCategory({ name }: {
      name: string;
    }) {
      await apiService.createCategory({ name });
      mutate();
    },
  };
}
```

### 카테고리 추가

카테고리를 추가하는 `CategoryNewPage`부터 보자.\
React Hook Form의 `useForm` 훅과 `Controller` 컴포넌트만 잘 쓰면 된다.\
`Controller` 쓰는 법이 약간 복잡하지만, 예전에 했던 것처럼 `TextBox`s 등으로 추출(extract)하면 깔끔하게 사용할 수 있다.

```tsx
import { useNavigate } from 'react-router-dom';

import { Controller, useForm } from 'react-hook-form';

import styled from 'styled-components';

import Button from '../components/ui/Button';

import useFetchCategories from '../hooks/useFetchCategories';

const Container = styled.div`
  h2 {
    margin-bottom: 2rem;
    font-size: 2rem;
  }

  [type=submit] {
    display: block;
    margin-block: 1rem;
  }
`;

export default function CategoryNewPage() {
  const navigate = useNavigate();

  const { createCategory } = useFetchCategories();

  type FormValues = {
    name: string;
  };

  const { handleSubmit, control } = useForm<FormValues>();

  const onSubmit = async (data: FormValues) => {
    await createCategory({
      name: data.name,
    });
    navigate('/categories');
  };

  return (
    <Container>
      <h2>New Category</h2>
      <form onSubmit={handleSubmit(onSubmit)}>
        <Controller
          control={control}
          name="name"
          defaultValue=""
          render={({ field: { onChange, value } }) => (
            <div>
              <label htmlFor="input-name">이름</label>
              <input
                id="input-name"
                value={value}
                onChange={onChange}
              />
            </div>
          )}
        />
        <Button type="submit">
          등록
        </Button>
      </form>
    </Container>
  );
}
```

### 카테고리 수정

기존 카테고리를 수정할 때는 기존 데이터를 활용해야 하기 때문에 살짝 일이 더 많다.\
여기서는 카테고리 표시 여부를 수정할 때만 조작 가능하게 했다.

```tsx
import { useNavigate, useParams } from 'react-router-dom';

import { Controller, useForm } from 'react-hook-form';

import styled from 'styled-components';

import Button from '../components/ui/Button';

import useFetchCategory from '../hooks/useFetchCategory';

const Container = styled.div`
  h2 {
    margin-bottom: 2rem;
    font-size: 2rem;
  }

  [type=submit] {
    display: block;
    margin-block: 1rem;
  }
`;

export default function CategoryEditPage() {
  const params = useParams();

  const categoryId = String(params.id);

  const navigate = useNavigate();

  const {
    category, loading, error, updateCategory,
  } = useFetchCategory({ categoryId });

  type FormValues = {
    name: string;
    hidden: boolean;
  };

  const { handleSubmit, control } = useForm<FormValues>();

  const onSubmit = async (data: FormValues) => {
    await updateCategory({
      name: data.name,
      hidden: data.hidden,
    });
    navigate('/categories');
  };

  if (loading) {
    return (
      <p>Loading...</p>
    );
  }

  if (error || !category) {
    return (
      <p>Error!</p>
    );
  }

  return (
    <Container>
      <h2>Edit Category</h2>
      <form onSubmit={handleSubmit(onSubmit)}>
        <Controller
          control={control}
          name="name"
          defaultValue={category.name}
          render={({ field: { onChange, value } }) => (
            <div>
              <label htmlFor="input-name">이름</label>
              <input
                id="input-name"
                value={value}
                onChange={onChange}
              />
            </div>
          )}
        />
        <Controller
          control={control}
          name="hidden"
          defaultValue={category.hidden}
          render={({ field: { onChange, value } }) => (
            <div>
              <label htmlFor="input-hidden">감춤</label>
              <input
                id="input-hidden"
                type="checkbox"
                checked={value}
                onChange={onChange}
              />
            </div>
          )}
        />
        <Button type="submit">
          수정
        </Button>
      </form>
    </Container>
  );
}
```

새로운 훅 `useFetchCategory`를 만들어 보자. 크게 다른 건 없다.

```tsx
import useFetch from './useFetch';

import { apiService } from '../services/ApiService';

import { Category } from '../types';

export default function useFetchCategory({ categoryId }: {
  categoryId: string;
}) {
  const url = `/categories/${categoryId}`;

  const {
    data, error, loading, mutate,
  } = useFetch<Category>(url);

  return {
    category: data,
    error,
    loading,
    async updateCategory({ name, hidden }: {
      name: string;
      hidden: boolean;
    }) {
      await apiService.updateCategory({ categoryId, name, hidden });
      mutate();
    },
  };
}
```
