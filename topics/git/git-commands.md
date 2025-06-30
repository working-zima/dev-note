# 깃 명령어

## 초기화 및 원격 저장소 설정

### `git init`

로컬 환경 디렉토리에서 깃 시스템으로 초기화

### `git remote add <별칭> <url>`

리모트 저장소 연결

- `<별칭>`은 일반적으로 `origin` 를 사용.
- `<url>` 은 깃 리모트 저장소 주소
- `url`에 해당하는 리모트 저장소를 `origin`이라는 별칭으로 연결하겠다는 의미

### `git remote prune origin`

로컬에서 삭제된 브랜치를 로컬에서도 지움

`git fetch` 할 때 원격 브랜치들의 최신 상태를 받아오지만,  
원격에서 삭제된 브랜치는 로컬에서 자동으로 사라지지 않기 때문에 수동으로 지워주어야 함

`git remote show origin` 명령어로 확인할 수 있음

출력 예시:

```bash
Remote branches:
  develop                 tracked
  feature/setting         tracked
  feature/login           stale (stale은 원격에는 없지만 로컬엔 남아 있는 브랜치)
```

## 브랜치 생성, 삭제, 전환

### `git branch`

존재하는 로컬 브랜치 확인

### `git branch -a`

존재하는 로컬 + 리모트 브랜치 확인

### `git branch <새 브랜치 이름>`

새 브랜치 생성

- 커밋 기록이 없다면 새 브랜치 생성 불가

### `git branch -d <삭제할 브랜치 이름>`, `git branch -D <삭제할 브랜치 이름>`

브랜치 지우기

- `d`: 브랜치가 다른 브랜치에 병합된 상태에만 삭제
- `D`: 병합 여부와 상관없이 강제 삭제

### `git checkout`

존재하는 브랜치가 있다면 해당 브랜치로 이동 (사용 지양)

- `checkout`은 `switch`와 `restore`의 기능을 합친 명령어

### `git switch <브랜치 이름>`

브랜치 간 전환 기능 (`checkout`의 대체 명령어)

### `git switch -`

**바로 직전에 작업하던 브랜치로 되돌아가기** 위한 단축 명령어

### `git switch -c <새 브랜치 이름>`

새로운 브랜치 민들고 해당 브랜치로 이동 (`checkout`의 대체 명령어)

### `git switch -c <새 브랜치 이름> <옵션> <기준 브랜치>`

기준 브랜치에서 새로운 브랜치 만들기 (생성과 동시에 이동)

- `<옵션>`
  - 사용 안할 경우 그냥 `develop` 브랜치를 기준으로 `<새 브랜치>`를 **생성** `<새 브랜치>`브랜치로 HEAD가 붙음
  - `--detach` 는 `develop` 브랜치를 기준으로 `<새 브랜치>`를 생성하지만 HEAD는 그 브랜치에 붙지 않음
    주로 특정 커밋이나 태그로 가서 잠깐 코드 확인하거나 실험하고 싶을 때 사용

## 브랜치 병합 및 업데이트

### `git pull`

원격 저장소에서 현재 체크아웃한 로컬 브랜치에 대한 변경사항을 받아오고 병합

- `git fetch` + `git merge` 효과.
- 현재 브랜치에 대해 원격 브랜치와 병합까지 자동 수행.
- `git pull <remote> <branch>` 로 특정 브랜치를 fetch 하고 현재 브랜치에 merge 할 수 있음.

### `git fetch`

모든 원격 브랜치의 상태 (`origin/*`)를 업데이트 함

- 모든 원격 브랜치의 최신 커밋 정보를 **로컬 `origin/*` 브랜치에만** 반영하여 업데이트.
- 로컬 작업 브랜치(`main`, `develop` 등)는 그대로 유지됨.

### `git merge`

현재 브랜치에 다른 브랜치의 변경 사항을 합치는 명령어입니다.

| merge 방식 종류                | 설명                                                                          |
| ------------------------------ | ----------------------------------------------------------------------------- |
| **Fast-forward merge**         | 중간에 다른 커밋이 없다면 브랜치가 그냥 앞으로 "빨리 감기"되어 병합됨         |
| **3-way merge (merge commit)** | 브랜치들이 나뉜 이후 모두 커밋이 존재하면, 병합 커밋(Merge commit)이 만들어짐 |
| **충돌(conflict)**             | 병합 대상 브랜치 간 같은 부분을 수정한 경우 충돌 발생. 수동으로 해결 필요     |

#### 1. Fast-forward merge

```plain
main:     A---B
feature:      \
               C---D
```

`main`이 아직 `C`와 `D`를 포함하지 않았고, 사이에 아무 작업도 없으면 `git merge feature`는 단순히 커밋을 앞으로 당깁니다.

