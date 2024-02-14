## 1. 일반적인 React App에서 Redux로 비동기 데이터를 관리하는 법
- 기본적으로 React 앱 개발 환경 설정 시 Redux를 사용하는 것은 상당히 일반화된 케이스이다.
- 내가 진행하는 프로젝트 역시 React App을 베이스로 작업하고 일반적으로 서버와의 API 통신과 비동기 데이터 관리에 Redux를 주로 사용하는 편이다.
- 비동기 데이터를 React Component의 State에 보관하게 될 경우 다수의 Component의 Lifecycle에 따라 비동기 데이터가 관리되므로 캐싱 등 최적화를 수행하기 어렵다. 그리고 다수의 Component에서 동일한 API를 호출하거나, 특정 API 응답이 다른 API에 영향을 미치는 경우(2개의 api 데이터를 불러와 1개의 데이터로 재구조화 하는 등) 등 복잡하지만 빈번하게 요구되는 사용자 시나리오에 대응하기에 꽤 어려움이 있었다.
- 하지만 Redux를 사용하여 비동기 데이터를 관리할 경우, React Component의 Lifecycle과 관계없이 Global State(전역 상태)에서 비동기 데이터가 관리되기 때문에 캐싱과 같은 최적화 작업을 쉽게 수행할 수 있고 복잡한 사용자 시나리오에 대한 대응이 보다 쉬워진다.

## 2. 비동기 데이터를 관리하는 측면에서  Redux 사용 시 불편한 점

> 아래 내용은 카카오페이에서 작성한 [카카오페이 프론트엔드 개발자들이 React Query를 선택한 이유](https://tech.kakaopay.com/post/react-query-1/#react-query%EB%A5%BC-%EC%93%B0%EA%B3%A0-%EC%9D%B4%EB%9F%B0-%EA%B2%8C-%ED%8E%B8%ED%95%B4%EC%A1%8C%EB%8B%A4) 글을 보고 저의 프로젝트의 상황 등을 적용하여 작성한 글이므로 원문은 위 링크를 참조하시기 바랍니다.

- 위와 같은 환경에서 Redux를 사용하여 API 통신을 수행하고 비동기 데이터를 관리하며 얻은 다양한 장점이 존재했지만, 역시 불편한 점도 상당했다.
- Redux에는 Redux Three Principles(리덕스 기본 원칙)이 존재한다. 이 기본 원칙을 충족하기 위해 Redux를 사용하는데에는 장황한 Boilerplate 코드가 요구된다.
- 이러한 이슈를 해결하기 위해 redux-toolkit이 등장했고, 이후 boilerplate 코드가 많이 줄어들었음에도 불구하고 여전히 비동기로 데이터를 관리할 때에는 여전히 불편한 boilderplate 코드가 필요하고 여전히 반복되어 작성하고 있다.

- 아래는 정말 예전에 작업했던 댓글 기능의 비동기 데이터 동기화 기능을 찾춘 get, update의 예시이다.

```jsx
// src/components/commentsList/CommentsList.jsx
import React, { useEffect, useState } from "react"
import styled from "styled-components"
import { useParams } from "react-router-dom"
import { useDispatch, useSelector } from "react-redux"
import useInput from "../../hooks/useInput"
import { __addComment, __getComments } from "../../redux/modules/commentsSlice"
import Comment from "../comment/Comment"

const CommentsList = ({postId, cookie, setIsOn, isOn}) => {
    const dispatch = useDispatch()
    const {id} = useParams()
    const {comments} = useSelector((state)=> state.comments)
    const realComments = comments.data
    const [postComment, onChangeCommentHandler, resetComment] = useInput();
    
    const onSubmitAddComment = (e) => {
        e.preventDefault();
        const objectComment = {
            comment: postComment
        }
        dispatch(__addComment({objectComment, postId, cookie}))
        resetComment()
        setIsOn(true)
        return window.location.replace(postId)
    }

    useEffect(() => {
        dispatch(__getComments(postId))
    }, [])

    return (
        <CommentsListCtn>
            <Footer>
                <AddCommentForm onSubmit={onSubmitAddComment}>
                    <AddCommentContent
                        type="text"
                        name="comment"
                        value={postComment}
                        onChange={onChangeCommentHandler}
                        placeholder="댓글을 입력해주세요. (100자 이내)"
                        maxLength="100"
                        required
                    />
                    <AddCommentBtn>추가하기</AddCommentBtn>
                </AddCommentForm>
                <CommentsCtn>
                    {realComments?.map((comment)=> (
                        <Comment
                            key={comment.commentId}
                            cookie={cookie}
                            postId={postId}
                            comment={comment}
                        />
                    ))}
                </CommentsCtn>
            </Footer>
        </CommentsListCtn>
    )
}

...

export default CommentsList
```

```js
// src/redux/modules/commentsSlice.js

import { createAsyncThunk, createSlice } from "@reduxjs/toolkit";
import axios from "axios";
import { instance } from "../instance";

const initialState = {
  comments: [],
  isLoading: false,
  isSuccess: false,
  error: null,
};

export const __getComments = createAsyncThunk(
  "comments/getComments",
  async (payload, thunkAPI) => {
    try {
      const data = await instance.get(`/comments/${payload}`);
      return thunkAPI.fulfillWithValue(data.data);
    } catch (error) {
      return thunkAPI.rejectWithValue(error);
    }
  }
);

export const __editComment = createAsyncThunk(
  "comments/editComment",
  async (payload, thunkAPI) => {
    try {
      await axios.patch(`https://shrouded-badlands-79466.herokuapp.com/comments/${payload.id}`, payload, {

      });
      return thunkAPI.fulfillWithValue(payload);
    } catch (error) {
      return thunkAPI.rejectWithValue(error);
    }
  }
);

