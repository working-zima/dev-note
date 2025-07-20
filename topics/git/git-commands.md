# 📚 Git 명령어 정리 (역할 기반)

## 🔀 브랜치 병합 및 업데이트

### `git fetch origin`

원격 저장소의 변경사항을 가져오되, 병합하지 않음

```bash
git fetch origin
```

- 로컬 `origin/*` 브랜치만 최신 상태로 업데이트
- 현재 작업 중인 브랜치에는 영향 없음

### `git merge <브랜치>`

다른 브랜치의 변경사항을 현재 브랜치로 병합

```bash
git merge feature/api
```

#### 병합 방식

| 유형         | 설명                                                    |
| ------------ | ------------------------------------------------------- |
| Fast-forward | 브랜치가 diverge되지 않았다면 단순히 앞으로 당겨서 병합 |
| 3-way merge  | 공통 조상 이후로 양쪽에 커밋이 있으면 병합 커밋 생성    |
| Conflict     | 같은 파일의 같은 부분을 수정했을 경우 충돌 발생         |

### `git merge --abort`

병합 도중 충돌이 발생했을 때 병합 취소

```bash
git merge --abort
```

### `git rebase <브랜치>`

브랜치 기반 변경. 커밋 히스토리를 재작성하여 병합 커밋 없이 깔끔한 이력 유지

```bash
git rebase main
```

#### 사용 시 주의사항

- 공유된 커밋에 사용 금지 (협업 브랜치에는 `merge` 권장)
- 주로 개인 브랜치에서만 사용

### `git rebase --continue`

충돌 해결 후 rebase 계속 진행

```bash
git rebase --continue
```

### `git rebase --abort`

진행 중인 rebase를 완전히 취소

```bash
git rebase --abort
```

### `git rebase -i HEAD~<숫자>`

최근 `<숫자>`개의 커밋을 인터랙티브하게 수정

```bash
git rebase -i HEAD~3
```

옵션:

- `pick`: 그대로 유지
- `reword`: 메시지만 변경
- `edit`: 커밋 내용 수정
- `fixup`: 이전 커밋에 합치고 메시지 제거
- `drop`: 커밋 삭제

## 🛠 작업 상태 확인 및 복원

### `git status`

현재 브랜치 상태, 스테이지 상태, 추적되지 않은 파일 등 확인

```bash
git status
```

### `git stash`

작업 중인 변경사항을 임시로 저장

```bash
git stash
```

변형:

- `git stash -m "메시지"`: 메시지와 함께 저장
- `git stash -u`: untracked 파일 포함
- `git stash list`: 저장된 stash 목록 확인
- `git stash apply`: stash 적용 (목록 유지)
- `git stash pop`: stash 적용 + 삭제

### `git stash` 활용 예시

#### 리모트 브랜치 반영 전 임시 저장

```bash
git stash
git fetch origin
git rebase origin/develop
git stash pop
```

#### 브랜치 전환 전 임시 저장

```bash
git stash
git switch hotfix
# 작업 후 다시 돌아와서
git switch feature/login
git stash pop
```

### `git restore`

파일 변경사항 되돌리기

#### 워킹 디렉토리 되돌리기

```bash
git restore index.js
```

#### 전체 워킹 디렉토리 초기화

```bash
git restore .
```

#### 스테이징 제거 (git add 취소)

```bash
git restore --staged index.js
```

#### 전체 스테이징 제거

```bash
git restore --staged .
```

#### staged + working 모두 복원

```bash
git restore --staged --worktree index.js
```

#### 특정 커밋 기준 복원

```bash
git restore --source=<커밋 해시> index.js
```

### Git이 관리하는 3가지 상태

| 상태      | 설명                             |
| --------- | -------------------------------- |
| Tracked   | Git이 이미 관리하는 파일         |
| Modified  | 변경된 상태 (아직 커밋되지 않음) |
| Staged    | 커밋 대기 중인 상태              |
| Untracked | Git이 추적하지 않는 새 파일      |

