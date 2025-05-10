# 시작하기(Get started)

## 설치(Install)

npm을 사용하여 Husky를 설치합니다:

```shell
npm install --save-dev husky
```

## husky init(권장)

`init` 명령은 Husky를 프로젝트에 설정하는 과정을 간소화합니다. 이 명령은 `.husky/` 디렉터리에 `pre-commit` 스크립트를 생성하고, `package.json`에 `prepare` 스크립트를 업데이트합니다. 워크플로에 맞게 수정이 가능합니다.

### 실행 방법

npm을 사용하여 초기화합니다:

```shell
npx husky init
```

### 테스트하기

축하합니다! 한 줄 명령으로 첫 Git 훅 설정을 완료했습니다 🎉. 아래 명령으로 테스트해 보세요:

```shell
git commit -m "Keep calm and commit"
# 커밋할 때마다 test 스크립트가 실행됩니다.
```

## 추가 설명

### 스크립팅(Scripting)

대부분의 경우 훅 안에서 `npm run` 또는 `npx` 명령을 실행하지만, POSIX 셸을 사용하여 사용자 정의 워크플로를 스크립팅할 수도 있습니다.

예를 들어, 다음은 외부 종속성 없이 커밋할 때마다 스테이징된 파일을 린트하는 두 줄짜리 셸 코드입니다:

```shell
# .husky/pre-commit

prettier $(git diff --cached --name-only --diff-filter=ACMR | sed 's| |\\ |g') --write --ignore-unknown
git update-index --again
```

이는 기본적으로 작동하는 예제이며, 더 강력한 기능이 필요하다면 [lint-staged](https://github.com/lint-staged/lint-staged)를 확인하세요.

### 훅 비활성화(Disabling hooks)

Husky는 Git 훅을 강제하지 않습니다. 글로벌 비활성화(`HUSKY=0`)가 가능하며, 필요에 따라 선택적으로 활성화할 수도 있습니다. 자세한 내용은 "How To" 섹션을 참고하세요.