...

export const commentsSlice = createSlice({
  name: "comments",
  initialState,
  reducers: {
    clearTodo: (state, action) => {
      state.isSuccess = true
    }
  },
  extraReducers: {
    [__getComments.pending]: (state) => {
      state.isLoading = true; // 네트워크 요청이 시작되면 로딩상태를 true로 변경합니다.
    },
    [__getComments.fulfilled]: (state, action) => {
      state.isLoading = false; // 네트워크 요청이 끝났으니, false로 변경합니다.
      state.comments = action.payload; // Store에 있는 comments에 서버에서 가져온 comments를 넣습니다.
    },
    [__getComments.rejected]: (state, action) => {
      state.isLoading = false; // 에러가 발생했지만, 네트워크 요청이 끝났으니, false로 변경합니다.
      state.error = action.payload; // catch 된 error 객체를 state.error에 넣습니다.
    },
    [__editComment.pending]: (state) => {
      state.isLoading = true;
    },
    [__editComment.fulfilled]: (state, action) => {
      state.isLoading = false;
      state.comments = state.comments.map((comment) => {
	      if (comment.id === action.payload.thisID) {
	        return {...comment, comment:action.payload.editComment}
	      } else {
	        return comment
	      }
      });
    },
    [__editComment.rejected]: (state, action) => {
      state.isLoading = false;
      state.error = action.payload;
    },
    ...
  },
});

