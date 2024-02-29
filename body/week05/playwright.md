# 4. Playwright

## 학습 키워드

- E2E(End to End) Test
- Headless Chrome
- Puppeteer
- Playwright
- CodeceptJS

## E2E(End to End) Test

실제 브라우저가 필요하고 애플리케이션이 연결된 서버가 필요한 테스트 입니다.\
사용자 시나리오를 흉내내어 모든 구성 요소가 의도한 대로 작동하는지 애플리케이션의 전체 시스템을 테스트합니다.\
실제 사용 환경에서 발생할 수 있는 문제를 식별할 수 있습니다.

## Headless Chrome

Headless Chrome은 브라우저 창이(GUI) 없이 Chrome 브라우저를 실행하는 모드입니다.\
리소스를 덜 소모하면서 브라우저를 제어할 수 있으며, 주로 웹 스크래핑이나 자동화 테스트 시나리오에서 사용됩니다.

## Puppeteer

Google이 개발한 Node.js 오픈 소스 라이브러리로, Headless Chrome을 자동화 및 제어하는 데 사용됩니다.

## Playwright

💡 Microsoft가 개발한 웹 브라우저 기반 E2E 테스트 자동화 도구입니다.
Headless Chrome을 기반으로 한 Puppeteer를 계승하면서, 더 많은 웹 브라우저를 지원합니다.

### Playwright 패키지 설치

```bash
npm i -D @playwright/test eslint-plugin-playwright
```

### Playwright 파일 세팅

`playwright.config.ts` 파일로 Playwright 설정

```jsx
import { PlaywrightTestConfig } from '@playwright/test';

const config: PlaywrightTestConfig = {
  // tests 폴더에 파일이 있음
  testDir: './tests',
  retries: 0,
  use: {
    // 테스트 할 URL 주소
    baseURL: 'http://localhost:8080',
    // GitHub Action등의 CI 파이프라인이 있다면 headless로 사용
    headless: !!process.env.CI,
    screenshot: 'only-on-failure',
  },
};

export default config;
```

`tests/.eslintrc.js` 파일로 테스트 파일의 eslint 설정

```jsx
module.exports = {
  env: {
    jest: false,
  },
  extends: ['plugin:playwright/playwright-test'],
  rules: {
    'import/no-extraneous-dependencies': 'off',
  },
};
```

`tests/home.spec.ts`으로 테스트

```jsx
// jest의 test와 expect와는 다름
import { test, expect } from '@playwright/test';

test('Filter products', async ({ page }) => {
  await page.goto('/');

  await expect(page.getByText('Apple')).toBeVisible();

  const searchInput = page.getByLabel('Search');

  await searchInput.fill('a');

  await expect(page.getByText('Apple')).toBeVisible();

  await searchInput.fill('aa');

  await expect(page.getByText('Apple')).toBeHidden();
});

test('Click the “Increase” button', async ({ page }) => {
  await page.goto('/');

  const count = 13;

  // 순회 가능한 객체에 주어진 모든 프로미스를 이행
  await Promise.all(
    [...Array(count)].map(async () => {
      await page.getByText('Increase').click();
    }),
  );

  await expect(page.getByText(`${count}`)).toBeVisible();
});
```

`.gitignore` 파일에 에러 상황의 스크린샷 등이 담기는 `test-results` 디렉터리 추가.

```json
# Playwright
/test-results/
```

### 테스트 실행

```bash
npx playwright test
```

headless로 실행하려는 경우

```bash
CI=true npx playwright test
```

## CodeceptJS

인간 친화적인 E2E 테스팅 도구로 CodeceptJS가 있습니다.

### CodeceptJS 설치

```bash
npx create-codeceptjs .
```

```bash
sed "s/    /  /g" package.json > package.json.tmp
rm package.json
mv package.json.tmp package.json
```

### CodeceptJS 실행

```bash
# 웹 브라우저를 화면에 띄워 테스트를 실행합니다.
npm run codeceptjs

# 웹 브라우저를 화면에 띄우지 않고 테스트를 실행합니다.
npm run codeceptjs:headless

# 웹 브라우저에 CodeceptUI를 띄워 훨씬 편학게 테스트를 실행합니다.
npm run codeceptjs:ui
```

### CodeceptJS 프로젝트 세팅

```bash
npx codeceptjs init
```

```bash
? Where are your tests located? (./*_test.js)
# => ./tests/**/*_test.js

? What helpers do you want to use?
(Use arrow keys)
# => ❯ Playwright

? Where should logs, screenshots, and reports to be stored? (./output)
# => ./output

? Do you want localization for tests? (See https://codecept.io/translation/)
(Use arrow keys)
# => ❯ English (no localization)

? [Playwright] Base url of site to be tested (http://localhost)
# => http://localhost:8080

? [Playwright] Show browser window (Y/n)
# => Y

? [Playwright] Browser in which testing will be performed.
Possible options: chromium, firefox or webkit (chromium)
# => chromium

Creating a new test...
----------------------

? Feature which is being tested (ex: account, login, etc)
# => google

? Filename of a test (google_test.js)
# => google_test.jss
```

## 참고 자료

- [Playwright](https://playwright.dev/)
- [Playwright Configuration](https://playwright.dev/docs/test-configuration)
- [Ashal의 Playwright](https://github.com/ahastudio/til/blob/main/test/playwright.md)
- [CodeceptJS](https://codecept.io/)
- [CodeceptJS 3 시작하기](https://github.com/ahastudio/til/blob/main/test/20201207-codeceptjs.md)
- [CodeceptJS 사용](https://github.com/ahastudio/CodingLife/tree/main/20211012/react#codeceptjs-사용)