```bash
# 결과 (Fast-forward):
A---B---C---D  (main, feature)
```

#### 2. Merge commit (3-way merge)

```plain
main:     A---B
feature:      \
               C---D
```

만약 `main`에도 커밋이 생겼다면:

```plain
main:     A---B---E
feature:      \
               C---D
```

이 경우는 `fast-forward`가 안 되므로 **병합 커밋**이 생깁니다.

```bash
git checkout main
git merge feature
```

```plain
결과:
          C---D
         /     \
A---B---E-------M  (main)
```

여기서 `M`은 merge commit입니다.

#### 충돌이 날 경우

```bash
Auto-merging index.js
CONFLICT (content): Merge conflict in index.js
Automatic merge failed; fix conflicts and then commit the result.
```

1. 충돌 부분을 직접 수정
2. `git add <파일>`로 해결 완료 표시
3. `git commit`으로 merge commit 생성

### `git merge --abort`

병합 도중 충돌이 나서 중단하고 싶을 때 사용하는 명령어

### `git rebase`

- 커밋을 다시 새로운 기반(base) 위로 옮기면서 기존 커밋을 복사해서 새 커밋으로 재작성하는 명령어
- 브랜치를 생성한 지점을, merge 대상의 최신 커밋 바로 뒤로 옮기고, 거기서 부터 작업을 시작한 것처럼 브랜치에서 작업한 커밋들을 다시 쌓는 것

### 기존 `merge` 된 브랜치 예시

```plain
A---B---C-------F       (main)
     \     \   /
      D---E---G         (feature)
```

### feature 브랜치에서 `git rebase main` 명령어 실행

```plain
A---B---C-------F         (main)
                 \
                   D'---E'---G'   (feature)
```

주로 불필요한 merge 커밋으로 브랜치 기록이 지저분해지는 것을 피하기 위하여 사용

### `git rebase` 주의 사항

1. 다른 개발자들이 이미 가지고 간 커밋( 이미 공유된 커밋 )을 리베이스하지 말아야 함

   만약 깃허브에 어떤 브랜치를 푸시했고 다른 사람들이 이미 여러분의 커밋을 자기 컴퓨터로 당겨 갔다고생각해 보세요,
   여러분이 리베이스를 하는 순간 히스토리가 재작성되니 커밋이 새로 생성되는 거잖아요
   이때 다른 개발자들의 컴퓨터에 반영된 깃 히스토리를 다시 서로 맞추는 건 무척 번거롭습니다.

### `git rebase --continue`

rebase 도중 충돌을 해결한 후, rebase 과정을 이어서 진행할 때 사용합니다.

```bash
$ git rebase main
# 충돌 발생!
# 수정 후...
$ git add conflict-file.js
$ git rebase --continue
# 모든 커밋이 처리될 때까지 이 과정을 반복하게 됩니다.
```

### `git rebase --abort`

진행 중인 rebase 작업을 완전히 취소**하고**, rebase를 시작하기 전 상태로 되돌리기 위해 사용합니다.

```bash
$ git rebase main
# 충돌 발생!
# 너무 복잡하네... 포기하고 원래대로 돌릴래
$ git rebase --abort
```

### `git rebase -i HEAD~<숫자>`

**Interactive Rebase**은 최근 로컬 커밋들을 선택적으로 **수정하거나 제거**, **메시지 변경** 등을 할 수 있는 명령어

최근 `<숫자>`개의 커밋을 대상으로 재정렬, 수정, 제거, 병합 등을 수행

- pick: 커밋 그대로 바꾸지 말고 유지하라는 의미
- reword: 커밋 메시지를 재작성
- edit: 커밋 시점으로 체크아웃하여 **코드를 수정**하고 다시 커밋 가능
- fixup: 해당 커밋의 코드를 이전 커밋으로 병합하고 커밋 메시지만 제거
- drop: 커밋 자체를 완전히 제거

`rebase`는 커밋 해시를 바꾸므로 이미 **공유된 커밋에 사용하면 충돌** 위험이 있기 때문에 **로컬에서만** 작업한 커밋에 사용

## 작업 상태 확인 및 복원

### `git restore [옵션] <파일 경로>`

수정한 파일을 복원 (되돌리기)

- 워킹 디렉토리 수정만 되돌리기 (아직 `git add` 안 했을 때)

  ```bash
  // index.js를 최근 커밋 상태로 되돌림
  git restore index.js
  ```

- 전체 파일 워킹 디렉토리 변경사항 삭제

  ```bash
  git restore .
  ```

