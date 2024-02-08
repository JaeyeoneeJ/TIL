## 1. 일반적인 React App에서 Redux로 비동기 데이터를 관리하는 법
- 기본적으로 React 앱 개발 환경 설정 시 Redux를 사용하는 것은 상당히 일반화된 케이스이다.
- 내가 진행하는 프로젝트 역시 React App을 베이스로 작업하고 일반적으로 서버와의 API 통신과 비동기 데이터 관리에 Redux를 주로 사용하는 편이다.
- 비동기 데이터를 React Component의 State에 보관하게 될 경우 다수의 Component의 Lifecycle에 따라 비동기 데이터가 관리되므로 캐싱 등 최적화를 수행하기 어렵다. 그리고 다수의 Component에서 동일한 API를 호출하거나, 특정 API 응답이 다른 API에 영향을 미치는 경우(2개의 api 데이터를 불러와 1개의 데이터로 재구조화 하는 등) 등 복잡하지만 빈번하게 요구되는 사용자 시나리오에 대응하기에 꽤 어려움이 있었다.
- 하지만 Redux를 사용하여 비동기 데이터를 관리할 경우, React Component의 Lifecycle과 관계없이 Global State(전역 상태)에서 비동기 데이터가 관리되기 때문에 캐싱과 같은 최적화 작업을 쉽게 수행할 수 있고 복잡한 사용자 시나리오에 대한 대응이 보다 쉬워진다.

## 2. 비동기 데이터를 관리하는 측면에서  Redux 사용 시 불편한 점
- 위와 같은 환경에서 Redux를 사용하여 API 통신을 수행하고 비동기 데이터를 관리하며 얻은 다양한 장점이 존재했지만, 역시 불편한 점도 상당했다.
- React 앱과 같은 컴포넌트 기반 스테이트(component-based state), React Hook, Redux와 같은 Global State Management Library(전역 상태관리 라이브러리) 등을 사용하여 비동기 데이터를 처리해 왔다. 이는 client state를 처리하는데 효과적이었으나 server state를 처리하는데 효과적이지 못하였다.
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
- 
- React Query v4 부터 TanStack Query로 라이브러리 명이 변경되었다. 

<hr>
## ref.
- https://www.npmjs.com/package/react-query
- https://tanstack.com/query/latest
- https://tech.kakaopay.com/post/react-query-1/#react-query%EB%A5%BC-%EC%93%B0%EA%B3%A0-%EC%9D%B4%EB%9F%B0-%EA%B2%8C-%ED%8E%B8%ED%95%B4%EC%A1%8C%EB%8B%A4