export const { clearTodo } = commentsSlice.actions;
export default commentsSlice.reducer;
```

- 컴포넌트 구성에 필요한 일부 소스코드만 발췌하였는데도 React 앱의 기능에 비해 코드 분량이 상당하다.(현재는 단순히 get, update만 표시했는데도)
- redux-toolkit을 사용했음에도 불구하고 장황한 boilerplate 코드가 남아있으며, 하나의 API 요청을 처리하기 위해 여러개의 Action과 Reducer가 필요하여 전체 코드가 눈에 잘 들어오지 않는 것 같다. 
- 또한 비동기 Action을 처리하기 위해 createAsyncThunk와 extraReducers를 사용한 파트도 수행 역할에 비해 분량이 너무 장황하게 느껴진다.
- 만일 API의 개수가 많아고 복잡한 처리를 요하는 경우가 생긴다면 위와 같은 코드의 분량이 많이 늘어날테고, 비동기 Action을 처리하기 위한 복잡성이 높아지게 될 우려가 있다.

- 당연한 얘기이지만 Redux는 API 통신 및 비동기 상태 관리를 위한 라이브러리가 아니다. 데이터를 관리하려면 관련 코드를 개발자가 모두 결정하고 구현해야 한다.
- API 관련 상태 저장 방법이 가장 대표적인 예이다. 나의 경우, API 로딩 여부를 `isLoading`이라는 state 값으로 단순히 `Boolean` 만으로 사용하였지만, 누군가는 `IDLE | LOADING | SUCCESS | ERROR` 등 상태를 더 세분화하여 관리하는 경우도 생길 것이다.
- 팀의 구성원이 많아지고 협업 관계가 복잡하게 구성될수록 내부적인 구현을 모두 개발자가 알아서 하다보니 상황에 따라 데이터를 관리하는 방식과 방법이 달라질 수 밖에 없다.
- 만일, API 상태를 관리하기 위한 규격화된 방식이 있다면 보다 효율적으로 협업하고 관리해나갈 수 있을 것이다.

## 2. React Query란
- React 앱과 같은 컴포넌트 기반 스테이트(component-based state), React Hook, Redux와 같은 Global State Management Library(전역 상태관리 라이브러리) 등을 사용하여 비동기 데이터를 처리해 왔다. 위의 예시와 같이 Redux와 같은 대부분의 기존 상태관리 라이브러리는 client state 작업에는 적합하지만 비동기 또는 server state 작업에는 그다지 적합하지 않다. 이는 server state가 전혀 다르기 때문이다.

- server state
	- 내가 통제하거나 소유하지 않는 위치에 원격으로 지속됨
	- 가져오기 및 업데이트를 위한 비동기 API가 필요함
	- 공유 소유권을 암시하며 본인도 모르게 다른 사람이 변경할 수 있음
	- 주의하지 않으면 애플리케이션이 "구식"이 될 수 있음

- 애플리케이션에서 서버 상태의 특성을 파악하면 진행하면서 더 많은 문제가 발생하게 된다.
	- 캐싱
	- 동일한 데이터에 대한 여러 요청을 단일 요청으로 중복 제거
	- 백그라운드에서 "오래된" 데이터 업데이트
	- 데이터가 "오래된" 시기 알기
	- 최대한 빠르게 데이터 업데이트 반영
	- 페이지 매김 및 지연 로딩 데이터와 같은 성능 최적화
	- 서버 상태의 메모리 및 가비지 수집 관리
	- 구조적 공유를 통해 쿼리 결과 메모

- React Query는 서버 상태 관리를 위한 최고의 라이브러리 중 하나이다. React Query를 사용하면 서버 상태의 까다로운 과제와 장애물을 극복하고 앱 데이터가 사용자를 제어하기 전에 제어할 수 있다.
- React Query는 보다 기술적인 측면에서 다음과 같은 기능을 수행할 수 있다.
	- 애플리케이에서 복잡하고 잘못 이해된 여러 줄의 코드를 제거하고 몇 줄의 React Query 로직으로 대체할 수 있도록 도와줌
	- 새로운 서버 상태 데이터 소스 연결에 대한 걱정 없이 애플리케이션을 더욱 유지 관리하기 쉽게 만들고 새로운 기능을 쉽게 구축할 수 있음
	- 애플리케이션이 그 어느 때보다 더 빠르고 반응성이 뛰어나다는 느낌을 주어 최종 사용자에게 직접적인 영향을 미침
	- 잠재적으로 대역폭을 절약하고 메모리 성능을 높이는데 도움이 됨


## 3. 설치
### 1) v4 미만
- React Query v2, v3은 React v16.8+와 호환되며 ReactDOM 및 React Native와 함께 작동한다.
- 자세한 내용은 [React Query v3 가이드 문서](https://tanstack.com/query/v3/docs/framework/react/installation) 참조
```bash
npm i react-query
# or
yarn add react-query
```

### 2) v4
- React Query v4 부터 TanStack Query로 라이브러리 명이 변경되었다. 이제 React만 지원하는 것이 아니라 `Solid, Vue, Svelte, Angular`도 지원하기 때문이다.
- TanStack Query v4는 React v16.8+와 호환되며 ReactDOM 및 React Native와 함께 작동한다.
```bash
npm i @tanstack/react-query
# or
pnpm add @tanstack/react-query
# or
yarn add @tanstack/react-query
```

- TanStack Query v4는 다음 브라우저 구성과 호환된다.
```bash
Chrome >= 73
Firefox >= 78
Edge >= 79
Safari >= 12.1
iOS >= 12.2
Opera >= 53
```
### 2) v5
- TanStack Query v5는 React v18+와 호환되며 ReactDOM 및 React Native와 함께 작동한다.
```bash
npm i @tanstack/react-query
# or
pnpm add @tanstack/react-query
# or
yarn add @tanstack/react-query
```

TanStack Query v5는 다음 브라우저 구성과 호환된다.
```bash
Chrome >= 91
Firefox >= 90
Edge >= 91
Safari >= 15
iOS >= 15
Opera >= 77
```

> 환경에 따라 폴리필을 추가해야 할 수도 있다.
> 이전 브라우저를 지원하려면 라이브러리를 `node_modules`직접 트랜스파일해야 한다.

- 코딩하는 동안 버그와 불일치를 잡는 데 도움이 되도록 ESLint 플러그인 쿼리를 사용하는 것이 좋다.
```bash
$ npm i -D @tanstack/eslint-plugin-query
# or
$ pnpm add -D @tanstack/eslint-plugin-query
# or
$ yarn add -D @tanstack/eslint-plugin-query
```


## 4. React App에서 사용
- React App에서 React Query를 사용한다면 3가지 핵심 개념을 알아두어야 한다.

### 1) Query
- Query는 고유 키에 연결된 비동기 데이터 소스에 대한 선언적 종속성이다. 서버에서 데이터를 가져오기 위해 Promise 기반 메서드(GET 및 POST 메서드 포함)와 함께 쿼리를 사용할 수 있다.
- 만일 서버의 데이터를 수정하는 경우에는 Query 대신 Mutations 를 사용하는 것이 좋다.
- Component 또는 custom hooks에서 쿼리를 구독하려면 최소한 아래 2개의 조건을 만족하여 `useQuery`를 사용하여 hook을 호출해야 한다.
	- 쿼리의 고유 키
	- Promise(Resolves the data, or Throws an error)를 반환하는 함수
```jsx
import { useQuery } from '@tanstack/react-query'

