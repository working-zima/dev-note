# 2. 목록보기

## 상품 목록

상품 목록을 얻어서 표시하는 화면을 만들자. 말 그대로 다음과 같이 두 가지로 나눌 수 있다:

1. 상품 목록 얻기
2. 상품 목록 보여주기

전자는 `useFetchProducts` 훅으로, 후자는 Products 컴포넌트로 구현하고, `ProductListPage`에선 이 둘을 조합한다.

```typescript
// src/pages/ProductListPage.tsx

import Products from '../components/product-list/Products';

import useFetchProducts from '../hooks/useFetchProducts';

export default function ProductListPage() {
  const { products } = useFetchProducts();

  return (
    <div>
      <h2>Products</h2>
      <Products products={products} />
    </div>
  );
}
```

일단 훅을 간단히 구현하고, `Products` 컴포넌트에 집중하자.

```typescript
// src/hooks/useFetchProducts.ts

const apiBaseUrl = 'https://shop-demo-api-01.fly.dev';

export default function useFetchProducts() {
  type Data = {
    products: ProductSummary[];
  };

  const { data } = useFetch<Data>(`${apiBaseUrl}/products`);

  return {
    products: data?.products ?? [],
  };
}
```

`Products` 컴포넌트를 만든다.

```typescript
// src/components/product-list/Products.tsx

const Container = styled.div`
  ul {
    display: flex;
    flex-wrap: wrap;
  }

  li {
    width: 20%;
    padding: 1rem;
  }

  a {
    display: block;
    text-decoration: none;
  }
`;

type ProductsProps = {
  products: ProductSummary[];
}

export default function Products({ products }: ProductsProps) {
  if (!products.length) {
    return null;
  }

  return (
    <Container>
      <ul>
        {products.map((product) => (
          <li key={product.id}>
            <Link to={`/products/${product.id}`}>
              <Product product={product} />
            </Link>
          </li>
        ))}
      </ul>
    </Container>
  );
}
```

`Product` 컴포넌트도 만든다.

```typescript
// src/components/product-list/Product.tsx

const Thumbnail = styled.img.attrs({
  alt: 'Thumbnail',
})`
  display: block;
  width: 100%;
  aspect-ratio: 1/1;
`;

type ProductProps = {
  product: ProductSummary;
}

export default function Product({ product }: ProductProps) {
  return (
    <div>
      <Thumbnail src={product.thumbnail.url} />
      <div>{product.name}</div>
      <div>
        {numberFormat(product.price)}
        원
      </div>
    </div>
  );
}
```

숫자를 읽기 좋게 보여주도록 `numberFormat` 유틸리티 함수를 준비한다.
[Intl](../../appendix/intl.md)을 사용하여 국가별 숫자를 표기

```typescript
// src/components/product-list/Products.tsx

export default function numberFormat(value: number) {
  return new Intl.NumberFormat().format(value);
}
```

지금은 큰 의미가 없지만, 상품 목록을 Store로 관리할 수 있다.

```typescript
// src/stores/ProductStore.ts

@singleton()
@Store()
export default class ProductsStore {
  products: ProductSummary[] = [];

  async fetchProducts() {
    this.setProducts([]);

    const { data } = await axios.get(`${apiBaseUrl}/products`);
    const { products } = data;

    this.setProducts(products);
  }

  @Action()
  setProducts(products: ProductSummary[]) {
    this.products = products;
  }
}
```

이제 `useFetchProducts`훅을 변경한다.

```typescript
// // src/hooks/useFetchProducts.ts

export default function useFetchProducts(): {
  products: ProductSummary[];
} {
  const store = container.resolve(ProductsStore);

  const [{ products }] = useStore(store);

  useEffectOnce(() => {
    store.fetchProducts();
  });

  return { products };
}
```

## 카테고리 목록

헤더에 카테고리 목록을 보여주자.

```typescript
// src/components/Header.tsx

const Container = styled.header`
  margin-bottom: 2rem;

  h1 {
    font-size: 4rem;
  }

  nav {
    padding-block: 2rem;

    ul {
      display: flex;
    }

    li {
      margin-right: 2rem;
    }

    .active {
      color: ${(props) => props.theme.colors.primary};
    }
  }
