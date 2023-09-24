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