function App() {
  const info = useQuery({ queryKey: ['todos'], queryFn: fetchTodoList })
}
```
- 작성한 고유 키(unique key)는 앱 전체에서 쿼리를 다시 가져오고, 캐싱하고, 고유하는데 내부적으로 사용된다.
- `useQuery`에서 반환된 쿼리 결과에는 템플릿 작성 및 기타 데이터 사용에 필요한 쿼리에 대한 모든 정보가 포함되어 있다.


## 5. 실 사용 예시
### 1) api 준비
- call할 api를 본인 상황에 맞게 준비하면 된다. 나의 경우, 여러 api를 추적하기 위해 공공데이터 기상청 Open-API를 사용하여 테스트했다.
<img src="https://github.com/JaeyeoneeJ/TIL/assets/77138259/4d4aedcb-5389-4006-85b1-e1a0fec7527a" alt="기상청 Open-api" />
> [기상청 공공데이터 Open-API](https://data.kma.go.kr/api/selectApiDetail.do?pgmNo=42&openApiNo=421)

- 위 링크를 클릭하여 Open-API 활용 신청을 하고 첨부문서를 통해 진행하면 된다.

- React App에서 테스트하기 위해 CRA로 리액트 앱을 설치하고 필요한 라이브러리를 설치한다. 이번 예시에서는 `react-query`와 `axios`를 활용했다.

> 상세한 예시는 [react-query 테스트용 repo](https://github.com/JaeyeoneeJ/react-query-test)를 참고

### 2) app.js
- 먼저 app.js를 `QueryClientProvider`로 감싼 후 `QueryClient`를 연결한다.
- 간단하게 버튼을 만들어 각 버튼을 클릭할 때 마다 각기 다른 api를 호출할 준비를 한다. 
```jsx
// App.js

import React, { useCallback, useState } from "react";
import "./App.css";
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";
import { tabButtons } from "./utils/constants";
import UltraSrcNcst from "./components/UltraSrcNcst";
import UltraSrcFcst from "./components/UltraSrtFcst";
import VilageFcst from "./components/VilageFcst";
import All from "./components/All";

