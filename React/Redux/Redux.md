# Redux란?
상태 관리를 도와준다.
전역 상태 관리에 중점을 두며, 애플리케이션의 전체 상태를 중앙 집중식 저장소인 스토어에 저장한다.
그리하여 모든 컴포넌트에 해당 상태에 접근하거나 업데이트 할 수 있다.

## Props vs State

### Props
- [1] `properties`의 줄임말
- [2] 컴포넌트들이 서로 통신하는 방법
- [3] **상위 구성 요소**에서 **하위**로 흐릅니다.
- [4] 데이터의 흐름은 단방향이다. 
- [4] 자식 컴포넌트가 이 값의 변경을 원한다면, 직접적으로 props 값을 변경하는 것이 아니라 부모에게 요청을 해야하며, 이런 요청은 주로 콜백 함수 형태의 prop을 통해 이루어진다.
- [4] 즉, 자식이 부모에게 상태 변경 요청 / 부모는 내부 상태를 변경 후 다시 prop으로 자식에게 전달.

```jsx
<ChatMessages 
	messages={messages}
	currentMember={member}
/>
```

### state
- [1] parent component에서 child component로 data를 보내는 것이 아닌, 현재 component 안에서 데이터를 전달하려면 state를 쓴다.
- [2] state is mutable
- [3] state가 변하면 re-render 된다.

```jsx
state = {
	message: '',
	attatchFile: undefined,
	openMenu: false
}
```

## 이러한 특성 때문에 redux로 state를 관리한다.

### store라는 곳에 중앙 집중 관리한다.
![[Pasted image 20230919004219.png]]

### redux flow

![[Pasted image 20230919011110.png]]


- [1] Action
 - 간단한 js 객체이다.
	- [2] type : 수행하는 작업의 유형을 지정
	- [2] payload : 선택적으로 redux 저장소에 일부 데이터를 보내는 데 사용되는 데이터 
	
	```js
	{ type: 'LIKE_ARTICLE', articleId: 42 }
	{ type: 'FETCH_USER_SUCCESS', response: { id: 3, name: 'Mary' } }
	{ type: 'ADD_TODO', text: 'Read the Redux Docs.' }
	```
	
- [1] Reducer
	- **상태의 변경 사항을 결정**하고 **업데이트된 상태를 반환**하는 **함수**
	- store의 내부 상태를 업데이트한다.
	- 순수함수이다.
	```js
	(previousState, action) => nextState
	```

- [1] Store
	- 하나로 모으는 객체 저장소 = **전체 상태 트리 보유** 하는 저장소
	- 내부 상태를 변경하는 유일한 방법은 **action** 을 전달하는 것
	- 클래스가 아니다. 그저 Methods가 있는 객체일 뿐

```js
import { createStore } from 'redux';

const store = createStore(counter);

const render = () => ReactDOM.render(
	<App 
		value={store.getState()} // getState()
		onIncrement={ () => store.dispatch({ type: 'INCREMENT' })}
		onDecrement={ () => store.dispatch({ type: 'DECREMENT' })}
	/>
) 
render();
store.subscribe(render); // subscribe()
```

- [2] getState()
- [ ] 현재 상태 트리를 반환한다.
- [ ] 스토어의 리듀서가 반환한 마지막 값과 같다.
- [2] subscribe()
- [ ] change listener를 추가합니다.
- [ ] 작업이 전달될 때마다 호출되며 상태 트리의 일부가 잠재적으로 변경되었을 수 있다.

- [2] combineReducers

### 중앙 집중 관리 형태의 state 관리의 장점
#### 1. 예측 가능한 상태 업데이트
	애플리케이션의 모든 상태 변화가 특정한 패턴(액션 생성자와 리듀서)을 따르게 된다.
	=> 코드의 일관성 높이기, 쉬운 디버깅, 코드 동작 에측
#### 2. 상태 추적의 용이성
	전체에서 공유되는 상태를 한 곳에서 관리하면 상태를 추적하고 이해하기 쉬워진다.
#### 3. 상태 변경 로직의 분리
	컴포넌트 로직과 상태 변경 로직을 분리할 수 있다. 
	


## Provider
React Redux 앱의 모든 React 요소는 저장소에 연결할 수 있으므로, 앱 구성 요소 트리가 내부에 있는 최상위 수준에서 Provider 렌더링한다.

## useSelector(), useDispatch()
### : Provider로 둘러 싸인 컴포넌트에서 store에 접근한다.
```jsx
const dispatch = useDispatch(); // dispatch 보낼 때

...

const addTodo = e => {
	e.preventDefault();
	dispatch({ type: "ADD_TODO", text: todoValue });
	setTodoValue("");
};
```

```jsx
// store.getState() {counter: 0, todos: Array(1)}

const counter = useSelector(state => state.counter); // 특정 값 사용할 때
const todos = useSelector(state => state.todos);

...
return (
	<div>
		Clicked: {counter}
		<ul>
			{todos.map((todo, index) => (
				<li key={index}>{todo}</li>
			))}
		</ul>
	</div>
)
```

## 리덕스 미들웨어
액션을 디스패치하고 리듀서에 도달하는 순간 사이에 사전 지정된 작업을 실행할 수 있게 해주는 중간자이다.
- [1] 지원하는 부가적인 기능
* 로깅
* 비동기 처리
* 에러 보고 등 


## 리덕스 Thunk
비동기 작업할 때 많이 사용하는 방법이다.
- [!] thunk? 일부 지연된 작업을 수행하는 코드 조각을 의미함.

	axios 사용하여 비동기 요청을 보냄

## 리덕스 툴킷
리덕스 로직을 작성하기 위한 공식 권장 접근 방식이다.

#### 1. react에 Redux 스토어 제공
저장소가 생성되면 `React-Redux<Provider>`를 배치하여 React 구성요소에서 사용할 수 있도록 한다.

![[Pasted image 20230919230707.png]]


