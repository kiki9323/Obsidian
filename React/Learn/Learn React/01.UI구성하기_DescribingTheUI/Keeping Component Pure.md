# Keeping Component Pure
> 컴포넌트 순수성 유지

## Purity: Component as formulas
> 순수성: 수식으로서의 컴포넌트

### 순수함수
* 자신의 일에만 신경쓴다. 호출되기 전에 존재했던 객체나 변수를 변경하지 않는다.
* 동일 입력, 동일 출력. 동일한 입력이 주어지면 동일한 결과를 반환해야 한다.

리액트는 순수 함수 개념을 중심으로 설계되었다.  모든 컴포넌트가 순수 함수라고 가정한다.  

## Side Effects: (un)intended consequences
> 사이드 이펙트: 의도하지 (않은) 결과

이런 짓은 하지마라.  
```jsx
let guest = 0;

function Cup() {
  // Bad: changing a preexisting variable!
  // 나쁨: 기존 변수를 변경합니다!
  guest = guest + 1;
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaSet() {
  return (
    <>
      <Cup />
      <Cup />
      <Cup />
    </>
  );
}
```

`props`로 넘겨야 한다.
```jsx
function Cup({ guest }) {
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaSet() {
  return (
    <>
      <Cup guest={1} />
      <Cup guest={2} />
      <Cup guest={3} />
    </>
  );
}
```

- [!] strict mode를 제공한다. 

### Local mutation: Your component’s little secret
> 지역 변이: 컴포넌트의 작은 비밀

- [n] **local**? private 멤버 (지역 변수와 같은 느낌)

변하는 것이 외부에 생성된 것이라면 문제가 될 것이다.  
**렌더링하는 동안 ‘방금’ 생성한 변수와 객체를 변경하는 것은 완전히 괜찮다.

```jsx
function Cup({ guest }) {
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaGathering() {
  let cups = [];
  for (let i = 1; i <= 12; i++) {
    cups.push(<Cup key={i} guest={i} />);
  }
  return cups;
}
```

## Where you _can_ cause side effects
> 사이드 이펙트를 일으킬 수 있는 곳

- [n] **사이드 이펙트**? 
      화면 업데이트, 애니메이션 시작, 데이터 변경과 같은 변경으로 렌더링 중에 일어나는 것이 아닌 *"부수적으로"* 일어나는 일이다. 

보통 **이벤트 핸들러**에 속한다.   
컴포넌트 내부에 정의되어져 있지만 렌더링 중에는 실행되지 않으므로 이벤트 핸들러는 순수할 필요는 없다.    



## Challenges
#### 1 of 3
- [!] js가 먼저 실행 -> 렌더링 순서이기 때문에 `document.getElementById('time')` 못 찾는다.
```jsx
// BAD
export default function Clock({ time }) {
  let hours = time.getHours();
  if (hours >= 0 && hours <= 6) {
    document.getElementById('time').className = 'night'; // - (X)
  } else {
    document.getElementById('time').className = 'day';
  }
  return (
    <h1 id="time">
      {time.toLocaleTimeString()}
    </h1>
  );
}
```


#### 2 of 3
- [!] 배열의 기존 항목을 변경하려고 하면 해당 항목도 복제해야 한다.
- [!] 배열에서 어떤 연산이 배열을 변경하고 어떤 연산이 변경되지 않는지 기억해두는 것이 유용하다. 
      예를 들어, `push`, `pop`, `reverse`, `sort`는 원래 배열을 변경하지만 `slice`, `filter`, `map`은 새 배열을 만든다.