- `git add`로 stage된 파일을 되돌리기 (`git add` 했지만 아직 commit은 안 했을 때)

  ```bash
  // staging 영역에서만 제거됨
  git restore --staged index.js
  ```

- 전체 staged 파일들 git add 취소

  ```bash
  git restore --staged .
  ```

- staged + working 디렉토리 모두 되돌리기

  ```bash
  // git add도 취소되고, 파일 수정도 되돌아감
  git restore --staged --worktree index.js
  ```

- 이전 커밋의 파일 상태로 복원하기

  ```bash
  git restore --source=<커밋 해시> index.js
  ```

### `git status`

현재 브랜치 상태, 커밋되지 않은 변경 사항, staged 파일, 추적되지 않는 파일 등을 보여줌

| 항목                                                  | 의미                                                                      |
| ----------------------------------------------------- | ------------------------------------------------------------------------- |
| `On branch main`                                      | 현재 내가 작업 중인 브랜치                                                |
| `Your branch is ahead of 'origin/main' by 2 commits.` | 원격(main)보다 로컬이 2개 더 앞섰음 (`push` 필요)                         |
| `Changes to be committed:`                            | `git add` 해서 스테이징(staged)된 파일들. `git commit` 하면 여기에 포함됨 |
| `Changes not staged for commit:`                      | 수정했지만 아직 `git add` 안 한 파일들                                    |
| `Untracked files:`                                    | Git이 아직 추적하지 않는 새 파일. (`git add` 하면 추적 시작됨)            |

#### Git이 파일을 관리하는 3가지 상태

| 상태                          | 설명                                                                                            |
| ----------------------------- | ----------------------------------------------------------------------------------------------- |
| **추적됨** (tracked)          | Git이 이미 관리 중(한 번이라도 add된 파일)이고, 이전 커밋에도 있던 파일 (수정 여부는 상관 없음) |
| **수정됨** (modified)         | 추적된 파일이지만 내용이 바뀜                                                                   |
| **스테이징됨** (staged)       | 다음 커밋에 포함되도록 준비된 상태                                                              |
| **추적되지 않음** (untracked) | Git이 아직 모르는 새 파일 (즉, `git add` 한 번도 안 한 파일)                                    |

HEAD는 언제나 master 브랜치에서 가장 최근에 커밋한 브랜치를 가리킵니다

## 커밋 관련 작업

### `git add`

파일을 staged 스테이지로 이동

### `git commit`

staged 에 있는 파일들을 커밋

- `git commit —amend` 는 Git에서 **방금 전 커밋을 수정**할 수 있게 해주는 명령어
- 커밋 메시지만 수정할 때

  ```bash
  git commit --amend
  # 에디터가 열리면 기존 커밋 메시지를 수정해서 저장
  ```

- 파일 추가 후 커밋 메시지까지 수정할 때

  ```bash
  # 빠뜨린 파일을 스테이징
  git add 빠진파일.js

  # 기존 커밋에 포함시키기
  git commit --amend
  ```

- 파일 추가 후 커밋 메시지는 그대로 쓸 때

  ```bash
  git add 빠뜨린파일.js
  git commit --amend --no-edit
  ```

## 원격 저장소와의 상호작용

### `git push`

현재 커밋된 파일들을 깃허브 리모트 리포지터리에 업로드

### `git push -u <원격저장소 별칭> <원격 브랜치>`

로컬 브랜치를 원격 저장소(origin)의 브랜치로 푸시하면서, 둘을 연결함

- 로컬 브랜치가 처음 만들어졌을 때는 **원격 브랜치와 연결돼 있지 않기 때문에** 그냥 `git push`나 `git pull`을 하면 어떤 브랜치로 푸시/풀 해야 할지 Git은 알 수 없음

`-u` vs `--set-upstream` vs `--set-upstream-to`

| 옵션                | 의미                                             |
| ------------------- | ------------------------------------------------ |
| `-u`                | `--set-upstream`의 축약형                        |
| `--set-upstream`    | 현재 브랜치를 원격 브랜치와 연결                 |
| `--set-upstream-to` | 이미 연결된 경우, 다른 브랜치로 재설정할 때 사용 |

### `git push <별칭> <로컬 브랜치>:<원격 브랜치>`

로컬 브랜치의 커밋을 원격 브랜치에 밀어넣을 때 쓰는 명령어

- 원격 브랜치의 커밋을 로컬 브랜치의 커밋으로 덮어씀
- **원격 브랜치의 커밋 기록이 로컬 브랜치와 완전히 같아짐**

### `git push <별칭> -d <삭제할 브랜치>`

리모트 브랜치 삭제

## **기록 및 로그 확인**

### `git log`

깃 기록 (커밋 내역) 확인
