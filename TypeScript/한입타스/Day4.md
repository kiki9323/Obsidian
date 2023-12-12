
![](https://i.imgur.com/5eNXUXB.jpg)
### Any, Unknown
타입스크립트에서 지원해주는 독특한 타입

```ts
// any -> 어떤 타입이던지 허용 가능
// 특정 변수의 타입을 우리가 확실히 모를 때

let anyVar: any = 10;
anyVar = "hello";

anyVar = true;
anyVar = {};
anyVar = () => {};

anyVar.toUpperCase();
anyVar.toFixed();

  
let num: number = 10;
num = anyVar;

// 할당 받을 수 있고 할당 할 수도 있다.
// 그러나 컴파일 에러가 아닌 런타임 에러를 뿜기 때문에 타스의 장점을 활용하지 못한다.


// unknown
let unknownVar: unknown;

unknownVar = "";
unknownVar = 1;
unknownVar = () => {};

// any와는 다르게 할당은 불가능하다.
num = unknownVar; // 'unknown' 형식은 'number' 형식에 할당할 수 없습니다.
unknownVar.toUpperCase(); // 'unknownVar'은(는) 'unknown' 형식입니다.

if (typeof unknownVar === "number") { // 이런식으로 타입 좁히기를 활용
	num = unknownVar;
}
```
### Void, never

```ts
// void
// 아무것도 없음을 의미하는 타입

function func1(): string {
	return "hello!";
}

function func2(): void {
	console.log("hi~~");
}

// never
// 불가능한 타입

function func3(): never {
	while (true) {
		// ...
	}
}

function func4(): never {
	throw new Error();
}

  
let anyVar = any;

let a: never;
a = 1; // - error
a = {}; // - error
a = ""; // - error
a = undefined; // - error
a = null; // - error
a = anyVar; // - error

// never는 그 어떤 값도 받을 수 없다.
```
### 타입스크립트 이해하기
1. 어떤 기준으로 타입을 정의하는가?
2. 어떤 기준으로 타입간의 관계를 정의하는가?
3. 어떤 기준으로 타입의 오류를 검사하는가?

에 대한 기준을 명확히 하는 것을 목표로 한다.
### 타입은 집합이다
타입스크립트의 **타입은 집합**이다.  
**집합**? 동일한 속성을 갖는 요소 원소를 하나로 묶는 단위를 말한다.


```ts
/**
* Unknown 타입
* - 모든 타입의 슈퍼 타입
*/

function unknownExam() {
	// 업 캐스팅 가능
	let a: unknown = 1;
	let b: unknown = "hi";
	let c: unknown = true;
	let d: unknown = null;
	let e: unknown = undefined;
	
	let unknownVar: unknown;
	
	// // 다운 캐스팅 불가능
	// let num: number = unknownVar;
	// let str: string = unknownVar;
	// let bool: boolean = unknownVar;
}


/**
* never 타입
* - 모든 타입의 서브 타입
*/

function neverExam() {
	function neverFunc(): never {
		while (true) {
			//...
		}
	}
	
	// 업 캐스팅 가능
	let num: number = neverFunc();
	let str: string = neverFunc();
	let bool: boolean = neverFunc();
	
	// 다운 캐스팅 불가능
	let never1: never = 10; // 'number' 형식은 'never' 형식에 할당할 수 없습니다.
	let never2: never = "string"; // 'string' 형식은 'never' 형식에 할당할 수 없습니다.
	let never3: never = true; // 'boolean' 형식은 'never' 형식에 할당할 수 없습니다.
	let never4: never = undefined; // 'undefined' 형식은 'never' 형식에 할당할 수 없습니다.
}

/**
* void 타입
* - undefined의 슈퍼 타입
*/

function voidExam() {
	function voidFunc(): void {
		console.log("hi~");
	return undefined;
}

let voidVar: void = undefined;
}

  
/**
* any 타입
* - 타입 계층도를 완전히 무시한다.
* - 모든 타입의 슈퍼타입, never를 제외한 서브타입 이기도 하다.
*/

function anyExam() {
	let unknownVar: unknown;
	let anyVar: any;
	let undefinedVar: undefined;
	let neverVar: never;
	
	anyVar = unknownVar;
	undefinedVar = anyVar;
	neverVar = anyVar; // 예외. never 타입은 불가
}
```

### 타입 계층도와 함께 기본타입 살펴보기

![](https://i.imgur.com/s0BFQLA.jpg)
