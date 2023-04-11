- spring boot 프로젝트 ./gradlew build가 느려도 너무 느림
- 이전까지는 인스턴스를 껐다가 켜는(중지 - 시작) 방법을 통해 리셋을 해왔음(이렇게 하면 서버가 위치하는 곳이 바뀌어서 속도가 개선될 수 있다고 함)
- 처음 몇번은 잠깐 잘 됐지만 뒤로 갈수록 느려지는 빈도가 잦아져서 재시작을 해도 느려지는 경우가 많았음

<img src="https://user-images.githubusercontent.com/77138259/231032555-38217d82-0fbe-466a-9341-b730239cef16.png" alt="AWS EC2 기본 인스턴스 용량" />
- AWS에서 프리티어로 제공해주는 t2.micro의 경우 메모리가 1GB임
```bash
free
```
- 스왑 파일을 생성하기 전에 메모리 확인
- 실제로 메모리를 확인하면 다음과 같이 나타남
<img src="https://user-images.githubusercontent.com/77138259/231032908-3ad399df-3db3-4456-983b-2a4207a409e3.png" alt="AWS EC2 기본 인스턴스 메모리 체크" />

- 실제로 gradle 멈춤 현상을 검색해보니 생각보다 많은 사람들이 프로세스 중에 서버가 먹통이 된 경우가 있었고, `스왑 파일`을 사용하여 메모리를 할당해주는 방법으로 대부분 해결한 것으로 보였음
```bash
free # 메모리 확인
sudo dd if=/dev/zero of=/swapfile bs=128M count=16 # bs * count 만큼 메모리가 할당됨
sudo chmod 600 /swapfile # 스왑 파일의 읽기 및 쓰기 권한을 업데이트 함
sudo mkswap /swapfile # linux 스왑 영열을 설정함
sudo swapon /swapfile # 스왑 공간에 스왑 파일을 추가하여 즉시 사용할 수 있게 함
sudo swapon -s # 프로시저의 상태를 확인함

# 부팅 시 스왑 파일을 시작하는 설정을 하기 위해 /etc/fstab 파일을 수정함
sudo vim /etc/fstab
```

```vi
/swapfile swap swap defaults 0 0
```
<img src="https://user-images.githubusercontent.com/77138259/231033676-9a0374f2-89b5-432d-974e-dc5aa7472d74.png" alt="vim 에디터 수정" />

<img src="https://user-images.githubusercontent.com/77138259/231033885-12198e05-d0ac-4290-ba2d-095c9f7bd205.png" alt="메모리 증가" />

- ./gradlew build 실행 결과
<img src="https://user-images.githubusercontent.com/77138259/231034453-bdafbe48-481a-473d-9e2e-0bd2b99d29c1.png" alt="./gradlew build 실행 결과" />
- 항상 30분이 넘으면 강제종료를 했는데, 신세계다.
- 빌드 후에 메모리를 확인해봤음
<img src="https://user-images.githubusercontent.com/77138259/231034720-d83f7911-09bf-41bf-8128-122cb5e0f5eb.png" alt="빌드 후 메모리" />
- 평상시 상태를 보면 스왑 메모리를 일부 사용하면서 RAM에 약간 여유가 생겼음
- 스왑 파일로 메모리를 추가 할당한 뒤로는 gradle에서 같은 문제가 아직까지는 반복되지 않았음

- 마지막으로 아래 출처에게 진한 감사를 전합니다. 나의 구세주!

<hr>
### ref
- https://velog.io/@shawnhansh/AWS-EC2-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%8A%A4%EC%99%91