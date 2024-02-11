# Pull Request 훈련하기

1. 복제할 repository 를 fork 로 복사하여 본인 계정의 GitHub로 동일한 repositiory 를 생성합니다.

2. 내 계정에 복제된 repository에서 초록색 Code 버튼을 눌러 나오는 주소를 복사해 줍니다.

3. 로컬 환경에서 터미널을 이용해 새 프로젝트를 clone합니다.

    ```bash
    # origin 원격 저장소 추가
    git clone <복사한 Repository 주소>

    # 예시)
    git clone git@github.com:working-zima/git-training.git
    ```

4. PR 보낼 저장소 주소를 upstream이라는 이름으로 추가합니다.

    ```bash
    # 생성된 폴더로 이동
    cd git-training


    # upstream 원격 저장소 추가
    git remote add upstream <PR 보낼 원격 Repository 주소>

    # 예시)
    git remote add upstream git@github.com:megaptera-kr/git-training.git
    ```

5. upstream 원격 저장소들이 잘 추가되었는지 확인합니다.

    ```bash
    # 원격 저장소 확인
    git remote -v

    # 아래와 같은 결과가 나왔는지 확인합니다.
    origin      git@github.com:working-zima/git-training.git (fetch)
    origin      git@github.com:working-zima/git-training.git (push)
    upstream      git@github.com:megaptera-kr/git-training.git (fetch)
    upstream      git@github.com:megaptera-kr/git-training.git (push)
    ```

6. upstream 원격 저장소의 최신 상태를 반영합니다.

    ```bash
    git fetch upstream

    git rebase upstream/main
    ```

7. 새 브랜치를 생성해줍니다.

    ```bash
    # 새 브랜치 생성 (브랜치 이름은 자신의 깃헙 유저네임으로 설정하기)
    git switch -c <브랜치 이름> upstream/main

    # 예시)
    git switch -c working-zima upstream/main
    ```

8. 작업을 진행합니다.

9. 작업이 완료된 후 변경된 점을 커밋합니다.

    ```bash
    # 변경된 점 모두 추가
    git add .

    # 변경된 점 커밋
    git commit
    ```

10. origin 원격 저장소에 작업 브랜치를 올립니다.

    ```bash
    git push origin <브랜치 이름>
    ```

11. 원본 레포지토리를 열고 페이지 상단에 Compare & pull request 버튼을 클릭합니다.

12. PR(Pull Request)을 요청 할 때, base repository의 main에 PR 요청을 보내는 것이 아닌 꼭 이미 생성되어 있는 본인의 GitHub Username과 동일한 브랜치에 보내야 한다는 점을 기억해주세요.\
(`base:working-zima`로 변경)
