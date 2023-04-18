## MacOS
- MacOS의 터미널에서 ncloud object storage의 Bucket에 CORS 설정을 하는 방법을 알아보자
- 먼저 AWS CLI(Command Line Interface)를 설치해야 함. 아래 명령어를 터미널에 입력해서 AWS CLI를 설치하자
```bash
brew install awscli
```

- AWS CLI를 설치한 후, 아래 명령어를 사용하여 ncloud object storage에 접속함
```bash
aws configure
# AWS Access Key ID [None]: <Access Key ID>
# AWS Secret Access Key [None]: <Secret Access Key>
# Default region name [None]: <Bucket이 위치한 Region>
# Default output format [None]: json 등 입력하면 됨
```
- Access Key ID와 Secret Access Key, Region을 입력함. 이때, Region은 ncloud object storage의 Bucket이 위치한 지역을 입력해야 함(사실 Default 값 두 개는 입력하지 않아도 무방함)
- 만일 리전이 한국이라면 `ap-norteast-2` 로 적으면 됨

- 내가 설정한 정보를 보려면 아래의 명령어를 통해 확인할 수 있음
```bash
aws configure list
```
- 위 명령어를 실행하면 설정한 AWS Access Key ID, Secret Access Key, Default region name, Default output format 정보가 출력됨


```bash
aws --endpoint-url=https://kr.object.ncloudstorage.com s3api put-bucket-cors --bucket bucket_name --cors-configuration '{"CORSRules": [{"AllowedHeaders": ["*"], "AllowedMethods": ["GET"], "AllowedOrigins": ["*"], "MaxAgeSeconds": 3000}]}'
```
- 해당 코드는 AWS CLI(Command Line Interface)를 사용하여 S3 Bucket의 CORS(Cross-Origin Resource Sharing) 설정을 변경하는 코드임
- 위 부분에서 bucket_name 부분에 나의 버킷을 넣으면 됨
- `--cors-configuration` 옵션으로 Bucket에 적용할 CORS 설정을 JSON 형태로 입력합니다. 이 예시에서는 하나의 CORS Rule을 적용하고 있으며, `AllowedHeaders`, `AllowedMethods`, `AllowedOrigins`, `MaxAgeSeconds` 4개의 속성을 가지고 있음
- `AllowedHeaders`: 요청 헤더에서 허용되는 키 값을 지정함. 이 예시에서는 모든 키 값을 허용하도록 `"*"`로 설정하였음
- `AllowedMethods`: CORS를 적용할 HTTP 메서드를 지정함. 이 예시에서는 `GET` 메서드만 허용하도록 지정하였음
- `AllowedOrigins`: 접근을 허용할 도메인 이름을 지정함. 이 예시에서는 모든 도메인에서 접근을 허용하도록 `"*"`로 설정하였음
- `MaxAgeSeconds`: preflight 요청에 대한 캐싱 시간을 지정함. 이 예시에서는 3000초(약 50분) 동안 캐싱하도록 지정하였음

### 엔트포인트에 대하여
- 처음에 설정을 적용할 때, 아래와 같은 오류가 계속 났었음
```bash
# An error occurred (InvalidAccessKeyId) when calling the GetBucketLocation operation: The AWS Access Key Id you provided does not exist in our records.
```
- 분명 Access Key ID를 잘 적었지만 오류가 계속 떠서 무슨 문제일까 열심히 찾던 중, aws의 엔드포인트를 네이버클라우드로 바꿔줘야 한다는 것을 깨닫게 되었음
- aws로 버킷 등에 접근하는 모든 명령어에 endpoint를 잊지 말자