function App() {
  const queryClient = new QueryClient();
  const [selectedTabIndex, setSelectedTabIndex] = useState("getUltraSrtNcst");

  const renderComponent = useCallback(() => {
    switch (selectedTabIndex) {
      case "getUltraSrtNcst":
        return <UltraSrcNcst />;
      case "getUltraSrtFcst":
        return <UltraSrcFcst />;
      case "getVilageFcst":
        return <VilageFcst />;
      case "getAll":
        return <All />;
      default:
        return <div>ERROR</div>;
    }
  }, [selectedTabIndex]);

  return (
    <QueryClientProvider client={queryClient}>
      <h1 className={"title"}>기상청 OPEN API</h1>
      <div>
        {Object.keys(tabButtons).map((tabName, index) => (
          <button
            key={index}
            className={"tabButton"}
            onClick={() => setSelectedTabIndex(tabName)}
          >
            {tabButtons[tabName]}
          </button>
        ))}
      </div>
      {renderComponent()}
    </QueryClientProvider>
  );
}

export default App;
```
- 버튼 중에 "ALL" 버튼은 추후에 useQueries를 사용하여 api를 동시에 호출하고 관리하는 형태를 위해 만들었으니 우선 무시하고 진행한다.

- 아래처럼 버튼이 생겨있을 것이다.
<img src="https://github.com/JaeyeoneeJ/TIL/assets/77138259/dd1c88de-747e-4023-893f-75b68a56c9fc" alt="app.js" />

### 3) axios instance 생성
- 단순한 테스트이기 때문에 axios instance를 굳이 만들 필요는 없지만 나의 경우에는 동일한 api를 호출하는데 URL을 중복으로 입력하는 것을 방지하고자 instance를 생성했다.
```js
// api/instance.js

import axios from "axios";

const apiKey = process.env.REACT_APP_OPEN_API_KEY;

// Axios 인스턴스 생성
export const weatherInstance = axios.create({
  baseURL: process.env.REACT_APP_WEATHER_API_URL,
  params: {
    serviceKey: apiKey,
  },
});
```

- instance를 만드는 김에 동일하게 넣게 되는 serviceKey도 넣었다.

### 4) api.js
- 각 api를 호출하기 위해 별도로 api.js를 생성했다.
```js
// api/api.js

import { getCurrentTime, getFormattedDate } from "../utils/utils";
import { weatherInstance } from "./instance";

export const getUltraSrtNcst = async () => {
  const res = await weatherInstance.get(`/getUltraSrtNcst`, {
    params: {
      numOfRows: "10",
      pageNo: "1",
      dataType: "JSON",
      base_date: getFormattedDate(),
      base_time: getCurrentTime(),
      nx: "55",
      ny: "127",
    },
  });
  
  if (res.data?.response?.header?.resultCode === "00") {
    return res.data?.response?.body?.items?.item;
  } else {
    return [];
  }
};

export const getUltraSrtFcst = async () => {
  const res = await weatherInstance.get(`/getUltraSrtFcst`, {
    params: {
      numOfRows: "60",
      pageNo: "1",
      dataType: "JSON",
      base_date: getFormattedDate(),
      base_time: "0600",
      nx: "55",
      ny: "127",
    },
  });

  const sortRes = [...res.data?.response?.body?.items?.item]?.sort(
    (a, b) => Number(a.fcstTime) - Number(b.fcstTime)
  );

  if (res.data?.response?.header?.resultCode === "00") {
    return sortRes;
  } else {
    return [];
  }
};

export const getVilageFcst = async () => {
  const res = await weatherInstance.get(`/getVilageFcst`, {
    params: {
      numOfRows: "60",
      pageNo: "1",
      dataType: "JSON",
      base_date: getFormattedDate(),
      base_time: "0200",
      nx: "55",
      ny: "127",
    },
  });

  const sortRes = [...res.data?.response?.body?.items?.item]?.sort(
    (a, b) => Number(a.fcstTime) - Number(b.fcstTime)
  );

  if (res.data?.response?.header?.resultCode === "00") {
    return sortRes;
  } else {
    return [];
  }
};
```
- `getFormattedDate()`, `getCurrentTime()`는 `Date()`함수로 현재 날짜와 시간을 api 요청 포멧에 맞게 변환하기 위한 함수이다.
- 각 api의 resultCode가 "00"이 아닌 경우에는 정상적인 데이터가 오지 않으므로 예외처리하였다.
- 중간에 `sort` 메서드를 사용한 부분은 일기예보가 1시간 단위로 많은 데이터를 가져오기 때문에 이를 1시간 단위로 구분할 수 있게 하기 위함이다.(기본 호출 시 category 기준으로 데이터가 쌓이기 때문)

### 5) UltraSrcNcst.jsx
- 여러 api 중 "초단기실황조회"만 예시로 보겠다. 아래는 컴포넌트의 원본 코드이다.
```jsx
// components/UltraSrcNcst.jsx

