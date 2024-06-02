# 18. 함수와 일급 객체

## 18.1 일급 객체

1. 런타임에 생성이 가능한지
2. 변수나 자료구조(객체, 배열 등)에 저장 가능한지
3. 함수의 매개변수에 전달 가능한지
4. 함수의 반환값으로 사용 가능한지

함수 = 일급 객체? = 함수를 객체와 동일하게 사용할 수 있음  
객체 = 값.  
함수는 값과 동일하게 취급 가능

결국, 함수는 값을 사용할 수 있는 곳이라면 어디서든지 리터럴로 정의 가능! + 런타임에 함수 객체로 평가!

일반 객체와의 차이점은 호출 유무! + 함수 고유의 프로퍼티 소유

<br>

## 18.2 함수 객체의 프로퍼티

> 💡 브라우저 콘솔에서 `console.dir` 메서드를 이용하여 객체 내부를 들여다 볼 수 있음!

### arguments 프로퍼티

함수 호출 시 전달된 인수들의 정보를 담고있는 이터러블 객체!  
함수 내부에서 지역 변수처럼 사용되므로 함수 외부에서는 참조할 수 없음

```jsx
function multiply(x, y) {
  console.log(arguments);
  return x * y;
}

console.log(multiply()); // NaN
console.log(multiply(1)); // NaN
console.log(multiply(1, 2)); // 2
console.log(multiply(1, 2, 3)); // 2
```

함수 정의 시 선언한 매개변수는 함수 몸체 안에서 변수와 동일하게 취급됨  
= 암묵적으로 매개변수가 선언되고 undefined로 초기화 후 인수 할당!  
그래서 1, 2번이 NaN 나온 이유는 undefined로 곱셈을 했기 때문!  
그리고 인수가 초과되면 버려지는 것이 아닌 arguments 객체의 프로퍼티로 보관됨!

그리고 배열이 아닌 유사 배열 객체이므로 배열 메서드를 사용할 수 없고, Function.prototype.call, Function.prototype.apply를 사용해 간접 호출 해야함.  
이러한 번거로움을 해결하기 위해 Rest 파라미터가 도입됨!

### caller 프로퍼티

참고로 ECMAScript 사양에 포함되지 않은 비표준 프로퍼티임  
함수 객체의 caller 프로퍼티는 함수 자신을 호출한 함수를 가리킴

### length 프로퍼티

함수 정의 시 선언한 매개변수의 개수를 가리킴  
주의할 점! arguments 객체의 length 프로퍼티와 함수 객체의 length 프로퍼티의 값은 다를 수 있음!

- argument 객체의 length 프로퍼티 ⇒ 인자의 개수
- 함수 객체의 length 프로퍼티 ⇒ 매개변수의 개수

### name 프로퍼티

함수 이름을 나타내는 프로퍼티

ES5와 ES6에서 동작을 달리하므로 주의해야 함!

- ES5 : 익명 함수 표현식의 경우 빈 문자열의 값을 갖음
- ES6: 함수 객체를 가리키는 식별자를 값으로 갖음

```jsx
// 기명 함수 표현식
var namedFunc = function foo() {};
console.log(namedFunc.name); // foo

// 익명 함수 표현식
var anonymousFunc = function() {};
// ES5에서 값 : 빈 문자열
// ES6에서 값 : 함수 객체를 가리키는 변수 이름
conosle.log(anonymousFunc.name); // anonymousFunc

// 함수 선언문(Function declaration)
funtion bar() {}
console.log(bar.name); // bar
```

### `__proto__` 접근자 프로퍼티

모든 객체는 [[Prototype]]이라는 내부 슬롯을 갖는다!

`__proto__` 프로퍼티는 [[Prototype]] 내부 슬롯이 가리키는 프로토타입 객체에 접근하기 위해 사용하는 접근자 프로퍼티. 직접 접근이 아닌 간접 접근 방법을 제공하는 경우에 한하여 접근 가능

```jsx
const obj = { a: 1 };

// 객체 리터럴 방식으로 생성한 객체의 프로토타입 객체는 Object.prototype이다.
console.log(obj.__proto__ === Object.prototype); // true

console.log(obj.hasOwnProperty("a")); // 객체 고유의 프로퍼티 키이므로 true 반환
console.log(obj.hasOwnProperty("__proto")); // 상속받은 프로토타입의 프로퍼티 키 이므로 false 반환
```

### prototype 프로퍼티

생성자 함수로 호출할 수 있는 함수 객체 = constructor만 소유하는 프로퍼티임!

```jsx
// 함수 객체는 prototype 프로펕를 소유함
(function () {}).hasOwnProperty("prototype"); // true

// 일반 객체는 prototype 프로퍼티를 소유하지 않는다.
({}).hasOwnProperty("prototype"); // false
```
