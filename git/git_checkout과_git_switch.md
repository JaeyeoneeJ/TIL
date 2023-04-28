## git checkout과 git switch
- `git checkout`과 `git switch`는 git에서 브랜치나 커밋을 전환하는 명령어임
- 두 명령어는 비슷한 동작을 하지만 git 2.23 버전부터 `git switch` 명령어가 추가되어 `git checkout` 대신 `git switch`를 사용하는 것이 권장됨

### 1) git chechout
- 브랜치를 전환함
- 커밋을 체크아웃함(커밋을 전환하고, 이전 커밋의 내용을 작업 디렉토리로 복원함)
- 파일을 체크아웃함(특정 파일의 내용을 작업 디렉토리에 복원함)

### 2) git switch
- 브랜치를 전환함
- 커밋을 스위치함(현재 작업 디렉토리의 변경 내용을 보존한 채, 새로운 커밋으로 전환함)

### 3) git switch는 왜 생겨났을까
- 두 명령어의 가장 큰 차이점은 커밋을 전환하는 경우임
- `git checkout`은 이전 커밋의 내용을 작업 디렉토리에 복원하므로 작업 디렉토리에 변경된 내용이 있다면 이를 덮어쓰게 됨
- 반면에 `git switch`는 변경 내용을 보존한 채로 커밋을 전환하므로, 작업 디렉토리에 변경된 내용이 있어도 이를 보존할 수 있음

- 따라서 git 2.23 이후 버전에서는 `git switch`를 사용하여 커밋을 전환하는 것이 좋음

## 사용 예시
### 1) 브랜치 전환 예시
- 임의로 test라는 브랜치를 만들어보겠음
```bash
git branch test
```

- test 브랜치로 옮겨보자
```bash
# checkout 사용
git checkout test
# 'test' 브랜치로 전환합니다

# switch 사용
git switch test
# 'test' 브랜치로 전환합니다
```

- 브랜치의 전환은 두 명령어에서 차이가 없음

### 2) 커밋 전환
- git은 커밋을 저장할 때 SHA-1 해시 알고리즘을 사용하여 고유한 해시값을 생성함. 따라서 커밋을 고유하게 식별할 수 있음
- 물론 우리는 어느 시점의 commit으로 돌아갈지 확인해야 함으로 git log를 통해 commit 내역을 확인한 후, 명령어를 사용하여 특정 커밋 시점으로 이동할 수 있음

- 먼저 `git log` 명령어로 돌아갈 커밋 위치를 확인해보자
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
- 현재의 커밋의 해시 값은 `d5a265e91986cf4a18f57f4121124f9f705ca366` 이며, 예전의 커밋의 해시 값은 `f3a5b52caca9e77123501a195000ebc19341daec`임

- git은 일부 해시값만 사용하여 커밋을 식별할 수 있음
- 예를 들어 예전 커밋으로 전환하기 위해 다음과 같이 작성할 수 있음
```bash
git switch f3a5b52caca9e77123501a195000ebc19341daec
# 또는
git switch f3a5b52

# 출력 결과
# fatal: a branch is expected, got commit 'f3a5b52caca9e77123501a195000ebc19341daec' 힌트: If you want to detach HEAD at the commit, try again with the --detach option.
```

- 이런 에러가 뜨는 이유는 `git switch` 명령어를 사용할 때 브랜치 이름 대신에 커밋 해시 값을 입력해서임
- 기본적으로 `git switch` 명령어는 브랜치를 전환하는 명령어이기 때문임
- 만약 해당 커밋에 대해 독립적인 작업을 하고 싶다면, `--detach` 옵션을 사용해서 HEAD를 분리해야 함
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