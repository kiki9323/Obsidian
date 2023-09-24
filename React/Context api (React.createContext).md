#contextapi #Context #createContext #Provider #Consumer

# Context API: createContext

- [1] 리액트 내장 기능
- [2] 단계마다 일일이 props 넘기지 않고도, 컴포넌트 트리 전체에 데이터를 제공할 수 있다.
- [3] 주로 전역 상태를 관리할 때 많이 사용한다.  
- [!] 선호 로케일, UI 테마 등


---
## Index
* [ ] **1. Reference**
	* `createContext(defaultValue)` // 컨텍스트 생성
	* `SomeContext.Provider` // 컨텍스트 값 지정
	* `useContext(SomeContext)` 혹은 `SomeContext.Consumer` // 컨텍스트 값 읽기
* [ ] **2. Usage**
	* creating context

---
#### flow summary
일반적으로 상위 컴포넌트에서 `SomeContext.Provider` 를 사용해 컨텍스트 값을 **지정**하고,
하위 컴포넌트에서 `useContext(SomeContext)`를 호출해 컨텍스트 값을 **읽는다**.
- SomeContext.Consumer 또한 컨텍스트 값을 *읽는 또다른 방법*이다. (거의 쓰지 않는다.)

`createContext`를 사용하면 컴포넌트가 제공하거나 읽을 수 있는 컨텍스트를 만들 수 있다.
```jsx
const SomeContext = createContext(defaultValue);
```

---

## 1. Reference
### 1. [생성] createContext(defaultValue)
- [1] usage | 컴포넌트 외부에서 createContext를 호출하여 컨텍스트를 생성.
```jsx
import { createContext } from 'react';

export const ThemeContext = createContext('light');
export const AuthContext = createContext(null);
```

#### 파라미터 (매개변수)
`defaultValue`
	* 컨텍스트를 읽는 컴포넌트 상위 트리에 일치하는 컨텍스트 provider가 없을 때 이 컨텍스트가 갖을 값이다. 
	* 의미 있는 기본 값이 없다면 `null` 지정.

#### 반환값
* `createContext()`는 **컨텍스트 객체**를 반환한다.
* **어떠한 정보도 보유하지 않는다.** 
* 다른 컴포넌트가 읽거나 제공할 수 있는 컨텍스트를 나타낸다.


### 2. [값 지정] SomeContext.Provider
- [1] 컴포넌트를 컨텍스트 provider로 감싸 내부의 모든 컴포넌트에 대한 이 컨텍스트의 값을 **지정**한다.
```jsx
function App() {
	const [theme, setTheme] = useState('light');
	// ...
	return (
		<ThemeContext.Provider value={theme}>
			<Page />
		</ThemeContext.Provider>
	)
}
```

#### Props
`value`
	* provider 내에서 이 컨텍스트를 읽는 모든 컴포넌트에 전달하려는 값이다.
	* 깊이에 상관없이 전달할 수 있다. 
	* provider 내부에서 useContext(SomeContext)를 호출하는 컴포넌트는 그 위에 있는 가장 가까운 컨텍스트 provider의 value를 받게된다.



### 3-1. [값 읽기 - lagacy] SomeContext.Consumer
`useContext` 이전에 컨텍스트를 읽는 오래된 방법이 있다.
```jsx
function Button() {
	// 👎 Legacy way (not recmmended)
	return (
		<ThemeContext.Consumer>
			{theme => (
				<button className={theme}/>
			)}
		</ThemeContext.Consumer>
	)
}
```

### 3-2. [값 읽기 - Recommended]  useContext()
- [I] https://react-ko.dev/reference/react/useContext
- [1] usage | createContext는 컨텍스트 객체를 반환한다.  useContext에 컨텍스트를 전달하면 컨텍스트를 읽을 수 있다.
```jsx
function Button() {
	// ✅ Recommended way
	const theme = useContext(ThemeContext);
	// ...
}

function Profile() {
	const currentUser = useContext(AuthContext);
	// ...
}
```




## 2. Usage
### 1. Creating context | 컨텍스트 생성하기
컨텍스트는 컴포넌트가 프로퍼티를 명시적으로 전달하지 않고도 **정보를 깊숙이 전달할 수 있게** 해준다.
- [I] https://react-ko.dev/learn/passing-data-deeply-with-context

하나 이상의 컨텍스트를 생성하려면 컴포넌트 외부에서 `createContext` 호출한다.
```jsx
import { createContext } from 'react';

const ThemeContext = createContext('light');
const AuthContext = createContext(null);
```
`createContext`는 컨텍스트 객체를 반환한다. 

컴포넌트는 생성한 컨텍스트를 `useContext`에 전달하여 컨텍스트를 읽을 수 있다.
```jsx
function Button() {
	const theme = useContext(ThemeContext);
}
```
기본적으로 수신되는 값은 컨텍스트를 만들 때 지정한 기본값이 된다. 
그러나 기본값은 절대 변경되지 않으므로 그 자체로는 유용하지 않다.

컨텍스트가 유용한 이유는 **컴포넌트에 다른 동적 값을 제공할 수 있기 때문**이다.
```jsx
function App() {
	const [theme, setTheme] = useState('dark');
	const [currentUser, setCurrentUser] = useState({ name: 'kiki' });
	
	// ...
	
	return (
		<ThemeContext.Provier value={theme}>
			<AuthContext.Provier value={currentUser}>
				<Page />
			</AuthContext.Provier>
		</ThemeContext.Provier>
	)
}
```
깊이에 상관없이 전달된 컨텍스트 값을 '바라보게' 됩니다.
만약 전달된 컨텍스트 값이 변경되면, React 컨텍스트를 읽는 컴포넌트도 다시 렌더링 된다.



---

##### Recap
1. `export const MyContext = createContext(defaultValue)` 컨텍스트를 생성한다. 파일은 따로 `context`폴더 하나 만들어서 생성하여 export 한다.
2. state값을 변경할 수 있는 상위 컴포넌트에서 생성한 컨텍스트를 `<SomeContext.Provider value={...}>` 로 감싸준다.
3. 굳이 프롭스로 드릴링 할 필요 없으니, 필요한 곳(하위 컴포넌트)에서 `useContext(MyContext)` hook에 MyContext를 전달하여 원하는 대로 모든 하위 컴포넌트에서 읽을 수 있게 사용하면 된다.
=> 위에 위치한 컨텍스트를 파악하게 되기 때문.

##### context? 마치 css의 property inheritance(속성 상속) 같다!
재정의 하지 않는 이상 특정 DOM안에 있는 모든 DOM 노드는 아무리 깊어도 css 속성을 상속 받는다.
마찬가지로 react에서 위에서 오는 context를 재정의하는 유일한 방법은 children을 다른 값으로 context provider로 감싸주는 것.