`;

export default function Header() {
  const { categories } = useFetchCategories();

  return (
    <Container>
      <h1>Shop</h1>
      <nav>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/products">Products</Link>
            {!!categories.length && (
              <ul>
                {categories.map((category) => (
                  <li key={category.id}>
                    <Link to={`/products?categoryId=${category.id}`}>
                      {category.name}
                    </Link>
                  </li>
                ))}
              </ul>
            )}
          </li>
          <li>
            <Link to="/cart">Cart</Link>
          </li>
        </ul>
      </nav>
    </Container>
  );
}
```

바로 Store를 준비한다.

```typescript
// src/stores/CategoriesStore.ts

@singleton()
@Store()
export default class CategoriesStore {
  categories: Category[] = [];

  async fetchCategories() {
    this.setCategories([]);

    const categories = await apiService.fetchCategories();

    this.setCategories(categories);
  }

  @Action()
  setCategories(categories: Category[]) {
    this.categories = categories;
  }
}
```

API 호출을 모아주는 ApiService를 만든다. API의 base URL을 지정하기 위해 환경변수를 활용한다.

```typescript
// src/services/ApiService.ts

const API_BASE_URL = process.env.API_BASE_URL
                     || 'https://shop-demo-api-01.fly.dev';

export default class ApiService {
  private instance = axios.create({
    baseURL: API_BASE_URL,
  });

  async fetchCategories(): Promise<Category[]> {
    const { data } = await this.instance.get('/categories');
    const { categories } = data;
    return categories;
  }

  async fetchProducts(): Promise<ProductSummary[]> {
    const { data } = await this.instance.get('/products');
    const { products } = data;
    return products;
  }
}

export const apiService = new ApiService();
```

마지막으로, useFetchCategories훅을 만든다.

```typescript
// src/hooks/useFetchCategories.ts

export default function useFetchCategories() {
  const store = container.resolve(CategoriesStore);

  const [{ categories }] = useStore(store);

  useEffect(() => {
    store.fetchCategories();
  }, [store]);

  return { categories };
}
```

## 카테고리별 상품 목록

ProductListPage컴포넌트에서 categoryId를 얻는다.

```typescript
// src/pages/ProductListPage.ts

export default function ProductListPage() {
  const [params] = useSearchParams();

  const categoryId = params.get('categoryId') ?? undefined;

  const { products } = useFetchProducts({ categoryId });

  return (
    <div>
      <h2>Products</h2>
      <Products products={products} />
    </div>
  );
}
```

카테고리 ID를 쓰도록 훅을 변경.

```typescript
// src/hooks/useFetchProducts.ts

export default function useFetchProducts({ categoryId }: {
  categoryId: string;
}): {
  products: ProductSummary[];
} {
  const store = container.resolve(ProductsStore);

  const [{ products }] = useStore(store);

  useEffect(() => {
    store.fetchProducts({ categoryId });
  }, [store, categoryId]);

  return { products };
}
```

Store도 변경.

```typescript
// src/stores/ProductsStore.tsx

@singleton()
@Store()
export default class ProductsStore {
  products: ProductSummary[] = [];

  async fetchProducts({ categoryId }: {categoryId?: string}) {
    this.setProducts([]);

    const products = await apiService.fetchProducts({ categoryId });

    this.setProducts(products);
  }

  @Action()
  setProducts(products: ProductSummary[]) {
    this.products = products;
  }
}
```

API Service도 변경.

```typescript
// src/services/ApiService.tsx

export default class ApiService {
  private instance = axios.create({
    baseURL: API_BASE_URL,
  });

  async fetchCategories(): Promise<Category[]> {
    const { data } = await this.instance.get('/categories');
    const { categories } = data;

    return categories;
  }

  async fetchProducts({ categoryId }: {
      categoryId?: string
    } = {}): Promise<ProductSummary[]> {
    const { data } = await this.instance.get(
      '/products'
      , { params: { categoryId } },
    );
    const { products } = data;

    return products;
  }
}

export const apiService = new ApiService();
```

사실 강의를 준비할 때는 처음부터 이렇게 만들기는 했다.
처음부터 고민해서 바로 만들어도 되고, 일단 만들고 이렇게 고쳐나가도 된다.
테스트 코드가 있으면 이런 변경 작업을 할 때 더 자신감을 얻을 수 있다.
