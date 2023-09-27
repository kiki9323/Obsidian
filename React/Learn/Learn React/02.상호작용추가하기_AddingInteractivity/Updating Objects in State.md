#mutation #closure #spread 
# Updating Objects in State
> 객체 state 업데이트

`state`는 객체를 포함해서 어떤 종류의 javaScript 값이든 저장할 수 있다.  

**그러나 React state에 있는 객체를 직접 변이해서는 안 된다.**
객체를 업데이트하려면 **새 객체를 생성**하고 **복사본을 사용하도록** state를 수정해야 한다.

## What's a mutation?
> 변이란 무엇인가?
### 변이(mutation)
내용은 바뀌는데 본체는 그대로인 것.

이런 경우는 **재할당**입니다.
```js
let a = 1;
a = 10; // 새로운 값으로 완전히 덮어 씌운다. '재할당', '치환', '대체', '덮어쓰기' 
```

이 경우가 **변이(mutation)** 입니다.
```js
let obj = {
	a: 1
}
obj.a = 3 
// 본인 (obj)는 바뀌지 않고 내부의 값만 변경된 상태 - 변이
```


### 기본형과 참조형을 비교해보자.
#### **Primitive Values** 기본형 
종류: `string, number, boolean, null, undefined, Symbol`
특징: *mutation 불가*, 불변하거나 애초부터 "읽기 전용"이다.  

> 리액트에서 렌더링을 트리거하여 값을 바꿀 수 있다. 
> => 완전 새로운 값, 기존값의 변이가 아니라는 뜻.

#### **Reference Values** 참조형
나머지는 전부 참조형. 
특징: 참조형은 언제나 *`mutation` 가능* 

> 객체 내부의 값을 변경하는 것이 가능하다. 이를 변이라 한다. 
> **리액트는 이 변경 사항을 감지 못 함**.


#### 리액트의 불변성?
만약, obj가 mutation하지 않고 새로운 객체를 덮어 쓰면? 
	기존 객체와 새 객체는 **완전히 다른 데이터**이다. 
	=> *불변성을 유지하는 규칙을 만들고, 이 규칙을 따르는 한 해당 객체는 불변하다고 말할 수 있다.
 

## 그래서, Treat state as read-only
> 모든 객체를 읽기 전용으로 취급해라.
## 즉, 결론은 무언가 바꾸고 싶다면? 새로 만드세요.
> -> 이렇게 해야 리액트가 변경사항을 파악하고, 리렌더링할 수 있다.


#### 읽기 전용으로 취급하지 않았을 경우 어떻게 되나?
값은 변하고 있으나 **상태 변경을 감지 못하여** 렌더링이 트리거가 되지 않는다.

```jsx
import { useState } from 'react';
export default function MovingDot() {
  const [position, setPosition] = useState({
    x: 0,
    y: 0
  });
  return (
    <div
      // ❎ BAD
      // 업데이트 함수를 사용하였다. 기존 객체는 놔두고 기존 객체의 프로퍼티만 바꾸는 형태.
      // => 변이 상태
      onPointerMove={e => 
	    setPosition(prev => {
          prev.x = e.clientX;
          prev.y = e.clientY;
          return prev;
        })
      }
      style={{
        position: 'relative',
        width: '100vw',
        height: '100vh',
      }}>
      <div style={{
        position: 'absolute',
        backgroundColor: 'red',
        borderRadius: '50%',
        transform: `translate(${position.x}px, ${position.y}px)`,
        left: -10,
        top: -10,
        width: 20,
        height: 20,
      }} />
    </div>
  );
}
```


아래 처럼 새 객체로 바꿔야 한다.
```jsx
import { useState } from 'react';
export default function MovingDot() {
  const [position, setPosition] = useState({
    x: 0,
    y: 0
  });
  return (
    <div
	// 🅾️ GOOD
	// 새로 만든 익명 객체로 덮어 씌웠다.
      onPointerMove={e => setPosition({
        x : e.clientX,
        y : e.clientY
      })}
      ...
    </div>
  );
}
```



