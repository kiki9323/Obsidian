# useReducer
`useReducer`는 컴포넌트에 **reducer**를 추가할 수 있는 React Hook입니다.

```jsx
const [state, dispatch] = useReducer(reducer, initialArg, init?)
```


---
## Index
* [ ] **1. Reference**
	* `useReducer(reducer, initialArg, init?)`
	* `dispatch` function

* [ ] **2. Usage**
	* 컴포넌트에 reducer 추가하기
	* reducer 함수 작성하기
	* 초기 state 재생성 방지

- [ ] **3. Troubleshooting**
	* action을 dispatch 했지만 logging하면 이전 state가 표시됨.
	* action을 dispatch 했지만 화면이 업데이트 되지 않음.
	* dispatch 하면 reducer state의 일부분이 undefined 됨.
	* disptach 하면 모든 reducer state가 undefined 됨.
	* error "Too many re-renders"
	* reducer 또는 초기화 함수가 두 번 실행 됨.

---
### Reference
#### `useReducer(reducer, initialArg, init?)`
컴포넌트 최상위 레벨에서 `useReducer`를 호출하여 `reducer`을 통해 `state`를 관리한다.  

##### 1. Parameters
> `reducer`

state가 *업데이트되는 방식을 지정*하는 **순수 함수**.    
`state`와 `action`을 인자로 받으며, 리턴값으론 `next state`를 반환한다.    
(state와 action은 어떤 유형이든 가능하다.)  

> `initialArg`

초기 state가 계산되는 값.

> optional `init`

optional `init`은 초기 state 계산 방법을 지정하는 초기화 **함수***   
이것을 지정하지 않으면 초기 `state`는` initialArg`로 설정이 된다. 

##### 2. Returns
> useReducer는 정확히 두 개의 값을 가진 배열을 반환한다: 
1. 현재 state. 첫 번째 렌더링 중에는 `init(initialArg)` 또는 (`init`이 없는 경우) `initialArg`로 설정됨.
2. state를 다른 값으로 업데이트하고 리렌더링을 트리거할 수 있는 `dispatch` 함수

화면에 표시되는 내용을 업데이트하려면 사용자가 수행한 작업을 나타내는 객체, 즉 액션을 사용하여 dispatch를 호출한다.
```jsx
function handleClick() {  
	dispatch({ type: 'incremented_age' });  
}
```

#### `dispatch` function 
`useReducer`가 반환하는 `dispatch`함수를 사용하면 state를 다른 값으로 업데이트 하고 다시 렌더링을 트리거할 수 있다. 
```jsx
const [state, dispatch] = useReducer(reducer, { age: 42 });

function handleClick() {
	dispatch({ type: 'incremented_age' });
	//...
}
```

##### 1. Parameters
> `action`

사용자가 수행한 작업. 어떤 데이터 유형이든 올 수 있다.  
관용적으로 액션은 보통 이를 식별하는 `type` 속성이 있는 객체이며, 선택적으로 추가 정보가 있는 다른 속성을 포함할 수 있다.  

##### 2. Returns
`dispatch` 함수에는 반환값이 없다.  

##### 3. Caveats
* `dispatch` 함수는 **다음 렌더링에 대한 `state`변수만 업데이트** 한다.  
* 만약, `disptach` 함수를 호출한 후 `state` 변수를 읽으면, *호출 전 화면에 있던 이전 값이 계속 표시된다.*  

### Usage
#### 1. Adding a reducer to a component
> 컴포넌트에 reducer 추가하기 
  
```tsx
import { useReducer } from 'react';

// State type
interface State {
  age: number;
  name: string;
}

// Action type
type Action = | { type: 'incremented_age' } | { type: 'changed_name'; nextName: string };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case 'incremented_age':
      return {
        ...state,
        age: state.age + 1,
      };
    case 'changed_name':
      return {
        ...state,
        name: action.nextName,
      };
    default:
      return state;
  }
}

function App() {
  const [state, dispatch] = useReducer(reducer, { name: 'taylor', age: 42 });
  return (
    <>
      <button
        onClick={() => {
          dispatch({ type: 'incremented_age' });
        }}
      >
        increment age
      </button>
      <input
        type="text"
        onChange={(e) => {
          dispatch({
            type: 'changed_name',
            nextName: e.target.value,
          });
        }}
      />
      <p>hello! {state.name || 'you'} are {state.age}</p>
    </>
  );
}
export default App;
```
