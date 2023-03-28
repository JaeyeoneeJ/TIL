- 키보드 shift 키를 두번 입력하면 나오는 검색창에서 'vm' 을 입력
- '사용자 지정 vm 옵션 편집..' 선택
- idea.vmoptions 파일에서 아래 두 줄 추가
```
-Dfile.encoding=UTF-8  
-Dconsole.encoding=UTF-8
```
- IntelliJ IDEA restart