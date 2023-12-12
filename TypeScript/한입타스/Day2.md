## 버전
```bash
➜ node -v 
v21.4.0

➜ tsc -v
Version 5.3.3
```


## TS 시작하기

```bash
npm init

npm i @types/node@20.8.0

sudo npm install typescript -g 
// TS Compiler 설치. 글로벌 설치시 컴퓨터 전체에 설치하여 사용 
// sudo는 mac에서만 사용

tsc -c // TS 버전 확인
```

## TS 컴파일 하기

```bash
tsc src/index.ts 
```

## JS 실행하기

```bash
node src/index.js
```


## TSX(TypeScript Execute)를 활용하여 한 번에 실행
> ts-node 는 Node 20 버전 이상에서 더 이상 동작하지 않는다.

일일이 계속 코드를 치기엔 넘 불편하당!


* tsx
```bash
sudo npm i -g tsx
tsx src/index.ts
```

* (Deprecated) ts-node
```bash
udo npm install ts-node -g // 설치 후
ts-node src/index.ts // 즉시 실행
```


---
## TS 컴파일러 옵션 설정하기
#### tsconfig.json 설정 시작하기

```bash
tsc --init
```
>> tsconfig.json 파일이 생성됨

#### tsconfig.json 설정하기
```json
{
	"compilerOptions": {
		"target": "ESNext", // ESNext: JS 최신 버전을 의미함
		"module": "ESNext", // ESmodule 사용 (import, export)
		"outDir": "dist", // dist 폴더로 output dir 경로 설정
		"strict": true, // 타입을 엄격하게 검사
		"moduleDetection": "force" // 어떤 모듈로 감지할지
	},
	"include": ["src"] // src 경로 아래에 있는 모든 파일을 한 번에 컴파일하라고 범위 설정
}

```

#### package.json
`"type": "module"`로 설정해줘야만,   
`tsconfig.json`에서 해주었던 `"moduleDetection": "force"` 설정으로 인해 dist/index.js 파일에서 `export{}` 구문이 모듈로 정상 실행된다.
```js
{
	...
	"type": "module"
	...
}
```

#### 실행
```bash
tsc
```


##### option: `"moduleDetection": "force"`
ts가 각각 파일들을 어떤 모듈로 감지할 것 이냐 에 대한 옵션.  

ts는 글로벌 모듈이다. 그래서 파일이 달라도 변수명이 겹치면 충돌이 생김.    
그렇다면 개별 모듈로 만들어줘야 하는데, 이때 방식은  

1. 각 ts 파일에서 `export {};` 를 넣어주던가, (너무 귀찮다!!)
2. `"moduleDetection": "force"` 옵션을 사용하면 이를 해결할 수 있음. (자동방식이다 ㅎㅎ)

> js 에서 esmodule 사용하면 모듈로 인지하여서 파일이 달라지면 각 스코프도 다르게 분리가 되어지는데, ts는 옵션을 설정해주지 않으면 한 스코프내로 인지하여 충돌하는 상황이 벌어짐.