## 📝 커밋 관련 작업

### `git add`

변경된 파일을 staging 영역으로 추가

```bash
git add 파일명
git add .             # 현재 디렉토리 이하 전체
git add -A            # 전체 파일 (삭제 포함)
```

### `git commit`

staged 파일을 하나의 커밋으로 기록

```bash
git commit -m "커밋 메시지"
```

### `git commit --amend`

마지막 커밋을 수정

- 메시지 수정
- 누락된 파일 추가 포함 가능

```bash
# 커밋 메시지 수정
git commit --amend

# 누락된 파일 추가 후 메시지 수정
git add 파일.js
git commit --amend

# 메시지는 그대로, 내용만 추가
git add 파일.js
git commit --amend --no-edit
```

> 참고: 이미 푸시한 커밋을 amend 하면 강제 push (`--force`)가 필요하며 협업 중에는 주의해야 함

## 🌐 원격 저장소와의 상호작용

### `git remote add <별칭> <url>`

원격 저장소를 로컬 저장소에 연결

```bash
git remote add origin https://github.com/user/repo.git
```

### `git push`

로컬 커밋을 원격 저장소에 업로드

```bash
git push
```

### `git push -u <원격> <브랜치>`

처음 푸시할 때 원격 브랜치와 연결까지 설정

```bash
git push -u origin main
```

### `git push <원격> <로컬>:<원격>`

브랜치 명시적 푸시

```bash
git push origin dev:dev
```

### `git push <원격> -d <브랜치>`

원격 브랜치 삭제

```bash
git push origin -d feature/login
```

### `git fetch`

원격 저장소의 변경사항을 로컬 저장소의 origin/\* 형태로 반영

```bash
git fetch
```

### `git pull`

원격 저장소의 변경사항을 현재 브랜치로 가져와 병합

```bash
git pull
```

- 내부적으로 `git fetch` + `git merge`
- 현재 브랜치가 원격 브랜치와 연동돼 있어야 함

```bash
git pull
git pull origin develop
```

### `git remote show origin`

원격 저장소의 브랜치 및 상태 확인

```bash
git remote show origin
```

### `git remote prune origin`

원격에서 삭제된 브랜치를 로컬에서도 삭제

```bash
git remote prune origin
```

출력 예시:

```bash
Remote branches:
  develop           tracked
  feature/login     stale
```

> stale은 원격에 없지만 로컬에는 남은 브랜치

## 📜 기록 및 로그 확인

### `git log`

커밋 기록 확인

```bash
git log
```

옵션 예시:

```bash
git log --oneline                # 간결한 출력
git log --graph --oneline        # 브랜치 구조 시각화
git log --all --decorate         # 모든 브랜치 커밋 보기
git log -p                      # 각 커밋의 변경사항(diff) 출력
```

### 커밋 상태 설명

| 메시지 예시                                           | 의미                                          |
| ----------------------------------------------------- | --------------------------------------------- |
| `Your branch is ahead of 'origin/main' by 2 commits.` | 로컬 브랜치가 원격보다 2커밋 앞섬 (푸시 필요) |
| `Changes to be committed:`                            | 커밋 예정 (staged) 상태                       |
| `Changes not staged for commit:`                      | 수정은 됐지만 아직 git add 하지 않음          |
| `Untracked files:`                                    | Git이 관리하지 않는 새 파일 (git add 필요)    |

### Git의 3가지 파일 상태

| 상태 (영문) | 설명                       |
| ----------- | -------------------------- |
| `tracked`   | Git이 추적 중인 파일       |
| `modified`  | 변경되었으나 아직 add 안됨 |
| `staged`    | 커밋될 준비가 된 상태      |
| `untracked` | Git이 처음 보는 새 파일    |

> HEAD는 현재 브랜치에서 가장 최근 커밋을 가리키는 포인터입니다.
