### 타입 호환성
타입 호환성은 타입간의 **슈퍼-서브 관계**를 기준으로 판단 됨

* 업 캐스팅 - 가능
* 다운 캐스팅 - 불가능 (예외는 있으나 대부분은 불가능)

```ts
a: number = -12; // Number 타입
b: 30 = 30; // Number 리터럴 타입

a = b; // 업 캐스팅 - 가능 (Number 타입 <- Number 리터럴)
b = a; // 다운 캐스팅 - 불가능 (Number 리터럴 <- Number 타입)
```

### 타입 계층도와 함께 특수한 타입들 이해하기
![[Snipaste_2023-12-08_13-57-14.png|600]]

#### 1. Unknown 타입 (전체 집합)
모든 타입의 슈퍼 타입
##### 성질
모든 타입의 값을 저장할 수 있다.
* 업 캐스팅 연산 가능
```ts
let a: unknown = 1;
let b: unknown = "hello";
let c: unknown = true;
let d: unknown = null;
let e: unknown = undefined;
let f: unknown = [];
let g: unknown = {};
let h: unknown = () => {};
```

반대로, 어떤 타입의 변수에도 저장할 수 없다.
* 다운 캐스팅 연산 불가능
```ts
let unknownVar: unknown;

let num: number = unknownVar; // - ERROR
let str: string = unknownVar; // - ERROR
let bool: string = unknownVar; // - ERROR
let obj: string = unknownVar; // - ERROR
let func: string = unknownVar; // - ERROR
```

##### 언제 사용할 수 있나요?
* 현재 정확한 타입을 알기 어려울 때 사용 
* **타입 좁히기**와 함께 값을 유연하게 사용 가능 [ts playground](https://www.typescriptlang.org/ko/play?#code/GYVwdgxgLglg9mABMOcAUA3AhgGxAUwC5FwBrMOAdzAEpEBvAKEURmETSgE8AHfOdtjz5EAXnGIA5GBABbAEb4ATpLpMWLJfigglSIQQB0UOADEYAD3wATNDQDczRAF9E+HAGcRbDtz4DEAxFxUSkPKCUYMABzVQYnTW1dfVwjEwAZOAhcfEzKZQBhLC87RxZXdy94jUQtHT1A1PwnZ0ZWxhR0AEYHDtQ0SQALdxw4VUdOtHBrfGAomxogA)
```ts
// 어떠한 값을 넣어도 문제가 되지 않으니 타입 좁히기와 함께 사용하여 유연하게 사용한다.

function foo(value: unknown) {
  if (typeof value === 'number') {
    return value.toFixed();
  } else if (typeof value === 'string') {
    return value.toLocaleLowerCase();
  } else {
    return value
  }
}

foo(1);
foo('hello');
foo(undefined);
```

#### 2. Never 타입 (공집합)
##### 성질
* 계층도의 최하위 존재. 모든 타입의 서브 타입.
* 모든 타입의 변수에도 저장할 수 있다.
	* 즉, 모든 타입의 업 캐스팅이 가능
```ts
let neverVar: never;

let never1: number = neverVar;
let never2: string = neverVar;
let never3: boolean = neverVar;
let never4: null = neverVar;
let never5: undefined = neverVar;
let never6: [] = neverVar;
let never7: {} = neverVar;
```

Never 타입에는 어떠한 값도 저장할 수 없다. 
* 다운 캐스팅 불가능
```ts
let never1: never = 1; // - ERROR
let never2: never = 'hello'; // - ERROR
let never3: never = true; // - ERROR
let never4: never = null; // - ERROR
let never5: never = undefined; // - ERROR
let never6: never = []; // - ERROR
let never7: never = {}; // - ERROR
let never8: never = () => {}; // - ERROR
```

##### 언제 사용할 수 있나요?
* 호출되지 않아야 하는 함수를 만들 때 
* Switch의 완전성을 보장할 때 활용할 수 있다 - `Exhaustiveness Checking`

예시 `Exhaustiveness Checking`
```ts
// 호출시 오류가 발생하는 함수
// - Switch의 완전성을 보장할 때 활용할 수 있다.

// 불완전한 switch 문
function error(v: never) {
    throw new Error();
}

type Color = "RED" | "GREEN" | "BLUE";

function getColorName(c: Color) {
    switch(c) {
        case "RED": // 타입 좁히기
            return "rgb(255,0,0)";
        case "GREEN": // 타입 좁히기에 의해서 type Color = "GREEN" | "BLUE";
            return "rgb(0,255,0)";
        default: { // 타입 좁히기에 의해서 type Color = "BLUE";는 문자열인데? 
            return error(c); // 이때 오류가 발생하게 된다.
        }
    }
}
```

```ts
// 안전성이 보장된 switch
function error(v: never) {
    throw new Error();
}

type Color = "RED" | "GREEN" | "BLUE";

function getColorName(c: Color) {
    switch(c) {
        case "RED": // 타입 좁히기
            return "rgb(255,0,0)";
        case "GREEN": // 타입 좁히기에 의해서 type Color = "GREEN" | "BLUE";
            return "rgb(0,255,0)";
        case "BLUE": // 타입 좁히기에 의해서 type Color = "BLUE";
        	return "rgb(0,0,255)";
        default: { // swtich문의 안전성을 보장해준다.
            return error(c); 
        }
    }
}
```

#### 3. Any 타입 (치트키)
##### 성질
* 타입 검사를 **받지 않음**
* 모든 타입의 슈퍼 타입, 서브 타입 처럼 동작한다.

##### 예외
그러나, Never 타입의 변수에는 저장할 수 없다. (Never는 공집합 이므로)
```ts
let anyVar: any;
let _never: never = anyVar; // - Error
```


##### 언제 any를 사용하나?
* **불가능한 타입 단언**을 가능케 할 수 있음.
```ts
// Not works
let str1: string = 10 as string; // number -> string 단언 불가
```

> 타입 단언은 "단언 이전"과 "단언 이후"의 타입들이 서로 겹치는 타입이어야만 한다. 
> 즉, super-sub 관계를 가져야한다.

`any`를 통해 이를 해결할 수 있다.
```ts
// Works!
let str2: string = 10 as any as string; // number -> any -> string

// 사실 unkwnon 도 가능 ㅋㅋ
let str3: string = 10 as unkwnon as string; // number -> unkwnon -> string

// 호호 never 도 가능 ㅋㅋ (super-sub 관계만 지키면 된다.)
let str4: string = 10 as never as string; // number -> unkwnon -> string
```



#### 퀴즈퀴즈
* A, B, C, D는 각각 어떤 타입으로 추론될까욤?
```ts
type A = never & string; // never (A 교 공집합 = 공집합)
type B = never | string; // string (A 합 공집합 = A)
type C = unknown & string; // string (A 교 전체집합 = A)
type D = unknown | string; // unknown (A 합 전체집합 = 전체집합)
```