## 지역 변이
#### 지역 변이(Local Mutation) 는 괜찮다. 
* **새로운 객체**를 변이하는 것이기 때문에 리액트가 변경을 감지한다.
```jsx 
const newPosition = {}; // 지역 변이
newPosition.x = e.clientX;
newPosition.y = e.clientY;
setPosition(newPosition);

// 위 코드는 사실 아래와 완전히 동일하다.

setPosition({  // 익명의 객체 리터럴({})을 사용
	x: e.clientX,  
	y: e.clientY  
});

```

#### 변이는 이미 state가 있는 기존 객체를 변경할 때만 문제가 된다.
* 문제가 되는 코드는 *기존 객체*의 state를 수정하기 때문이다.
```jsx
const [position, setPosition] = useState({
  x: 0,
  y: 0
});

onPointerMove={e => {
	position.x = e.clientX;
	position.y = e.clientY;
}}
```


## Copying objects with the spread syntax
> 전개 구문을 사용하여 객체 복사하기

아래와 같이 하나하나씩 변경해주려면 함수도 많아지고 귀찮고 번거롭다.  
```jsx
import { useState } from 'react';

export default function Form() {
  const [person, setPerson] = useState({
    firstName: 'Barbara',
    lastName: 'Hepworth',
    email: 'bhepworth@sculpture.com'
  });

  // ❎ BAD
  function handleFirstNameChange(e) {
    setPerson({ // 이런 친구를 아래에도 동일하게..! 귀찮다.
      firstName = e.target.value;
      lastName = person.lastName;
      email = person.email;
    })
  }

  function handleLastNameChange(e) {
    person.lastName = e.target.value;
  }

  function handleEmailChange(e) {
    person.email = e.target.value;
  }

  return (
    <>
      <label>
        First name:
        <input
          value={person.firstName}
          onChange={handleFirstNameChange}
        />
      </label>
      <label>
        Last name:
        <input
          value={person.lastName}
          onChange={handleLastNameChange}
        />
      </label>
      <label>
        Email:
        <input
          value={person.email}
          onChange={handleEmailChange}
        />
      </label>
      <p>
        {person.firstName}{' '}
        {person.lastName}{' '}
        ({person.email})
      </p>
    </>
  );
}

```

### `Object.assign()`
> `Object.assign(target, source1, source2)`
> source1, source2를 전달하여 대상 객체인 target에 복사한다.

- [n] 주의할 점은 target은 새로운 객체여야 하고, state인 경우에는 mutation이 일어나므로 렌더링이 되지 않는다는 점이다.

**👍🏻 불변객체 업데이트: target이 {} 새로운 객체인 경우**
```jsx
function handleChange(e) {
    setPerson(Object.assign({}, person, {
	    email: e.target.value
    }))
}
```

**👎 변이: state 객체를 변이한 경우** 
```jsx
function handleChange(e) {
    setPerson(Object.assign(person, {
	    email: e.target.value
    }))
}
```


### 전개 구문 + 여러 필드에 단일 핸들러 사용
#### 1. name property 사용
귀찮으니까 name 속성을 설정 해주고, 하나의 함수로 바꿀 수 있게 한다.  
```jsx
...
   // 🅾️ GOOD
  function handleChange({ target: { name, value } }) {
    setPerson({
      ...person,
      [name]: value
    })
  }

  return (
    <>
      <label>
        First name:
        <input
          value={person.firstName}
          name="firstName"
          onChange={handleChange}
        />
      </label>
      <label>
        Last name:
        <input
          value={person.lastName}
          name="lastName"
          onChange={handleChange}
        />
      </label>
      <label>
        Email:
        <input
          value={person.email}
          name="email"
          onChange={handleChange}
        />
      </label>
    </>
  );
}
```

#### 2. 클로저 사용
name 속성 말고 클로저를 이용할 수도 있다.

클로저: 함수와 그 함수가 선언 됐을 때의 렉시컬 환경의 조합.  
각기 다른 값을 가진 여러 개의 클로저는 **별도의 상태를 유지하는 여러 개의 핸들러 함수**라고 생각할 수 있다.  

