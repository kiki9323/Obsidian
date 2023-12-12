# 기본 타입
## 타입스크립트가 자체적으로 제공하는 타입

(사진)

### 원시타입 (Primitive Type)
하나의 값만 저장하는 타입
> number, string, boolean, null, undefined

```ts
// number
let num1: number = 123; // :number 타입어노테이션이라 부른다.
let num2: number = -123;
let num3: number = 0.1234;
let num5: number = Infinity;
let num6: number = NaN;

// string
let str1: string = "hello";
let str2: string = 'hello';
let str3: string = `hello`;

// boolean
let bool1: boolean = true;
let bool2: boolean = false;

// null
let null1: null = null;

// undefined
let unde1: undefined = undefined;

// 리터럴 타입
let numA: 10 = 10;
let strA: "hello" = "hello";
let boolA: true = true;

// 허용되지 않음
let numA: number = null; // null 형식은 대입할 수 없다.
//
// tsconfing.json에서 "strictNullChecks": false 를 추가하면 null을 넣을 수 있다.

```


### 참조타입 (Reference Type)
#### 배열
```ts
// array
let numArr: number[] = [1, 2, 3]; // 타입 어노테이션
let strArr: string[] = ["hi", "im", "noodle"];
let boolArr: Array<boolean> = [true, false, true]; // <>꺽쇠에 다른 타입을 넣는것을 제네릭이라고 함.

// array에 들어가는 요소들의 타입이 다양한 경우
let multiArr: (string | number)[] = [1, "hello"];

// 다차원 배열의 타입을 정의하는 방법
let doubleArr: number[][] = [
	[1, 2, 3],
	[4, 5],
];
```

```ts
let tup1: [number, number] = [1, 2];
tup1 = [1, 2, 3]; // - ERROR 길이 제한
tup1 = ["1", "2", "3"]; // - ERROR 타입이 안 맞음
  
let tup2: [number, string, boolean] = [1, "2", true];

tup2.push(1);
tup2.pop();
tup2.pop();
tup2.pop(); // 을 해줘도 에러가 안난다. 조심!
```
```ts
// 튜플 -> js로 컴파일 해보면 튜플은 그저 배열일 뿐이다.
// 그럼 언제 사용하나요? -> 인덱스별로 다른 값을 넣어야하는데 순서가 헷갈리는 경우!
const users: [string, number][] = [
	["호호", 1],
	["모모", 2],
	["나나", 3],
	["키키", 4],
	[5, "오잉"],
];
```

#### 객체
```ts
// object - 잘 쓰지 않는다.
let user: object = {
	id: 1,
	name: "noodle",
};

user.id; // 'object' 형식에 'id' 속성이 없습니다. 라고 에러가 뜸.

/**
* 이유는 `: object`로 지정해줬는데 그 내부에 뭐가 있는지는 알 수 없기 때문!
* 아래와 같이 바꿔준다.
*/
```

```ts
// object 리터럴 타입 - 구조를 모두 적어준다.
let user2: {
	id: number;
	name: string;
} = {
	id: 1,
	name: "noodle",
};

user2.id;
```

* 이름 기준 = 명목적 타입 시스템 (대부분의 다른 언어들이 채택하는 방법)
* 프로퍼티 기준 = 구조적 타입 시스템 (TS)

#### 옵셔널
```ts
let user2: {
	id?: number;
	name: string;
} = {
	id: 1,
	name: "noodle",
};

user2 = {
	name: '홍길동'
}
```

#### readonly
```ts
let config: {
	readonly apiKey: string;
} = {
	apiKey: "MY API KEY",
};

config.apiKey = 'hacked'; // 읽기 전용 속성이므로 'apiKey' 에 할당할 수 없습니다.
```


### 타입 별칭과 인덱스 시그니처

```ts
// 타입 별칭
type User = {
	id: number;
	name: string;
	nickname: string;
	birth: string;
	bio: string;
	location: string;
};
```
### enum

```ts
// Enum 열거형 타입 (Enumerable type)
// 여러가지 값들에 각각 이름을 부여해 열거해두고 사용하는 타입
enum Role {
	ADMIN = 0,
	USER = 1,
	GUEST = 2,
}

enum Language {
	korean = "ko",
	english = "en",
}

const user1 = {
	name: "toto",
	role: Role.ADMIN, // 0 관리자
	language: Language.english,
};

const user2 = {
	name: "Kiki",
	role: Role.USER, // 1 일반 유저
	language: Language.korean,
};


const user3 = {
	name: "momo",
	// role: 2, // 2 게스트
	role: Role.GUEST, // 2 게스트
};
```