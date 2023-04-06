## ls 사용법
- 디렉토리 목록을 확인하기 위해 사용함
- 보통 ls 명령어를 인자 없이 사용하는 경우도 있지만, 대부분은 명령어에 옵션을 사용함
- 인자 없이 사용하면 해당 디렉토리 내에 있는 파일과 디렉토리를 쭉 나열하는 수준으로 이름과 파일을 출력하는데 파일인지 디렉토리인지는 보통 색으로 구분되거나 구분되지 않음
- 소유권이나 만든 날짜, 용량 등을 확인하려면 ls 명령어 단독으로는 확인할 수 없기 때문에 `ls -al`과 같이 옵션을 사용함

### ls 옵션
- -a: 디렉토리 안에 있는 모든 파일과 디렉토리를 보여줌. 숨김 파일도 포함(all)
- -l: 파일에 대한 정보를 자세하게 보여줌. 사용자의 권한, 소유자 그룹, 크기, 날짜 등을 자세하게 보여줌(list)
- -h: -l에서 지정한 크기 등을 사람이 읽기 쉽게 변환하여 보여줌(humanize)

```bash
pwd
# 출력 결과: /home/ubuntu

ls
# 출력 결과: app

ls -a
# 출력 결과:
# .   .bash_logout  .cache          .profile  .sudo_as_admin_successful  app
# ..  .bashrc       .mysql_history  .ssh      .viminfo

ls -l
# 출력 결과:
# total 4
# drwxrwxr-x 3 ubuntu ubuntu 4096 Apr  6 09:10 app

ls -h
# 출력 결과: app
```

- ls -al을 사용하면 전체 내용을 보여줌. 보통의 경우 아래와 같이 정보를 보여줌
```bash
ls -al
# 출력 결과:
# total 40
# drwxr-xr-x 5 ubuntu ubuntu 4096 Apr  6 09:10 .
# drwxr-xr-x 3 root   root   4096 Apr  5 13:14 ..
# -rw-r--r-- 1 ubuntu ubuntu  220 Feb 25  2020 .bash_logout
# -rw-r--r-- 1 ubuntu ubuntu 3867 Apr  5 14:58 .bashrc
# drwx------ 2 ubuntu ubuntu 4096 Apr  5 13:33 .cache
# -rw------- 1 ubuntu ubuntu   37 Apr  6 09:02 .mysql_history
# -rw-r--r-- 1 ubuntu ubuntu  807 Feb 25  2020 .profile
# drwx------ 2 ubuntu ubuntu 4096 Apr  5 13:14 .ssh
# -rw-r--r-- 1 ubuntu ubuntu    0 Apr  5 14:46 .sudo_as_admin_successful
# -rw------- 1 ubuntu ubuntu  865 Apr  5 14:58 .viminfo
# drwxrwxr-x 3 ubuntu ubuntu 4096 Apr  6 09:10 app
```
- 이 경우 내용이 많기 때문에 해당 화면을 지나치는 경우가 많아 주의해야 함
- 즉, 먼저 나오는 파일 내용은 위에 있기 때문에 최종에는 보이지 않는 경우도 있다.
- 'total 40'과 같이 현재 디렉토리 내 파일의 갯수를 알려줌. 파일의 갯수는 하위 디렉토리도 포함하기 때문에 실제 보여지는 내용과는 차이가 있음

- ls -alh을 사용하면 MB, GB와 같이 바이트 단위 용량으로 사람이 읽기 쉬운 형태로 변환하여 출력함
```bash
ls -alh
# 출력 결과:
# total 40K
# drwxr-xr-x 5 ubuntu ubuntu 4.0K Apr  6 09:10 .
# drwxr-xr-x 3 root   root   4.0K Apr  5 13:14 ..
# -rw-r--r-- 1 ubuntu ubuntu  220 Feb 25  2020 .bash_logout
# -rw-r--r-- 1 ubuntu ubuntu 3.8K Apr  5 14:58 .bashrc
# drwx------ 2 ubuntu ubuntu 4.0K Apr  5 13:33 .cache
# -rw------- 1 ubuntu ubuntu   37 Apr  6 09:02 .mysql_history
# -rw-r--r-- 1 ubuntu ubuntu  807 Feb 25  2020 .profile
# drwx------ 2 ubuntu ubuntu 4.0K Apr  5 13:14 .ssh
# -rw-r--r-- 1 ubuntu ubuntu    0 Apr  5 14:46 .sudo_as_admin_successful
# -rw------- 1 ubuntu ubuntu  865 Apr  5 14:58 .viminfo
# drwxrwxr-x 3 ubuntu ubuntu 4.0K Apr  6 09:10 app
```

## ls -al 읽는 방법
```bash
# drwxrwxr-x 3 ubuntu ubuntu 4096 Apr  6 09:10 app
```

1. `drwxr-xr-x`
- 10글자로 이루어지는데, 첫 글자는 d, l, -로 이루어져 있음
	- l: 링크
	- -: 파일
	- d: 디렉토리(폴더)
- 나머지 9자는 파일 및 디렉토리의 퍼미션(권한)으로 어떤 권한이 부여되어 있는지 확인할 수 있음
- 퍼미션은 rwx, rwx, rwx 조합임
	- 퍼미션의 종류: rwx
		- read(읽기 권한)
		- wirte(쓰기 또는 수정 권한)
		- execute(실행 권한)
	- rwxrwxrwx
		- 세 글자씩 묶어 공개, 그룹, 권한으로 나뉨
- 3자리씩 끊어서 분석해보면 d(디렉토리)
	- rwx(소유자가 접근할 수 있는 권한): 소유자는 읽기, 쓰기, 실행이 모두 허용됨
	- r-x(소유 그룹에 속하고 있는 사용자들의 권한): 해당 그룹은 읽기, 실행이 허용됨
	- r-x(모든 사용자들에 대한 권한): 모든 사용자는 소유그룹과 마찬가지로 읽기, 실행이 허용됨

2. 3: 링크 수
3. ubuntu: 해당 파일 또는 디렉토리(여기서는 디렉토리)의 소유자
4. ubuntu: 소유 그룹
5. 4096: 파일의 용량
6. Apr 6 09:10: 생성 날짜
7. app: 파일 또는 디렉토리의 이름