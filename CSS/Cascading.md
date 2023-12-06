# Cascading
스타일 (색상, 폰트 등)이 어떻게 적용 되는지를 결정하는 규칙의 집합.

이 규칙에 따라, 한 요소에 여러 스타일 규칙이 적용될 경우 어떤 규칙이 우선적으로 적용될지 결정된다.

### Cascading 의 뜻
**"폭포수처럼 흐르다"** 라는 뜻으로, 여러 소스에서 오는 스타일 규칙들이 **특정 순서에 따라 합쳐진다**는 개념을 나타냅니다.

### Cascading의 순서
1. `!important` 로 선언된 사용자 스타일 시트가 가장 우선. 브라우저 기본 값보다 우선된다.
2. 특정성 (Specificity) : 선택자의 특정성이 높을 수록 더 큰 우선순위를 가진다.  
		[ 선택자의 특정성 규칙 ]
```css
	1순위. 인라인 스타일 (HTML 요소에 직접 적용된 스타일) - 특정성 1
	2순위. #id 선택자 - 특정성 10
	3순위. 클래스 선택자 (.class), 속성 선택자 ([type="text"]), 가상 클래스 (:hover) - 특정성 100
	4순위. 요소 선택자 (div, p 등등), 가상 요소 (::before, ::after) 
```
3. 소스 순서 : 마지막에 선언된 것이 우선
