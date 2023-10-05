# Escape Hatches
: 탈출구

컴포넌트 중 일부는 React **외부**의 시스템을 제어하고 동기화해야 할 수 있다.  
* 브라우저 API를 사용해 input에 초점 맞추기
* React 없이 구현된 비디오 플레이어를 재생 및 일시정지
* 원격 서버에 연결해서 메시지를 수신
등, 외부로 나가서 외부 시스템에 연결할 수 있다.  

---
## index
* 리렌더링하지 않고 정보를 **기억**하는 방법
* React가 관리하는 DOM element에 접근하는 방법
* 컴포넌트를 외부 시스템과 동기화하는 방법
* 컴포넌트에서 불필요한 Effect를 제거하는 방법
* effect의 생명주기가 컴포넌트와 어떻게 다른지
* 일부 값이 Effect를 다시 촉발하는 것을 방지하는 방법
* Effect 재실행 빈도를 줄이는 방법
* 컴포넌트 간 로직을 공유하는 방법 
---

## [Referencing values with Refs](https://react-ko.dev/learn/escape-hatches#referencing-values-with-refs)
: ref로 값 참조하기

새 렌더링을 트리거하지 않도록 하려면 Ref 사용  
```jsx
const ref = useRef(0);
```

`ref.current` 프로퍼티를 사용하여 ref의 현재 값에 접근한다.  

`state`와 마찬가지로 `ref`는 리렌더링 사이에 `React`에 의해 유지된다.  
다만 `state`를 설정하면 컴포넌트가 다시 렌더링되는 반면, `ref`를 변경하면 그렇지 않다.  

### ref는 React가 추적하지 않는다.  
컴포넌트의 렌더링 출력에 영향을 주지 않는 timeout ID, DOM element 및 기타 객체를 저장한다.  

## [Manipulating the DOM with Refs](https://react-ko.dev/learn/escape-hatches#manipulating-the-dom-with-refs)
: ref로 DOM 조작하기

React는 렌더링 출력과 일치하도록 DOM을 자동으로 업데이트하므로 컴포넌트에서 자주 조작할 필요가 없다.  
그러나 DOM node에 대한 ref가 필요할 때가 있다.
#### 1. 노드에 초점 맞추기
```jsx
const inputRef = useRef(null);
function handleClick() {
  inputRef.current.focus();
}
return (
  <>
    <input ref={inputRef} />
    <button onClick={handleClick}>Focus the input</button>
  </>
)
```
#### 2. 스크롤하거나 크기와 위치를 측정
...

#### 3. ref 콜백을 사용하여 refs 목록 관리
많은 ref가 필요할지 모르는 경우 제대로 동작하지 않는다.  
```jsx
<ul>
  {items.map((item) => {
    // Doesn't work!
    const ref = useRef(null);
    return <li ref={ref} />;
  })}
</ul>
```
컴포넌트 최상위 레벨에서만 호출해야 하고, 반복문 또는 `map()` 내부에서는 `useRef`를 호출할 수 없기 때문이다.  

#### 다른 방법
1. 부모 엘리먼트에 대한 단일 ref를 가져와서 querySelectorAll로 하위 노드 찾기  
	=> DOM 구조가 변경되면 깨질 수 있다.  
2. ref 속성에 함수 전달 (= ref 콜백)
	ref 설정시 DOM 노드로 콜백, 지울 때는 null로 ref 콜백 호출  
	
```jsx
<li
  key={cat.id}
  ref={node => {
    const map = getMap();
    if (node) {
      map.set(cat.id, node);
    } else {
      map.delete(cat.id);
    }
  }}
</li>
```

## [Synchronizing with Effects](https://react-ko.dev/learn/escape-hatches#synchronizing-with-effects)
: Effect와 동기화하기

## [You Might Not Need An Effect](https://react-ko.dev/learn/escape-hatches#you-might-not-need-an-effect)
: Effect가 필요하지 않을 수도 있습니다
## [Lifecycle of reactive Effects](https://react-ko.dev/learn/escape-hatches#lifecycle-of-reactive-effects)
: 반응형 Effect의 생명주기
##  [Separating events from Effects](https://react-ko.dev/learn/escape-hatches#separating-events-from-effects)
: 이벤트와 Effect 분리하기

## [Removing Effect dependencies](https://react-ko.dev/learn/escape-hatches#removing-effect-dependencies)
: Effect 의존성 제거하기

## [Reusing logic with custom Hooks](https://react-ko.dev/learn/escape-hatches#reusing-logic-with-custom-hooks)
: 커스텀 훅으로 로직 재사용하기
