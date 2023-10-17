# 15 let, const 키워드와 블록 레벨 스코프
## var 키워드
1. 변수 중복 선언이 허용된다.
2. 함수 레벨 스코프  
함수 외부에서 var 키워드로 선언한 변수는 코드 블록 내에서 선언해도 모두 전역변수가 된다.

```js
var x = 1;
if (true) {
	// x는 전역 변수
	var x = 10;
}
console.log(x); // 10
```
```js
var i = 10;
for(var i = 0; i < 5; i++) {
	console.log(i); // 0 1 2 3 4
}
// 의도치 않게 i의 값이 변경 됨.
console.log(i); // 5
```

3. var 변수 호이스팅

> '선언 단계'와 '초기화 단계' 가 동시에 이루어짐.  

변수 선언문이 스코프의 선두로 끌어 올려진 것처럼 동작한다.   
* 변수 선언문 이전에 참조 가능하며,
* 할당문 이전에 참조하면 `undefined`가 반환된다.  
```js
// var 키워드는 런타임 이전에 선언 단계와 초기화 단계(undefined)가 실행된다.
// 그래서 선언문 이전에 변수를 참조할 수 있다.
console.log(foo); // undefined

foo = 123; // 할당
console.log(foo); // 123

var foo; // 변수 선언은 런타임 이전에 자바스크립트 엔진에 의해 암묵적으로 실행
```
4. window.a로 접근 가능

## let 키워드
1. var 키워드의 단점을 보완한다.  
	* 재선언 금지
	* 재할당 가능
2. 블록 레벨 스코프
	* 코드 블록 (함수, if, for, while, try/catch)
```js
let foo = 1; // 전역 변수

{ // 블록레벨 스코프로 지역 변수
	let foo = 2;
	let bar = 3; 
}

console.log(foo); // 1
console.log(bar); // ReferenceError: bar is not defined
```

3. let 변수 호이스팅

> '선언 단계'와 '초기화 단계'가 분리되어 진행된다.  

런타임 이전에 암묵적으로 선언 단계가 실행이 된다. 
그러나 초기화는 선언문에 도달해야만 진행이 된다.

초기화 이전에 접근하려고 하면 참조 에러가 난다.  

스코프의 시작 지점 ~ 초기화 단계 (변수 선언문)까지 변수를 참조할 수 없다. TDZ - 참조 에러구간

```js
// 런타임 이전에 선언 단계 실행. 아직 초기화가 되지 않음.
// 초기화 이전의 TDZ에서는 변수를 참조할 수 없다.
console.log(foo); // 참조 에러

let foo; // 선언문. 초기화가 이루어진다.
console.log(foo); // undefined

foo = 1; // 할당
console.log(foo); // 1
```

만약 호이스팅이 발생하지 않았다면 전역 변수인 foo를 출력해야 한다.  
그러나 let도 여전히 호이스팅이 발생하기 때문에 참조 에러가 발생함.
```js
let foo = 1;
{
	console.log(foo); // ReferenceError: Cannot access 'foo' before initializaion
	let foo = 2;
}
```

4. 브라우저 환경에서 실행 시 let,const는 window의 프로퍼티가 아니여서 undefined가 뜬다.
```js
let x = 1;
console.log(window.x); // undefined
```


## const 키워드
* 선언과 초기화
	* 동시에 해야한다.
* 재할당 금지
	* 상수 : 원시 값 설정할 때 재할당 불가라는 특징을 이용하여 const 키워드로 상수를 표현한다.
* const 키워드와 객체
	* 재할당을 금지할 뿐 "불변"을 의미하지 않는다.
	* 프로퍼티 동적 생성, 삭제, 프로퍼티 값의 변경을 통해 객체 변경은 가능
```js
const person = {
	name: 'lee'
}
// 객체는 변경 가능한 값이다. 재할당 없이 변경이 가능하다.
person.name = 'Kim';
```

---
## var vs. let vs. const
* 변수 선언은 기본적으로 const
* 재할당이 필요하다면 let (한정하여 사용, 스코프를 최대한 좁게 만든다.)
* ES6를 사용한다면 var는 피하자.

