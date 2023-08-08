- git을 사용하다 보면 브랜치 전체를 clone 하지 않고 특정 브랜치 하나만을 clone 해야할 때가 있다.
- 특히 브랜치가 많은 경우, 이 방법을 사용할 수 있다.
```bash
git clone -b {branch_name} --single-branch {clone_url}
```

