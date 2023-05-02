- 가장 자주 사용하는 git 명령어를 알아보자

### 1) git log
```bash
git log
# commit d5a265e91986cf4a18f57f4121124f9f705ca366 (HEAD -> main, origin/main, origin/HEAD)
# Author: local-user <local-user@example.com>
# Date:   Fri Apr 28 10:11:13 2023 +0900
# 
#     이게 최근 커밋
# 
# commit f3a5b52caca9e77123501a195000ebc19341daec
# Author: local-user <local-user@example.com>
# Date:   Thu Apr 27 18:31:51 2023 +0900
# 
#     이게 예전 커밋
# 
# ...
```
- 해당 명령어를 입력하면 터미널에서 git에 설정된 에디터를 불러와 현재 git 리포지토리에서 작업한 내역을 보여줌
- commit과 메세지, reset 내역, 작성자와 시간 등을 확인할 수 있으며, 내역은 최신 기록을 맨 위로 보여줌
- 만약 터미널의 크기가 작다면 방향키를 사용하여 내용을 확인할 수 있음
- 또한 바로 빠져나오고 싶다면 `q` 를 입력하면 됨

### 2) git checkout과 git switch
- 상세한 내용은 아래 참고
> [git checkout과 git switch](./git_checkout과_git_switch.md)

- 나의 경우, linux ubuntu에 설치된 깃 버전이 2.17이기 때문에 `git switch`를 사용할 수 없음
- 특이한 경우가 아니라면 가급적 `git switch`를 사용하자

```bash
# 브랜치 전환
git switch branch-name

# 커밋 전환
git switch --detach a5518fa
```

### 3) 브랜치 생성과 삭제
- 최근 커밋 내역에서 브랜치를 새로 만든다면 다음과 같음
```bash
git branch new-branch
```

- 이제 git 리포지토리에 있는 브랜치를 확인하기 위해 다음을 입력
```bash
git branch
# * main
#   new-branch
```

- 만약 해당 브랜치를 삭제하고 싶다면 아래 명령어 입력
```bash
git branch -d test
# new-branch 브랜치 삭제 (과거 d5a265e).
```
- 어느 시점의 커밋이 있었는지 알려주기도 함

만약 특정 커밋 시점에서 브랜치를 만들고 싶다면 아래와 같이 입력
```bash
git switch --detach f3a5b52caca9e77123501a195000ebc19341daec
```
- 위 명령어를 실행하면 현재 체크아웃 된 브랜치 대신에 해당 커밋으로 HEAD가 분리됨
- 이후에 새로운 브랜치를 생성해서 해당 브랜치에서 작업할 수 있음

```bash
git branch new-branch

git log
# commit f3a5b52caca9e77123501a195000ebc19341daec (new-branch)
# Author: local-user <local-user@example.com>
# Date:   Thu Apr 27 18:31:51 2023 +0900
# 
#     이게 예전 커밋
# 
# ...
```
- 해당 커밋 시점으로 브랜치가 생성된 것을 알 수 있음

### 4) git status
- 현재 작업 중인 git 저장소의 상태를 확인할 수 있는 명령어임
- 명령어를 실행하면 다음과 같은 정보를 출력함
	- 현재 브랜치 이름
	- 작업 중인 커밋과 이전 커밋 사이의 차이(변경된 파일 목록)
	- Untracked 파일 목록(git으로 추적되지 않은 파일)
```bash
git status
# 현재 브랜치 main
# 브랜치가 'origin/main'에 맞게 업데이트된 상태입니다.
# 
# 커밋하도록 정하지 않은 변경 사항:
#   (무엇을 커밋할지 바꾸려면 "git add <파일>..."을 사용하십시오)
#   (use "git restore <file>..." to discard changes in working directory)
# 수정함:  "git/README.md"
# 
# 커밋할 변경 사항을 추가하지 않았습니다 ("git add" 및/또는 "git commit -a"를 사용하십시오)
```

- 위 예시에서는 main 브랜치에서 작업중이며, READMD.md 파일이 수정되었음

### 5) git commit
- `git commit`은 git 저장소에 변경 사항을 커밋하는데 사용되며, 다양한 옵션을 제공함
- 일반적으로 `git commit -m` 옵션을 사용하여 짧은 커밋 메세지를 작성하는 편이며, 그 외에도 다양한 옵션이 있음

- 우리는 커밋을 하기 위해 커밋할 파일을 추가하는 작업을 할 것임
- 일반적으로 커밋 파일을 추가하기 위해 아래 명령어를 입력함
```bash
git add .
```
- 위 명령어는 git 저장소에 모든 파일을 커밋할 파일로 추가하는 것임

- 그리고 보통 짧은 커밋 메세지를 위해 아래 명령어를 입력함
```bash
git commit -m "커밋 메세지"
```

- 이 작업은 간단하지만 2단계를 거쳐야 하므로 이렇게 축약할 수 있음
```bash
git commit -am "커밋 메세지"
```

- 이렇게 입력하면 변경된 모든 파일을 자동으로 스테이징하기 때문에 `git add` 명령어를 생략할 수 있음
- git log를 찍어보면 아래와 같이 뜸
```bash
git log
# commit d5a265e91986cf4a18f57f4121124f9f705ca366 (HEAD -> main)
# Author: local-user <local-user@example.com>
# Date:   Fri Apr 28 12:56:34 2023 +0900
# 
#     커밋 메세지
# 
# commit d5a265e91986cf4a18f57f4121124f9f705ca366 (origin/main, origin/HEAD)
# Author: local-user <local-user@example.com>
# Date:   Fri Apr 28 10:11:13 2023 +0900
# 
#     이게 최근 커밋
# 
# commit f3a5b52caca9e77123501a195000ebc19341daec
# Author: local-user <local-user@example.com>
# Date:   Thu Apr 27 18:31:51 2023 +0900
# 
#     이게 예전 커밋
# 
# ...
```