```jsx
...
   // 🅾️ GOOD
  const handleChange = changedTarget => ({ target : { value } }) => {
    setPerson({
      ...person,
      [changedTarget]: value
    })
  }

  return (
    <>
      <label>
        First name:
        <input
          value={person.firstName}
          onChange={handleChange('firstName')}
        />
      </label>
      <label>
        Last name:
        <input
          value={person.lastName}
		  onChange={handleChange('lastName')}
        />
      </label>
      <label>
        Email:
        <input
          value={person.email}
          onChange={handleChange('email')}
        />
      </label>
    </>
  );
}
```


**[코드 설명]**
```javascript
const handleChange = (changedTarget) => {
  return ({ target: { value } }) => {
    setPerson({
      ...person,
      [changedTarget]: value
    });
  };
};

...

handleChange('firstName')
handleChange('lastName')
handleChange('email')

```

설명하자면,   
`handleChange`는 클로저를 반환한다.   
왜냐하면 이 함수가 반환하는 익명함수가 `handleChange`의 인자인 `changedTarget`을 참조하기 때문이다.    
이렇게 되면, `handleChange`가 호출되어 반환된 익명함수는 자신이 생성될 당시의 환경, 즉 `changedTarget`에 접근할 수 있게 되며 이런 현상을 **클로저**라고 한다.   
그래서 각기 다른 값을 가진 여러 개의 클로저, 즉 **별도의 상태를 유지하는 여러 개의 핸들러 함수**를 만들 수 있다.  


## Updating a nested object 
> 중첩된 객체 업데이트하기

중첩된 객체가 있다.
```jsx
const [person, setPerson] = useState({
    name: 'Niki de Saint Phalle',
    artwork: {
      title: 'Blue Nana',
      city: 'Hamburg',
      image: 'https://i.imgur.com/Sd1AgUOm.jpg',
    }
});
```

일단 냅다 펼쳐서 업데이트 해주면 된다.
```jsx
  const handleChange = ({target:{name, value}}) => {
    setPerson({
      ...person,
      [name]: value,
      artwork: {
        ...person.artwork,
        [name]: value
      }
    })
  }

  return (
    <>
      <label>
        Name:
        <input
          value={person.name}
          name="name"
          onChange={handleChange}
        />
      </label>
      <label>
        Title:
        <input
          value={person.artwork.title}
          name="title"
          onChange={handleChange}
        />
      </label>
      <label>
        City:
        <input
          value={person.artwork.city}
            name="city"
          onChange={handleChange}
        />
      </label>
      <label>
        Image:
        <input
          value={person.artwork.image}
              name="image"
          onChange={handleChange}
        />
      </label>
    </>
  );
}

```


* 그러나 사실 중첩된 객체인 경우 다른 방법, 다른 대안을 생각해보면 좋다. => immer.   
* 또 객체는 사실 중첩된게 아니다. 그저 서로 다른 두 개의 객체를 보고있는 것일 뿐. => 참조형.    

### Immer
state가 중첩된 경우 *flattening하게 만드는 것*을 고려해볼 수 있다.  
그러나 state 구조를 변경하고 싶지 않다면 immer를 사용해 볼 수 있다.  


```js
const initialPosition = {
  x: 0,
  y: 0
};

export default function Canvas() {
  const [shape, setShape] = useState({
    color: 'orange',
    position: initialPosition
  });

  function handleMove(dx, dy) {
     setShape(prev => ({
      ...prev, // 새로운 객체
      position: { // mutation은 position 에서만!
        x: shape.position.x + dx,
        y: shape.position.y + dy,
      }
    }));
  }
  ...
  
```

```js
const initialPosition = {
  x: 0,
  y: 0
};

export default function Canvas() {
  const [shape, setShape] = useState({
    color: 'orange',
    position: initialPosition
  });

  function handleMove(dx, dy) {
     setShape(prev => ({
      const newShape = { ...prev, position: {...prev.position }} 
      newShape.position.x += dx;
      newShape.position.y += dy;
      return newShape;
    }));
  }
  ...
  
```

* 주변 실무자들은 immer 대신 로대시를 많이 사용한다고 한다! 