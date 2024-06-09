# 20장. strict mode

## 20.1 Strict mode란?

자바스크립트 언어의 문법을 좀 더 엄격히 적용하여 오류를 발생시킬 가능성이 높거나, 자바스크립트 엔진의 최적화 작업에 문제를 일으킬 수 있는 코드에 대해 명시적인 에러를 발생

strict mode가 아닌 `ESLint` 같은 린트 도구를 사용해도 유사한 효과를 얻을 수 있으며, 더욱 강력한 효과를 얻을 수 있다고 한다.

## 20.2 strict mode의 적용

전역의 선두 또는 함수 몸체 선두에 `'use strict';` 를 추가하면 된다.

```jsx
function foo() {
	'use strict';
	
	x = 10; // ReferenceError
}
foo();
```

## 20.3 전역에 사용하는 것은 지양

스트립트 단위로 적용된다.

```html
<!DOCTYPE html>
<html>
<body>
	<script>
		'use strict';
	</script>
	<script>
		x = 1; // 에러 발생 안함
		console.log(x); // 1
	</script>
	<script>
		'use strict';
		
		y = 1; // ReferenceError
		console.log(y);
	</script>
</body>
</html>
```

## 20.4 함수 단위로 적용하는 것도 지양

어떤 함수는 사용하고 어떤 함수는 사용하지 않는다? 바람직 하지 않음.  
그리고 적용시킨 함수를 외부에서 적용시키지 않은 함수가 참조한다면? 문제가 발생할 수 있음.  
그럼 일일이 적용해야 하는데 그것도 번거로움

***그래서 즉시 실행 함수로 감싼 스트립트 단위로 적용하는 것이 바람직함.***

## 20.5 strict mode가 발생시키는 에러

### 암묵적 전역

선언하지 않은 변수를 참조하면  ReferenceError 발생

### 변수, 함수, 매개변수의 삭제

delete 연산자로 변수, 함수, 매개변수 삭제하면 SyntaxError 발생

### 매개변수 이름의 중복

중복된 매개변수 이름을 사용하면 SyntaxError 발생

### with 문의 사용

with 문 사용하면 SyntaxError 발생.

## 20.6 strict mode 적용에 의한 변화

### 일반 함수의 this

this 사용 시 일반함수는 언디파인드, 생성자 함수는 잘 나옴

```jsx
(function () {
	'use strict';
	
	function foo() {
		console.log(this); // undefined
	}
	foo();
	
	function Foo() {
		console.log(this) // Foo
	}
	new Foo();
}());
```

### arguments 객체

```jsx
(function (a) {
	'use strict';
	
	// 매개변수에 전달된 인수를 재할당
	a = 2;
	
	// 변경된 인수가 arguments 객체 반영되지 않음
	console.log(arguments); // { 0: 1, length: 1}
}(1));
```

<br><br><br>

# 21. 빌트인 객체

## 21.1 자바스크립트 객체의 분류

- 표준 빌트인 객체 : ECMAScript 사양에 정의된 객체
- 호스트 객체 : ECMAScript 사양에 정의되어 있지 않지만 자바스크립트 실행 환경(브라우저 or Node.js)에서 추가로 제공하는 객체
- 사용자 정의 객체 : 사용자가 직접 정의한 객체

## 21.2 표준 빌트인 객체

Math, Reflect, JSON을 제외한 표준 빌트인 객체는 모두 인스턴스 생성 가능한 생성자 함수 객체!

생성자 함수가 생성한 인스턴스의 프로토타입은 표준 빌트인 객체의 prototype 프로퍼티에 바인딩된 객체임.

## 21.3 원시 값과 래퍼 객체

바로 원시값인 문자열, 숫자, 불리언 등을 사용해도 되는데 왜 String으로 문자를 만들고 Number로 숫자를 만들고… 등등.. 왜 표준 빌트인 생성자 함수가 존재하냐?

원시값은 객체가 아니죠? 그래서 프로퍼티나 메서드를 가질 수 없죠? 근데 객체처럼 동작하죠?ㅋ

```jsx
const str = 'hello'

// 원시 타입인 문자열이 래퍼 객체인 String 인스턴스로 변환!
console.log(str.length); // 5
console.log(str.toUpperCase()); // HELLO

// 그 후 다시 원시값으로 되돌림.
console.log(typeof str); // string
```

즉, 객체처럼 접근하면 암묵적으로 객체를 생성함. 이러한 임시 객체를 **래퍼 객체(wrapper object)**라 함

원시 타입인 문자열이 래퍼 객체인 String 인스턴스로 변환 후 다시 원시값으로 되돌림. 그리고 래퍼 객체는 가비지 컬렉션의 대상이 됨!

## 21.4 전역 객체

브라우저 환경에서는 window, Node.js 환경에서는 global이 전역 객체를 가리킨다.

- var 키워드는 전역 객체와 바인딩 되서 아래 코드 처럼 가능

```jsx
var foo = 1;
console.log(window.foo); // 1
```

- let이나 const는 불가능

```jsx
let foo = 1;
console.log(window.foo); // undefined
```

### 빌트인 전역 프로퍼티와 빌트인 전역 함수

전역 객체는 몇 가지 프로퍼티와 메서드를 가지고 있음. = window나 global 생략하여 참조/호출 가능

- 빌트인 전역 프로퍼티
    - Infinity
    - NaN
    - undefined
- 빌트인 전역 함수
    - eval
    - isFinite
    - inNaN
    - parseFloat
    - parseInt
    - encodeURI / decodeURI
    - encodeURIComponent / decodeURIComponent

### 암묵적 전역

```jsx
var x = 10; // 전역 변수

function foo () {
	// 선언하지 않은 식별자에 값을 할당
	y = 20; // window.y = 20;
	console.log(x + y);
}

foo(); // 30

console.log(window.x); // 10
console.log(window.y); // 20

delete x; // 전역 변수는 삭제되지 않는다.
delete y; // 프로퍼티라서 삭제 가능하다.

console.log(window.x); // 10
console.log(window.y); // undefined
```