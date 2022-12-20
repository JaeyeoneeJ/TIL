# React-router v5 버전

기존 v5 버전에서는 다음과 같이 작성한다.

```javascript
<Switch>
	<Route path={`/${부모url}/one`}>
		<One />
	</Route>
	<Route path={`/${부모url}/two`}>
		<Two />
	</Route>
</Switch>
```

---

# React-router v6 버전

fv6에서 nested routes를 구현하는 방법은 두 가지가 있다.

### 첫 번째
부모 route의 path 마지막에 /*를 적어 명시적으로 이 route의 내부에서 nested route가 render 될 수 있음을 표시하고 자식 route를 부모 route의 element 내부에 작성하는 방법
  

router.tsx에서  
  
```javascript
<Route path="/${부모url}/*" element={<Url/>} />
```
  
Url.tsx에서  
  
```javascript
<Routes>  
	<Route path="one" element={<One />} />  
	<Route path="two" element={<Two />} />  
</ Routes>  
```
  
Routes가 상대경로도 지원하기 때문에 path="one"과 같이 써도 동작한다고 한다. 
  
### 두번째
자식 route를 부모 element의 내부가 아닌 route 내부에 작성하는 방법 

router.tsx에서  
- one와 two 컴포넌트를 import하고
  
```javascript
<Routes> 
	<Route path="/${부모url}" element={<Url />} >  
	<Route path="one" element={<One />} />  
	<Route path="two" element={<Two />} />  
</Routes>  
```
  
그리고 이 자식 Route들이 어디에 render 될지 부모의 element안에 Outlet을 이용해 표시해주면 된다.
  
Url.tsx에서, react-router-dom에서 Outlet을 import하고  
원하는 위치에 <Outlet />를 작성해주면 된다.

---

ref. https://ui.dev/react-router-nested-routes/  