import React from "react";
import { useQuery } from "@tanstack/react-query";
import { getUltraSrtNcst } from "../api/api";
import { category, getCategoryValue } from "../utils/constants";

const UltraSrcNcst = () => {
  const { data, isPending, error } = useQuery({
    queryKey: ["ultraSrcNcst"],
    queryFn: getUltraSrtNcst,
  });

  if (isPending) return "Loading...";
  if (error) return "error: " + error.message;
  return (
    <div>
      {data?.[0]?.baseDate ? (
        <>
          <h3>today: {data?.[0]?.baseDate}</h3>
          {data.map((item, index) => (
            <div key={index}>
              - {category[item.category].title}:{" "}
              {getCategoryValue(item.category, item.obsrValue)}
            </div>
          ))}
        </>
      ) : (
        "NO DATA"
      )}
    </div>
  );
};

export default UltraSrcNcst;
```
- 하나씩 살펴보자.

```jsx
const { data, isPending, error } = useQuery({
  queryKey: ["ultraSrcNcst"],
  queryFn: getUltraSrtNcst,
});
```
- useQuery로 앞에서 작성한 api를 불러온다. 이때, queryKey는 고유한 값이어야 하며, queryFn은 확실히 데이터의 결과 값을 가지고 있는 비동기 함수여야 한다.
- 구조 분해 할당으로 `data`, `isPending`, `error`를 가져온다.

```jsx
if (isPending) return "Loading...";
if (error) return "error: " + error.message;
return (
  ...
)
```
- 만일 `isPending`이 true일 경우, 비동기 데이터의 통신이 끝나지 않았으므로 앱에 "Loading..." 이라는 문구를 띄워준다.
- 만일 api 호출 간 에러가 발생하는 경우(return 값이 없거나 api를 호출한 비동기 함수 내에서 에러가 발생하는 경우), 에러 메세지를 띄워준다.
- 모두 정상이라면 `return ( ... )` 을 호출한다. 내부 코드는 불러온 정보를 잘 보여주기 위한 내용이므로 설명하지 않겠다.

<img src="https://github.com/JaeyeoneeJ/TIL/assets/77138259/1243ca2c-899a-4b5c-8c4d-ecbc6498a304" alt="UltraSrcNcst.js" />
- 위와 같은 결과가 나타난다.

### 7) useQueries 사용
- 만일 한번에 여러 api를 동시 호출하고 모든 api가 있어야 다음 동작을 할 수 있는 경우에는 위의 `useQuery` 사용이 마치 보일러플레이트 처럼 쓸데없이 길고 복잡하다고 느낄 수 있을 것이다.
- react-query 라이브러리는 이런 경우를 대비하여 `useQueries`를 제공한다.

```jsx
// components/All.jsx

import React from "react";
import { useQueries } from "@tanstack/react-query";
import { getUltraSrtFcst, getUltraSrtNcst, getVilageFcst } from "../api/api";

const All = () => {
  const results = useQueries({
    queries: [
      { queryKey: ["ultraSrcFcst"], queryFn: getUltraSrtFcst },
      { queryKey: ["ultraSrcNcst"], queryFn: getUltraSrtNcst },
      { queryKey: ["VilageFcst"], queryFn: getVilageFcst },
    ],
    combine: (results) => {
      return {
        data: results.map((result) => result.data),
        pending: results.some((result) => result.isPending),
      };
    },
  });

  console.log("results", results);
  if (results.pending) {
    return <div>Not yet...</div>;
  } else {
    return <div>All Data</div>;
  }
};

export default All;

