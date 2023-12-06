https://developer.chrome.com/blog/style-queries/

# Style Query
DOM 요소의 CSS 속성과 값을 검색하고 필터링하는 데 사용된다.  

## 언제 사용?
스타일 쿼리는 다양한 변형이 포함된 재사용 가능한 구성 요소가 있거나, 모든 스타일을 제어할 수 없지만 특정 경우에 변경 사항을 적용해야 하는 경우 유용하다.  

## 예시
카드 제품에 이름이 지정된 사용자 정의 속성에 의해 실행되는 **'신규'** 또는 **'재고 부족'** 과 같은 추가 세부 정보/메모가 있다.  
```html
<div class="product-card-container" style="--detail: new"> 
	... 
</div>
...
<div class="product-card-container" style="--detail: low-stock"> 
	... 
</div>
```
이러한 유형의 정보는 서버에서 렌더링될 가능성이 높고, 위와 같이 인라인으로 적용될 수 있다.

```css
.comment-block {
	display: block;
}
@container style(--detail: new) {
	.comment-block::after {    
		content: 'New';   
		border: 1px solid currentColor;    
		background: white;    
		...  
	}
}
@container style(--detail: low-stock) {  
	.comment-block::after {    
		content: 'Low Stock';   
		border: 1px solid currentColor;    
		background: white;    
		...  
	}
}
```


## 디자인과 데이터 분리
데이터 레이어(페이지에 렌더링 되는 DOM)를 분리하는 구조적 이점이 있다.  





### 의문
- [ ] 단순히 클래스로 분기하는거랑 무슨 차이? 어떤 이점이 있길래?
	- `.card-container` `.card-new` `.card-popular` 이렇게 클래스로 분기 처리
- [ ] 스타일과 데이터를 분리한다 치면, `data-*` 속성도 있는데 굳이? 웹접근성 까지 신경쓴다면 `aria-*` 도 되지 않을까?

