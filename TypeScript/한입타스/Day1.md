## 타입스크립트를 소개합니다

JS
* 유연한 문법
* 버그 발생 가능성 높음 
* 자유로움

타입스크립트 = 자바스크립트의 확장판
* 자바스크립트를 더 안전하게 사용할 수 있도록 "타입 관련 기능들을 추가한" 언어


## 자바스크립트의 단점과 타입스크립트
![[Snipaste_2023-12-09_17-01-22.png|500]]
### 모든 프로그래밍 언어는 타입 시스템을 가지고 있다
### 타입 시스템?
: 언어의 타입 관련된 문법 체계이다.  

1. 값들을 어떤 기준으로 묶어서 규정할 것인지?
2. 코드의 타입은 언제 검사할지?
3. 어떻게 타입을 검사할지?

### 타입 시스템의 종류
1. 정적 타입 시스템 : 코드 **실행 이전** 모든 변수의 타입을 고정적으로 결정
2. 동적 타입 시스템 : 코드를 실행하고 나서 그때 그때 마다 **유동적**으로 변수의 타입을 결정 (ex. js)

#### JS with 동적 타입 시스템
**동적 타입 시스템**
특징: 코드를 실행하면서 유동적으로 변수의 타입을 결정 (런타임)    
단점 : 코드의 타입 오류를 미리 검사할 수 없다. 예기치 못한 오류가 발생할 수 있다.  
```js
let a = "hello~";
```

#### TS with 정적 타입 시스템 & 점진적 타입 시스템
**정적 타입 시스템**
특징: 모든 변수에 일일이 타입 지정 -> 타이핑 증가, 유연하지 못함  

**점진적 타입 시스템**
모든 변수에 타입을 일일이 지정할 필요가 없다.
= `Gradual Type System`
```ts
let a = 1;  // 숫자 값으로 초기화 되었으니 a가 숫자 타입이라는걸 추론함.
a.toUpperCase(); // Error
```


---

## 타입스크립트 동작 원리
1. 어떤 과정을 거쳐 동작하나?
2. 왜 그렇게 동작하나?

### 컴파일러의 컴파일 과정
사람 [프로그래밍 언어] ---컴파일---> 컴퓨터 [기계어] 즉, `Byte Code`

#### JS 컴파일 과정
```js
JS -> AST(코드 실행과 관계 없는 것들을 다 지우고 **추상 문법 트리**로 만듦)  -> Byte Code
```

#### TS 컴파일 과정
```ts
TS -> AST -> Type Checking (타입 검사) -> JS (AST를 JS로 변환하고 종료)
```