# 2. 로그아웃

## Access Token 확인

사이트에 접속하거나 새로 고침 등을 했을 땐 Access Token을 확인하고, 사용자 정보를 얻는 작업이 필요하다.\
만약 회원 정보를 얻는 API가 실패하면 Access Token에 문제가 있는 상황이니 로그아웃시킨다.

`ApiService`에 `fetchCurrentUser` 메서드를 추가한다.

```tsx
// src/services/ApiService.ts

async fetchCurrentUser(): Promise<{
  id: string;
  name: string;
}> {
  const { data } = await this.instance.get('/users/me');
  const { id, name } = data;
  return { id, name };
}
```

`useCheckAccessToken` 훅을 만든다.

```tsx
// src/hooks/useCheckAccessToken.ts

export default function useCheckAccessToken(): void {
  const { accessToken, setAccessToken } = useAccessToken();

  useEffect(() => {
    const fetchCurrentUser = async () => {
      try {
        await apiService.fetchCurrentUser();
      } catch (e) {
        setAccessToken('');
      }
    };

    fetchCurrentUser();
  }, [accessToken, setAccessToken]);
}
```

가장 밖에 있는 Layout에서 `useCheckAccessToken`을 호출한다.

```tsx
// src/components/Layout.tsx

export default function Layout() {
  useCheckAccessToken();

  return (
    <Container>
      <Header />
      <Outlet />
    </Container>
  );
}
```

## 로그아웃

`ApiService`에 로그아웃 API를 호출하는 메서드를 추가한다.

```tsx
// src/services/ApiService.ts

async logout(): Promise<void> {
  await this.instance.delete('/session');
}
```

로그아웃 버튼이 있는 `Header` 컴포넌트를 수정한다.

```tsx
// src/components/Header.tsx

export default function Header() {
  const navigate = useNavigate();

  const { accessToken, setAccessToken } = useAccessToken();

  const { categories } = useFetchCategories();

  const handleClickLogout = async () => {
    await apiService.logout();
    setAccessToken('');
    navigate('/');
  };

  // …(중략)…
}
```

react-router-dom를 mocking해서 `Header`를 테스트할 수 있다.

```tsx
import { screen, fireEvent, waitFor } from '@testing-library/react';

import { render } from '../test-helpers';

import Header from './Header';

const navigate = jest.fn();

let accessToken = '';
const setAccessToken = jest.fn();

jest.mock('react-router-dom', () => ({
  ...jest.requireActual('react-router-dom'),
  useNavigate: () => navigate,
}));

jest.mock('../hooks/useAccessToken', () => () => ({
  accessToken,
  setAccessToken,
}));

jest.mock('../hooks/useFetchCategories', () => () => ({
  categories: [],
}));

const context = describe;

describe('Header', () => {
  beforeEach(() => {
    jest.clearAllMocks();
  });

  it('renders title', () => {
    render(<Header />);

    screen.getByText(/Shop/);
  });

  it('renders basic link', () => {
    render(<Header />);

    screen.getByRole('link', { name: 'Home' });
  });

  context("when the current user isn't logged in", () => {
    beforeEach(() => {
      accessToken = '';
    });

    it('renders “Login” link', () => {
      render(<Header />);

      screen.getByRole('link', { name: 'Login' });
    });
  });

  context('when the current user is logged in', () => {
    beforeEach(() => {
      accessToken = 'ACCESS-TOKEN';
    });

    it('renders “Cart” link', () => {
      render(<Header />);

      screen.getByRole('link', { name: 'Cart' });
    });

    it('renders “Logout” button', async () => {
      render(<Header />);

      const button = screen.getByRole('button', { name: 'Logout' });

      fireEvent.click(button);

      await waitFor(() => {
        expect(setAccessToken).toBeCalledWith('');
        expect(navigate).toBeCalledWith('/');
      });
    });
  });
});
```
