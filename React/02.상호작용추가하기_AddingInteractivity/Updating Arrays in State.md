# Updating Arrays in State
> 배열 state 업데이트
## Updating arrays without mutation
> 변이 없이 배열 업데이트하기

JS에서 배열은 객체의 또 다른 종류일 뿐이다.  
*객체와 마찬가지*로, React state의 배열은 읽기 전용으로 취급해야 한다. 

| -         | avoid (mutates the array: 배열 직접 변이) | prefer (returns a new array: 새 배열 반환)     |
| --------- | ------------------------- | -------------------------------- |
| adding    | `push`, `unshift`         | `concat`, `[...arr]` spread syntax |
| removing  | `pop`, `shift`, `splice`  | `filter`, `slice`                |
| replacing | `splice`, `arr[i] = ...`  | `map`                            |
| sorting   | `reverse`, `sort`         | copy the array first: 배열을 복사한 다음 처리             | 

공식 문서에서는 immer를 사용할 수도 있다고 하는데, 내 주변 실무자들은 로대시를 많이들 사용하더라..

#### slice()
splice()는 잊어도 된다.

#### push() 는 배열을 변이한다.
예시코드
(push변이코드)[https://codesandbox.io/s/1vkb9e?file=/App.js&utm_medium=sandpack]

#### 기존 항목과 **앞**에 새 항목을 포함하는 새 배열을 만드는 방법.
##### 1. `...` spread syntax
```js
setArtists([
  { id: nextId++, name },
  ...artists,
])
```
##### 2. local mutation
```javascript
setArtists(prevArtists => {
  const newArtists = [...prevArtists]; // 새 배열 
  newArtists.unshift({ id: nextId++, name });
  return newArtists;
});
```

##### 3. 무조건 새 배열만 리턴해주면 된다.
```javascript
setArtists((prevArtists) => {
  prevArtists.unshift({ id: nextId++, name }); // 기존 데이터 mutation
  return [...prevArtists]; // 새 배열
});
```

#### 배열 사이에 삽입하기
특정 위치에 항목을 삽입하고 싶을 때 `...`와 `slice()` 함께 사용하면 된다.   
```javascript
const handleInserting = () => {
  const insertAt = 1; // 특정 위치
  const nextArtists = [
    // Items before the insertion point:
    ...artists.slice(0, insertAt), 
    // New Item:
    { id: nextId++, name }, 
    // Items after the insertion point:
    ...artists.slice(insertAt)
  ];
  setArtists(nextArtists);
}
```

