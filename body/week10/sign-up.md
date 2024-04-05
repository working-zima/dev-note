# 3. 회원 가입

## 회원 가입

`ApiService`에 signup 메서드를 추가한다.

```tsx
// src/services/ApiService.ts

async signup({ email, name, password }: {
  email: string;
  name: string;
  password: string;
}): Promise<string> {
  const { data } = await this.instance.post('/users', {
    email, name, password,
  });
  const { accessToken } = data;
  return accessToken;
}
```

로그인과 비슷하게 `SignupFormStore`를 만든다.

```tsx
// src/stores/SignupFormStore.ts

@singleton()
@Store()
class SignupFormStore {
  email = '';

  name = '';

  password = '';

  passwordConfirmation = '';

  error = false;

  accessToken = '';

  get valid() {
    return this.email.includes('@')
      && !!this.name
      && this.password.length >= 4
      && this.password === this.passwordConfirmation;
  }

  @Action()
  changeEmail(email: string) {
    this.email = email;
  }

  @Action()
  changeName(name: string) {
    this.name = name;
  }

  @Action()
  changePassword(password: string) {
    this.password = password;
  }

  @Action()
  changePasswordConfirmation(password: string) {
    this.passwordConfirmation = password;
  }

  @Action()
  setAccessToken(accessToken: string) {
    this.accessToken = accessToken;
  }

  @Action()
  setError() {
    this.error = true;
  }

  @Action()
  reset() {
    this.email = '';
    this.name = '';
    this.password = '';
    this.passwordConfirmation = '';
    this.error = false;
    this.accessToken = '';
  }

  async signup() {
    try {
      const accessToken = await apiService.signup({
        email: this.email,
        name: this.name,
        password: this.password,
      });

      this.setAccessToken(accessToken);
    } catch (e) {
      this.setError();
    }
  }
}

export default SignupFormStore;
```

`SignupFormStore`를 사용할 수 있는 훅을 만든다.

```tsx
// src/hooks/useSignupFormStore.ts

export default function useSignupFormStore() {
  const store = container.resolve(SignupFormStore);
  return useStore(store);
}
```

SignupForm 컴포넌트를 만든다.

```tsx
// src/components/signup/SignupForm.tsx

const Container = styled.div`
  h2 {
    margin-bottom: 1rem;
    font-size: 4rem;
  }

  button {
    margin-left: 10.5rem;
  }

  p {
    margin-block: 1rem;
  }
`;

export default function SignupForm() {
  const { setAccessToken } = useAccessToken();

  const [{
    email, name, password, passwordConfirmation, valid, error, accessToken,
  }, store] = useSignupFormStore();

  useEffect(() => {
    if (accessToken) {
      setAccessToken(accessToken);
    }
  }, [accessToken]);

  const handleChangeEmail = (value: string) => {
    store.changeEmail(value);
  };

 const handleChangeName = (value: string) => {
    store.changeName(value);
  };

  const handleChangePassword = (value: string) => {
    store.changePassword(value);
  };

  const handleChangePasswordConfirmation = (value: string) => {
    store.changePasswordConfirmation(value);
  };

  const handleSubmit = (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();
    store.signup();
  };

  return (
    <Container>
      <h2>회원 가입</h2>
      <form onSubmit={handleSubmit}>
        <TextBox
          label="E-mail"
          placeholder="tester@example.com"
          value={email}
          onChange={handleChangeEmail}
        />
        <TextBox
          label="Name"
          value={name}
          onChange={handleChangeName}
        />
        <TextBox
          label="Password"
          type="password"
          value={password}
          onChange={handleChangePassword}
        />
        <TextBox
          label="Password Confirmation"
          type="password"
          value={passwordConfirmation}
          onChange={handleChangePasswordConfirmation}
        />
        <Button type="submit" disabled={!valid}>
          회원 가입
        </Button>
        {error && (
          <p>회원 가입 실패</p>
        )}
      </form>
    </Container>
  );
}
```

`SignupPage`를 준비한다.

```tsx
export default function SignupPage() {
  const navigate = useNavigate();

  const [{ accessToken }, store] = useSignupFormStore();

  useEffect(() => {
    store.reset();
  }, []);

  useEffect(() => {
    if (accessToken) {
      store.reset();
      navigate('/signup/complete');
    }
  }, [accessToken]);

  return (
    <SignupForm />
  );
}
```

`SignupCompletePage`도 준비한다.

```tsx
// src/pages/SignupCompletePage.ts

export default function SignupCompletePage() {
  return (
    <p>
      회원 가입이 완료되었습니다.
    </p>
  );
}
```

`routes`에 추가.

```tsx
// src/routes.tsx

{ path: '/signup', element: <SignupPage /> },
{ path: '/signup/complete', element: <SignupCompletePage /> },
```

`LoginForm` 컴포넌트에 “회원 가입” 링크를 추가한다.

```tsx
// src/components/login/LoginForm.tsx

<p>
  <Link to="/signup">
    회원 가입
  </Link>
</p>
```