```

- 나의 경우에는 3개의 api를 동시에 호출하고, api 결과가 모두 도착해야 pending을 false로 만들었다.
<img src="https://github.com/JaeyeoneeJ/TIL/assets/77138259/bb0654f0-898e-4298-a21b-40e77f56e544" alt="console.log" />
- 위처럼 3개의 데이터가 모두 도착해야 `pending: false`가 되는 것을 알 수 있다.

### 8) useQueries에 key 값 부여
- 위처럼 사용해도 되지만 나의 경우에는 같은 api를 특정 id만 바꿔 호출한 것이 아닌, 각기 다른 api를 호출한 케이스로 data가 Array타입이면 어떤 api를 호출한 값이 몇 번째 배열에 들어있는지 헷갈리는 경우도 종종 발생한다.
- 따라서 위의 값을 보다 식별 가능하도록 수정해보자.

- 우선 `useQueries` 훅에서는 결과 객체(`result`)에 `queryKey` 라는 속성이 존재하지 않는다.
<img src="https://github.com/JaeyeoneeJ/react-query-test/assets/77138259/5bb7323e-1415-4189-9ec1-8606e1a5866f" alt="console.log" />
- 따라서 일반적인 경우에는 배열로 받아오는 형태를 많이 사용하게 될 것이다.(동일 API를 호출하는데 params 중 id 값 정도만 달라 순서대로 식별이 가능한 경우 등)
- 하지만 우리는 명확한 키 값을 가지고 식별하고자 하기 때문에 `queryKey`를 식별자로 두겠다.

>여기서 한 가지 걱정이 드는 점은 배열을 불러올 때, api 데이터가 도착하는 순서대로 쌓일 수 도 있지 않을까라는 불안감이 들 수 있다.
>하지만 위의 콘솔에서 배열이 도달한 것을 보면 총 3개의 데이터 중 2번 째, 3번 째, 1번 째 순으로 도착하는 것을 확인할 수 있다.
>즉, 고유한 `queryKey`로 호출한 순간, 결과 값을 받을 메모리를 순차적으로 미리 할당했다는 결론이 나오므로 데이터가 꼬일 일은 없다.

- 아래는 컴포넌트를 수정한 내용이다.
```jsx
// components/All.jsx

import React from "react";
import { useQueries } from "@tanstack/react-query";
import { getUltraSrtFcst, getUltraSrtNcst, getVilageFcst } from "../api/api";

const All = () => {
  const queries = [
    { queryKey: ["ultraSrcNcst"], queryFn: getUltraSrtNcst },
    { queryKey: ["ultraSrcFcst"], queryFn: getUltraSrtFcst },
    { queryKey: ["VilageFcst"], queryFn: getVilageFcst },
  ];
  const results = useQueries({
    queries,
    combine: (queryResults) => {
      const combinedData = {};
      queryResults.forEach((result, index) => {
        const queryKey = queries[index].queryKey.join("_");
        combinedData[queryKey] = result.data;
      });
      return {
        data: combinedData,
        pending: queryResults.some((result) => result.isPending),
      };
    },
  });

  console.log("results: ", results);

  if (results.pending) {
    return <div>Not yet...</div>;
  } else {
    return <div>All Data</div>;
  }
};

export default All;
```
- 하나씩 살펴보자.

```jsx
const queries = [
  { queryKey: ["ultraSrcNcst"], queryFn: getUltraSrtNcst },
  { queryKey: ["ultraSrcFcst"], queryFn: getUltraSrtFcst },
  { queryKey: ["VilageFcst"], queryFn: getVilageFcst },
];
```
- 위에서 확인했듯이 `useQueries`의 결과 객체에는 `queryKey`라는 속성이 존재하지 않았다.
- 따라서 외부에서 `queryKey` 값을 부여하기 위해 `queries`를 밖에서 생성하여 연결해주는 방식을 택했다.

```jsx
  const results = useQueries({
    queries,
    combine: (queryResults) => {
      const combinedData = {};
      queryResults.forEach((result, index) => {
        const queryKey = queries[index].queryKey.join("_");
        combinedData[queryKey] = result.data;
      });
      return {
        data: combinedData,
        pending: queryResults.some((result) => result.isPending),
      };
    },
  });
```
- 위에서 선언한 `queries`를 연결해주고, 기존에 3개의 단순 array로 들어갔던 data에 `combinedData`라는 새로운 객체를 연결하고 `combinedData`에 키 값으로 `queries`의 `queryKey`를 연결해준다.

<img src="https://github.com/JaeyeoneeJ/react-query-test/assets/77138259/8647ebfe-2578-4294-97e8-f23a781b793e" alt="console.log" />
- 결과 값을 보면 도착한 순서대로 객체에 쌓이는 것을 확인할 수 있다.
- 모든 결과가 도착하자 `pending: false`가 되었다.

<hr>
## ref.
- https://tanstack.com/query/v5/docs