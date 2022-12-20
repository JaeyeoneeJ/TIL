우리의 작업에는 input form이 한 개만 존재하지는 않을 것이다.

가령 누군가 계정을 생성하거나 회원가입을 하고 싶다고 생각해보자.
정말 간단한 정보만 들어가도 ID와 PW, PW confirm만을 넣어도 벌써 3개의 input form이 사용된다.
일반적으로 이메일, 이름, 성, 비밀번호, 비밀번호 확인, 사용자 이름... 등등 엄청 많은 항목을 가지게 될 것이다.

일반적인 방법으로 구현을 하게 된다면 우리는 하나의 컴포넌트에 엄청나게 많은 state들을 등록하게 될 것이다.

심지어 이보다 큰 규모의 앱을 만든다면 form validation(검증)이 필요할 것이다.
유저는 우리의 뜻대로 값을 입력하지 않을 가능성이 높기 때문이다.
즉, 데이터의 타입을 체크하고, 조건을 확인할 필요가 있다는 뜻이다.
우리는 조건을 확인할 state가 따로 필요하고, 유저에게 검증 state가 작동하고 있다는 것을 시각적으로 보여주기 위해 별도의 코딩을 진행할 것이다.

이런 작업이 여러개의 input에서 반복해서 일어난다면 그나마 다행일지도 모른다.
input 마다 별도의 유효성 검증이 발생할 필요가 있다면 코드의 가독성도, 향후 유지보수도 불편하지 않을까?


# 설치
---
```bash
npm install react-hook-form

# or

yarn add react-hook-form
```


# 적용
---

```javascript
import { useForm } from "react-hook-form";

const App = () => {
	const { register, watch } = useForm();
	console.log(watch())

	return (
		<div>
			<form>
				<input {...register("toDo")} />
				<button>Add</button>
			</form>
		</div>
	);
};
```



# 비교
---
### 기존 작성 방법

```javascript
// 기존 작성 방법
const ToDoList = () => {
	const [toDo, setToDo] = useState("");
	const [toDoError, setToDoError] = useState("");
	const onChange = (event: React.FormEvent<HTMLInputElement>) =>
	{
		const {
		currentTarget: { value },
		} = event;
		setToDoError("");
		setToDo(value);
	};
	const onSubmit = (event: React.FormEvent<HTMLFormElement>) => {
		event.preventDefault();
		if (toDo.length < 10) {
			return setToDoError("To do should be longer");
		}
		console.log(toDo);
	};
	
	return (
		<div>
			<form onSubmit={onSubmit}>
				<input
					onChange={onChange}
					value={toDo}
					type="text"
					placeholder="Write a to do"
				/>
				<button>Add</button>
				{toDoError !== "" ? toDoError : null}
			</form>
		</div>
	);
};
export default ToDoList;
```


### react-hook-form 적용

```javascript
// react-hook-form 적용
import { useForm } from "react-hook-form";

const ToDoList = () => {
	const { register, watch } = useForm();
	console.log(watch())
//	console.log(watch("email"))

	return (
		<div>
			<form>
				<input {...register("email")} placeholder="Email" />
				<input {...register("firstName")} placeholder="First Name" />
				<input {...register("lastName")} placeholder="Last Name" />
				<input {...register("username")} placeholder="Username" />
				<input {...register("password")} placeholder="Password" />
				<input {...register("password1")} placeholder="Password1" />
				<button>Add</button>
			</form>
		</div>
	);
};
```

registor를 쓰는 것만으로도 우리는 onChange 이벤트 함수를 만들고, input에 props를 줄 수 있다.

또한 watch를 사용함으로써, 우리는 form의 입력 값을 추적할 수 있다.