- 만약 내가 커밋 메세지를 잘못 입력했을 때는 어떻게 해야할까?
- `git commit --amend` 명령어를 사용하면 바로 직전 커밋을 수정할 수 있음
```bash
git commit --amend
```
- 위 명령어를 실행하면 최근 커밋 메세지를 수정할 수 있는 에디터 창이 열림. 나의 경우, vi 에디터였음
- 맨 윗줄이 입력한 커밋 메세지였음. 내용 수정을 위해 `:i`를 입력하고 내용을 변경한 후, `:wq`로 저장 후 종료하였음
- 다시 git log로 확인하면 아래와 같이 정상적으로 수정된 것을 확인할 수 있음
```bash
git log
# commit d5a265e91986cf4a18f57f4121124f9f705ca366 (HEAD -> main)
# Author: local-user <local-user@example.com>
# Date:   Fri Apr 28 12:56:34 2023 +0900
# 
#     git commit --amend로 커밋 메세지 변경 테스트.  # <- 잘 수정되었다
# 
# commit d5a265e91986cf4a18f57f4121124f9f705ca366 (origin/main, origin/HEAD)
# Author: local-user <local-user@example.com>
# Date:   Fri Apr 28 10:11:13 2023 +0900
# 
#     이게 최근 커밋
# 
# commit f3a5b52caca9e77123501a195000ebc19341daec
# Author: local-user <local-user@example.com>
# Date:   Thu Apr 27 18:31:51 2023 +0900
# 
#     이게 예전 커밋
# 
# ...
```

- 혼자 작업할 때는 큰 문제가 없겠지만 만일 이미 push를 통해 원격 저장소에 커밋이 업로드 되어있고, 다른 개발자들이 저장소에서 해당 커밋을 사용하고 있다면 충돌이 발생할 수 있음
- 이런 경우에는 수정한 커밋을 다시 push 하는 것이 아니라, 새로운 커밋을 생성하여 push하는 것이 좋음

### 6) git push
- commit까지 다 했다면 이제 나의 로컬 저장소에 변경된 git 리포지토리의 내용을 원격 저장소에 업로드하는 것만 남았음
- 일반적으로는 `git push` 명령어를 사용하지만 초기 git 설정 시, 다른 사람들과 git을 통해 프로젝트를 처음 진행하다보면 권한 문제나 브랜치 문제로 여러가지 애를 먹을 때가 있음
- 내가 작업하는 브랜치가 `feature/info` 라고 가정해보고 이 브런치에 수정된 내용을 원격 저장소에 push해보겠음
```bash
git push origin feature/info
```

- 위 명령어에서 `origin`은 git에서 원격 저장소의 이름을 지정하는 기본적인 이름임
- git은 `origin`이라는 이름으로 원격 저장소를 자동으로 생성하며, 이 이름을 사용하여 원격 저장소를 참조할 수 있음
- 즉, 위 명령어는 `feature/info` 브랜치에서 변경된 내용을 `origin` 원격 저장소에 업로드하겠다는 의미임

- 만약 아래와 같은 오류가 발생할 경우
```bash
git push origin feature/info
# fatal: The current branch feature/info has no upstream branch.
# To push the current branch and set the remote as upstream, use
# 
#     git push --set-upstream origin feature/info
# 
# To have this happen automatically for branches without a tracking
# upstream, see 'push.autoSetupRemote' in 'git help config'.
```

- `feature/info` 브랜치가 원격 저장소의 어떤 브랜치와도 연결되어 있지 않아 발생하는 오류임
- git은 브랜치를 생성하면 자동으로 해당 브랜치가 로컬 저장소에서만 존재하는  '로컬 브랜치'로 생성됨
- 이 로컬 브랜치는 원격 저장소와 동기화되지 않기 때문에 원격 저장소와 연결해야 함
- 해결 방법으로는 `--set-upstream` 옵션을 사용하여 현재 브랜치를 원격 저장소의 특정 브랜치와 연결하면 됨
```bash
git push --set-upstream origin feature/info
```
- 정상적으로 작동되었다면 이후부터는 `git push origin feature/info`을 사용하면 됨

### 7) git merge
- 만일 내가 `feature/info`에서 작업하고 있다가 해당 내용을 `main` 브랜치에 병합해야 한다고 가정해보자
- 먼저 push할 커밋 내역이 남아있는지 확인함
```bash
git push
# Everything up-to-date
```

- 위와 같이 뜬다면 커밋 내역이 모두 원격 저장소에 업로드되어 있는 상황임
- 현재 내 로컬에서 커밋하지 않은 내역이 있을 수도 있으므로 `git stash` 명령어로 로컬에서 작업한 내역을 저장하고 최근 커밋으로 돌아감
```bash
git stash
# Saved working directory and index state WIP on main: 55691ce {최근 커밋 메세지}
```
- 로컬 작업 내용은 stash에 가상으로 저장되고, `{최근 커밋 메세지}`라고 적은 55691ce 로 작업 내용이 돌아감

- 이제 합칠 브랜치인 `main` 으로 이동
```bash
git switch main
```

- `feature/info` 브랜치의 최신 변경 내용을 main 브랜치로 병합함
```bash
git merge feature/info
```

- 병합 결과를 바로 원격 저장소에 push할 수도 있지만, 로컬 작업내용이 남아있기 때문에 다시 작업 내용을 불러옴
```bash
git stash apply
```

- 변경된 내용을 다시 커밋하고 원격 저장소에 push함
```bash
git commit -am "feature/info -> main merge 완료"
git push     # <- 현재 브랜치를 기준으로 push함
```

