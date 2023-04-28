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

### 5) 커밋 및 푸시
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