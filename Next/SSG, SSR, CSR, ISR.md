# 1. CSR
> CSR - Client Side Rendering

초기 로딩이 느리고, 검색 엔진 최적화가 좋지 못하다.

# 2. SSR
> SSR - Server Side Rendering

서버 부하가 크다


---
# 3. SSG

> SSG - Static Site Generation | 정적 사이트

Build 시(next build 시) 정적인 HTML 파일이 서버에 생성되는 것을 의미한다.

이미 생성된 파일이 있기에 다른 유저가 같은 URL로 요청했을 때 어떠한 작업도 하지않고 HTML만 반환하면 되므로 응답속도가 매우 빠르다. - **초기렌더링속도**

Next.js에서는 기본적인 방법으로 SSG를 사용해 데이터 유무에 관계없이 정적인 페이지를 만든다.  

```markdown
ex) 마케팅 페이지, 블로그 페이지 등
외부 요청에 의해 변하지 않는 페이지(=랜딩페이지)의 경우 SSG로 작성할 수 있다.  
```


> **뭔가 바뀐다면?** 재빌드를 다시 해야한다.  

동적 컨텐츠가 포함된다면 SSG는 사용하지 않는다.  

---
# 4. ISR
> ISR - Incremental Static Regeneration | 점진적으로 정적 페이지를 다시 생성해준다.  

SSG + revalidate

- 빌드 시점에 페이지 생성해준다. (이것은 SSG와 동일)
- 그러나, 일정 시간 지난 후(revalidate) 페이지 새로 생성해준다. (최신 데이터로 업데이트, SSG와의 차이)
```js
만약 revalidate를 60초로 설정해 두었다면, 
60초 뒤 첫번째 호출은 이전 페이지를 보여주고, 이후 새로운 페이지가 생성된다.  
```



---

