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
- `git checkout`과 `git switch`는 git에서 브랜치나 커밋을 전환하는 명령어임
- 두 명령어는 비슷한 동작을 하지만 git 2.23 버전부터 `git switch` 명령어가 추가되어 `git checkout` 대신 `git switch`를 사용하는 것이 권장됨

1. git chechout
- 브랜치를 전환함
- 커밋을 체크아웃함(커밋을 전환하고, 이전 커밋의 내용을 작업 디렉토리로 복원함)
- 파일을 체크아웃함(특정 파일의 내용을 작업 디렉토리에 복원함)

2. git switch
- 브랜치를 전환함
- 커밋을 스위치함(현재 작업 디렉토리의 변경 내용을 보존한 채, 새로운 커밋으로 전환함)

- 두 명령어의 가장 큰 차이점은 커밋을 전환하는 경우임
- `git checkout`은 이전 커밋의 내용을 작업 디렉토리에 복원하므로 작업 디렉토리에 변경된 내용이 있다면 이를 덮어쓰게 됨
- 반면에 `git switch`는 변경 내용을 보존한 채로 커밋을 전환하므로, 작업 디렉토리에 변경된 내용이 있어도 이를 보존할 수 있음

- 따라서 git 2.23 이후 버전에서는 `git switch`를 사용하여 커밋을 전환하는 것이 좋음
- 
- git은 커밋을 저장할 때 SHA-1 해시 알고리즘을 사용하여 고유한 해시값을 생성함. 따라서 커밋을 고유하게 식별할 수 있음
- 물론 우리는 어느 시점의 commit으로 돌아갈지 확인해야 함으로 git log를 통해 commit 내역을 확인한 후, 명령어를 사용하여 특정 커밋 시점으로 이동할 수 있음

```bash
git checkout a5518fa
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

