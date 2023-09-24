#useContext 

# useContext
컴포넌트에서 context를 읽고 구독할 수 있게 해주는 React Hook

```jsx
const value = useContext(SomeContext);
```

## Reference
### **Caveats**
1. `<Context.Provider>` 는 반드시 `useContext()` 호출을 수행하는 컴포넌트 **위**에 있어야 한다.

### Usage
#### 1. Passing data deeply into the tree
> 트리 깊숙이 데이터 전달하기 

컴포넌트 최상위 레벨에서 useContext 호출하여 context 읽고 구독한다.
```jsx
import { useContext } from 'react';
function Button() {
	const theme = useContext(ThemeContext); // 최상위에서 호출한다.
}
```

Button에 context를 전달하려면 해당 버튼 또는 상위 컴포넌트 중 하나를 해당 context provider로 감싼다.
```jsx
function MyPage() {
	return (
		<ThemeContext.Provider value="dark">
			<Form /> // 대략 Form 컴포넌트 내부에 Button이 들어있음.
		</ThemeContext.Provider>
	)
}
```
`Provider`와 `Button` 사이에 얼마나 많은 컴포넌트 레이어가 있는지는 중요하지 않고,
`useContext(ThemeContext)`를 호출하면 기본값인 "dark"를 값으로 받는다.

- [!] `useContext()`는 항상 이것을 호출하는 컴포넌트 **위**의 가장 가까운 provider를 찾는다. 
      `useContext()`를 호출하는 컴포넌트 내의 Provider는 고려하지 않는다.

#### 2. Updating data passed via context
> context를 통해 전달된 데이터 업데이트하기

`context`가 변경되기를 원하는 경우, 즉 `context`를 업데이트하려면 `state`와 결합해야 한다.

간단한 예시1) js mapping
```jsx
const ThemeContext = createContext(null);

export default function App() {
	const [theme, setTheme] = useState('dark'); // state 변수 선언하고
	
	const themeToggler = {
		dark: 'light',
		light: 'dark',
	};
	
	const toggleTheme = () => {
		setTheme(themeToggler[theme]);
	};
	
	return (
		<ThemeContext.Provider value={theme}> // context 값으로 provider에 전달한다.
			<Form />
			// <button
			//	onClick={() => {
			//		if (theme === 'dark') {
			//		setTheme('light');
			//		} else {
			//		setTheme('dark');
			//		}
			//	}}
			>switch</button>	
			<button onClick={toggleTheme}>switch</button>
		</ThemeContext.Provider>
	);
}
```

간단한 예시2) input tag 사용
```jsx
const ThemeContext = createContext(null);

export default function App() {
	const [theme, setTheme] = useState('light'); // state 변수 선언하고
	
	return (
		<ThemeContext.Provider value={theme}> // context 값으로 provider에 전달한다.
			<Form />
			<label>
				<input 
					type="checkbox" 
					checked={theme === 'dark'}
					onChange={e => setTheme(e.target.checked ? 'dark' : 'light')} 
				/>
			</label>
		</ThemeContext.Provider>
	);
}
```

#### 3. Specifying a fallback default value
> fallback 기본값 지정하기

1. 부모 트리에서 특정 context의 provider들을 찾을 수 없는 경우
	useContext()가 반환하는 context 값은 해당 context를 생성했을 때 지정한 기본값과 동일.
```jsx
const ThemeContext = createContext(null);
```
2. default value는 절대 변경되지 않는다.
	context를 업데이트 하려면 state를 사용한다.


#### 4. Overriding context for a part of the tree
> 트리 일부에 대한 context 재정의하기

트리의 일부분을 다른 값의 provider로 감싸 해당 부분에 대한 context를 재정의할 수 있다.
```jsx
<ThemeContext.Provider value="dark">  
	...
	<ThemeContext.Provider value="light">  // 여기부터
		<Footer />  
	</ThemeContext.Provider>  // 여기까지
	...  
</ThemeContext.Provider>
```
필요한 만큼 provider들을 중첩하고 재정의할 수 있다.

#### 5. Optimizing re-renders when passing objects and functions
> 객체 및 함수 전달 시 리렌더링 최적화

context를 통해 **객체**와 **함수**를 포함한 **모든 값**을 전달할 수 있다.

아래 코드에서 `MyApp`이 리렌더링할 때마다 *다른 함수*를 가리키는 *다른 객체*가 되므로 `useContext(AuthContext)`를 호출한 트리 깊숙한 곳의 모든 컴포넌트도 리렌더링해야 한다.

소규모의 경우 문제가 되지 않겠지만, 성능 최적화를 위해 *함수는 `useCallback`*, *객체는 `useMemo`* 로 감싸면 된다.

```jsx
function MyApp() {
  const [currentUser, setCurrentUser] = useState(null);

  const login = (response) => {
    storeCredentials(response.credentials);
    setCurrentUser(response.user);
  };
  
  return (
    <AuthContext.Provider value={{ currentUser, login }}>
      <Page />
    </AuthContext.Provider>
  );
}
```
아래와 같이 바꿔준다.

```jsx
import { useCallback, useMemo } from 'react';

function MyApp() {
	const [currentUser, setCurrentUser] = useState(null);
	
	const login = useCallback((response) => {
		storeCredentials(response.credentials);
		setCurrentUser(response.user);
	}, []);
	
	const contextValue = useMemo(() => {
		currentUser,
		login
	}), [currentUser, login]);
	
	return (
		<AuthContext.Provider value={contextValue}>
		  <Page />
		</AuthContext.Provider>
	);
}
```
`MyApp` 이 리렌더링해야 하는 경우에도 `currentUser`가 변경되지 않는 한, `useContext(AuthProvider)`를 호출하는 컴포넌트는 리렌더링할 필요가 없다.
