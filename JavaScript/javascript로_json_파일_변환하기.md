- javascript를 활용하여 origin.json 파일에서 특정 필드를 제외하거나 특정 필드를 추가하여 convert.json을 생성하는 방법을 작성한다.

## 1. 필요한 모듈 설치(node.js 환경 기준)
```bash
npm install fs
```
- fs: 파일 시스템과 상호 작용하기 위한 node.js의 기본 모듈

## 2. javascript 코드 작성
```javascript
// convert.js
const fs = require("fs");

// origin.json 파일 읽기
const data = fs.readFileSync("origin.json");
const jsonData = JSON.parse(data)?.dataList;

// 제외할 필드 이름을 배열에 저장 (예시: id, createdAt)
const excludeFields = ["rejectField"];

// 새로운 객체 생성 및 원하는 필드만 복사
const newData = jsonData.map((item) => {
  const newItem = {};
  for (const key in item) {
    if (!excludeFields.includes(key)) {
      newItem[key] = item[key];
    }
  }
  newItem.newField = [
    { countryId: "KR", value: 6 },
    { countryId: "US", value: 10 },
  ];
  return newItem;
});

// convert.json 파일 생성
fs.writeFileSync("convert.json", JSON.stringify(newData, null, 2));
```
- **파일 읽기:** `fs.readFileSync`를 사용하여 origins.json 파일의 내용을 읽고 JSON 객체로 변환
- **제외할 필드 설정:** `excludeFields` 배열에 제외하고 싶은 필드 이름들을 저장
- 신규 필드 추가: `newItem`에 `newField` 필드를 추가하고 값을 부여
- **새로운 객체 생성:** `jsonData` 배열의 각 요소를 순회하며 새로운 객체를 생성하고, `excludeFields`에 포함되지 않은 필드만 복사, 새로운 객체에 신규 필드를 추가
- **파일 쓰기:** `fs.writeFileSync`를 사용하여 새롭게 생성된 JSON 데이터를 convert.json 파일에 저장

- JSON.stringify는 javascript 객체를 JSON 문자열로 변환하는 함수
	- **newData:** 앞서 생성하거나 수정한 JavaScript 객체를 의미. 이 객체가 JSON 형식으로 변환되어 파일에 저장
	- **null:** 이 자리에는 replacer 함수가 올 수 있는데, 여기서는 사용하지 않으므로 null 값 입력. replacer 함수를 통해 특정 값을 변환하거나 제외할 수 있
	- **2:** JSON 문자열의 들여쓰기를 2칸으로 설정. 가독성을 높이기 위해 사용하며, 생략 시 모든 값이 한 줄로 출력

### 3. 실행
```bash
node convert.js
```
- convert.js, origin.json이 있는 폴더에서 위 값을 실행하면 convert.json이 생성됨