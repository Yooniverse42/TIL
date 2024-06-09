# 22장. this

## 22.1 this 키워드

`객체` 

= 상태를 나타내는 ***프로퍼티*** + 동작을 나타내는 ***메서드*** 를 하나의 논리적인 단위로 묶은 복합적인 자료구조이다.

메서드는 자신이 속한 객체의 상태, 즉 프로퍼티를 참조하고 변경할 수 있어야 한다. 참조하려면, 먼저 자신이 속한 객체를 가리키는 식별자를 참조할 수 있어야 한다.

객체 리터럴 방식으로 생성한 객체의 경우 메서드 내부에서 메서드 자신이 속한 객체를 가리키는 식별자를 재귀적으로 참조할 수 있다.

(재귀적 : 어떤 사건이 자신을 포함하고 다시 자기 자신을 사용하여 정의할 때, 즉 자기 자신을 포함∙참조하는 것을 뜻함)

```jsx
const circle = {
	// 프로퍼티: 객체 고유의 상태 데이터
	radius: 5,
	
	// 메서드: 상태 데이터를 참조하고 조작하는 동작
	getDiameter() {
		// 이 메서드가 자신이 속한 객체의 프로퍼티나 다른 메서드를 참조하려면 
		// 자신이 속한 객체인 circle을 참조할 수 있어야 한다.
		return 2 * circle.radius;
	｝
};

console.log(circle.getDiameter()); // 10
```

하지만 이런 방식은 일반적이지 않으며 바람직하지도 않다.

생성자 함수 방식으로 인스턴스를 생성하는 경우를 생각해보자.

```jsx
function Circle(radius) {
	// 이 시점에는 생성자 함수 자신이 생성할 인스턴스를 가리키는 식별자를 알 수 없다.
	????.radius = radius;
｝

Circle.prototype.getDiameter = function () {
	// 이 시점에는 생성자 함수 자신이 생성할 인스턴스를 가리키는 식별자를 알 수 없다.
	return 2 * ????.radius;
};

// 생성자 함수로 인스턴스를 생성하려면 먼저 생성자 함수를 정의해야 한다.
const circle = new Circle(5);
```

생성자 함수를 정의하는 시점에는 아직 인스턴스를 생성하기 이전이므로 생성자 함수가 생성할 인스턴스를 가리키는 식별자를 알 수 없다.

따라서 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 특수한 식별자가 필요하다. 그것을  자바스크립트는 `this` 라는 특수한 식별자를 제공한다.

**this는 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 자기 참조 변수다. this를 통해 자신이 속한 객체 또는 자신이 생성할 인스턴스의 프로퍼티나 메서드를 참조할 수 있다.**

코드 어디서든 참조할 수 있으며 this 바인딩은 함수 호출 방식에 의해 **동적으로 결정**된다.

<aside>
💡 this 바인딩
바인딩이란 식별자와 값을 연결하는 과정을 의미.
예를 들어, 변수 선언은 식별자와 확보된 메모리 공간의 주소를 바인딩 하는 것.
this 바인딩은 this와 this가 가리킬 객체를 바인딩 하는 것.

</aside>

위 예시들를 this를 사용해 수정해 본다면,

```jsx
// 객체 리터럴
const circle = {
	radius: 5,
	getDiameter() {
		// this는 메서드를 호출한 객체, 즉 circle을 가리킨다.
		return 2 * this.radius;
	｝
};

console.log(circle.getDiameter()); // 10
```

```jsx
function Circle(radius) {
	// this는 생성자 함수가 생성할 인스턴스를 가리킨다.
	this.radius = radius;
｝

Circle.prototype.getDiameter = function () {
	// this는 생성자 함수가 생성할 인스턴스를 가리킨다.
	return 2 * this.radius;
};

// 인스턴스 생성
const circle = new Circle(5);
console.log(circle.getDiameter()); // 10
```

이처럼 this는 상황에 따라 가리키는 대상이 다르다.

```jsx
// this는 어디서든지 참조 가능하다.
// 전역에서 this는 전역 객체 window를 가리킨다.
console.log(this); // window

function square(number) {
	// 일반 함수 내부에서 this는 전역 객체 window를 가리킨다.
	console.log(this); // window
	return number * number;
}

square (2);

const person = {
	name: 'yooni',
	getName() {
		// 메서드 내부에서 this는 메서드를 호출한 객체를 가리킨다.
		console.log(this); // {name: "yonni", getName: [Function: getName] }
		return this.name;
	}
};

console.log(person.getName()); // yooni

function Person(name) {
	this.name = name;
	// 생성자 함수 내부에서 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
	console.log(this); // Person {name: "yooni"}
}

const me = new Person('yooni');
```

일반 함수 내부에서는 this를 사용할 필요가 없고 undefined가 바인딩되서 객체의 메서드 내부 또는 생성자 함수 내부에만 의미가 있다.

## 22.2 함수 호출 방식과 this 바인딩

this 바인딩은 함수가 어떻게 호출되었는지에 따라 동적으로 결정된다.

### 일반 함수 호출

기본적으로 this에는 전역 객체가 바인딩된다.

일반 함수로 호출하면 함수 내부의 this에는 전역 객체가 바인딩 된다.

```jsx
function foo() {
	console.log("foo's this: ", this); // window
	function bar() {
	console.log("bar's this: ", this); // window
	}
	bar();
}
foo();
```

다음 예제처럼 strict mode가 적용된 일반 함수 내부의 this에는 undefined가 바인딩 된다.

```jsx
// 중첩 함수 예시
function foo() {
'use strict';

	console.log("foo's this: ", this); // undefined
	function bar() {
	console.log("bar's this: ", this); // undefined
	}
	bar();
}
foo();
```

```jsx
// 콜백 함수 예시
var value = 1;

const obj = {
	value: 100,
	foo() {
		console.log("foo's this: ", this); // {value: 100, foo: [Function: foo] }
		// 콜백 함수 내부의 this에는 전역 객체가 바인딩된다.
		setTimeout (function () {
			console.log("callback's this: ", this); // window
			console.log("callback's this.value: ", this.value); // 1
		}, 100);
	}
};

obj.foo();
```

메서드 내부의 중첩 함수나 콜백 함수도 일반 함수로 호출되면 함수 내부의 this 또한 전역 객체가 바인딩 된다.

그래서 메서드의 this 바인딩과 일치시키기 위한 방법은 다음과 같다.

```jsx
var value = 1;

const obj = {
	value: 100,
	foo() {
		// this 바인딩(obj)을 변수 that에 할당한다.
		const that = this;
		
		//콜백 함수 내부에서 this 대신 that을 참조한다.
		setTimeout(function () {
			console.log(that.value); // 100
		}, 100);
	}
};

obj.foo();
```

또는 화살표 함수를 사용해서 this 바인딩을 일치시킬 수도 있다.

```jsx
var value = 1;

const obj = {
	value: 100,
	foo() {
		// 화살표 함수 내부의 this는 상위 스코프의 this를 가리킨다.
		setTimeout(() => console.log(this.value), 100); //100
	}
};

obj.foo();
```

### 메서드 호출

메서드 내부의 this는 메서드를 소유한 객체가 아닌 메서드를 호출한 객체에 바인딩 된다는 것이다.

```jsx
const person = {
	name: 'yooni',
	getName() {
		// 메서드 내부의 this는 메서드를 호출한 객체에 바인딩된다.
		return this.name;
	}
};

// 메서드 getName을 호출한 객체는 person이다.
console.log(person.getName()): // yooni
```

즉, person 객체의 ***getName 프로퍼티*** 가 가리키는 함수 객체는 person 객체에 포함된 것이 아니라 `독립적으로 존재하는 별도의 객체` 이다. getName 프로퍼티가 함수 객체를 가리키고 있을 뿐이다.

따라서 getName 메서드는 다른 객체의 프로퍼티에 할당하는 것으로 ***다른 객체의 메서드가 될 수 있고*** 일반 변수에 할당하여 일반 함수로 호출될 수도 있다.

```jsx
const anotherPerson = {
	name: 'Kim'
};
// getName 메서드를 anotherPerson 객체의 메서드로 할당
anotherPerson.getName = person.getName;

// getName 메서드를 호출한 객체는 anotherPerson이다.
console.log(anotherPerson.getName()); // Kim

// getName 메서드를 변수에 할당
const getName = person.getName;

// getName 메서드를 일반 함수로 호출
console.log(getName()); // ''
// 일반 함수로 호출된 getName 함수 내부의 this.name은 브라우저 환경에서 window.name과 같다.
// 브라우저 환경에서 window.name은 브라우저 창의 이름을 나타내는 빌트인 프로퍼티이며 기본값은 ''이다.
// Node.js 환경에서 this.name은 undefined 이다.
```

따라서 메서드 내부의 this는 프로퍼티로 메서드를 가리키고 있는 객체와는 관계가 없고 메서드를 호출한 객체에 바인딩된다. 

프로토타입 메서드 내부에서 사용된 this도 일반 메서드와 마찬가지로 해당 메서드를 호출한 객체에 바인딩 된다.

### 생성자 함수 호출

생성자 함수 내부의 this에는 생성자 함수가 미래에 생성할 인스턴스가 바인딩 된다.

생성자 함수는 이름 그대로 객체(인스턴스)를 생성하는 함수다. 일반 함수와 동일한 방법으로 생성자 함수를 정의하고 new 연산자와 함께 호출하면 해당 함수는 생성자 함수로 동작한다. 만약 new 연산자와 함께 생성자 함수를 호출하지 않으면 생성자 함수가 아니라 일반 함수로 동작 한다.

### Function.prototype.aplly/call/bind 메서드에 의한 간접 호출

apply, call, bind 메서드는 Fnction.prototpye의 메서드이다. 즉, 이러한 메서드는 모든 함수가 상속받아 사용할 수 있다.

Function.prototype.apply, Function.prototype.call 메서드는 this로 사용할 객체와 인수 리스트를 인수로 전달받아 함수를 호출한다. apply와 call 메서드의 사용법은 다음과 같다.

```jsx
// apply와 call 메서드의 사용법
/**
	* 주어진 this 바인딩과 인수 리스트 배열을 사용하여 함수를 호출한다.
	* @param thisArg - this로 사용할 객체
	* @param argsArray - 함수에게 전달할 인수 리스트의 배열 또는 유사 배열 객체
	* @returns 호출된 함수의 반환값 
*/
Function.prototype.apply(thisArg[, argsArray])
/**
	* 주어진 this 바인딩과 ,로 구분된 인수 리스트를 사용하여 함수를 호출한다.
	* @param thisArg - this로 사용할 객체
	* @param arg1, arg2, ... - 함수에게 전달할 인수 리스트
	* @returns 호출된 함수의 반환값 
	*/
Function.prototype.call (thisArg[, arg1l[, arg2[, ...]]])
```

Ex.

```jsx
function getThisBinding() {
	return this;
｝

// this로 사용할 객체
const thisArg = { a: 1 };

console.log(getThisBinding()); // window

// getThisBinding 함수를 호출하면서 인수로 전달한 객체를 getThisBinding 함수의 this에 바인딩한다.
console.log(getThisBinding.apply(thisArg)); // {a: 1}
console.log(getThisBinding.call(thisArg)); // {a: 1}
```

apply와 call 메서드의 본질적인 기능은 함수를 호출하는 것이다.

bind 메서드는 메서드의 this와 메서드 내부의 중첩 함수 또는 콜백 함수의 this가 불일치하는 문제를 해결하기 위해 사용한다.

Ex.

```jsx
const person = {
	name: 'yooni',
	foo(callback) {
		setTimeout(callback, 100);
	}
};

person.foo(function () {
	console.log(`Hi! my name is ${this.name}.`);
});
/*
Hi! my name is .
*/
```

this가 window와 방인딩 되는 현상이 일어나므로 bind를 이용하여 해결 가능하다.

```jsx
const person = {
	name: 'yooni',
	foo(callback) {
		setTimeout(callback.bind(this), 100);
	}
};

person.foo(function () {
	console.log(`Hi! my name is ${this.name}.`);
});
/*
Hi! my name is yooni.
*/
```

## 정리!

| 함수 호출 방식 | this 바인딩 |
| --- | --- |
| 일반 함수 호출 | 전역 객체 |
| 메서드 호출 | 메서드를 호출한 객체 |
| 생성자 함수 호출 | 생성자 함수가 미래에 생성할 인스턴스 |
| Function.prototype.apply/call/bind
메서드에 의한 간접 호출 | Function.prototype.apply/call/bind 메서드에 
첫 번째 인수로 전달한 객체 |

<br><br><br>

# 23장. 실행 컨텍스트

## 23.1 소스코드의 타입

ECMAScript(에크마 스크립트) 사양은 소스코드(ECMAScript code)를 4가지 타입으로 구분.  
4가지 타입의 소스코드는 실행 컨텍스트를 생성

- **전역 코드**
전역에 존재하는 소소코드를 말함. 전역에 정의된 함수, 클래스 등의 내부 코드는 포함되지 않음.
- **함수 코드**
함수 내부에 존재하는 소스코드를 말함. 함수 내부에 중첩된 함수, 클래스 등의 내부 코드는 포함되지 않음.
- **eval 코드**
빌트인 전역 함수인 eval 함수에 인수로 전달되어 실행되는 소스코드를 말함.
- **모듈 코드**
모듈 내부에 존재하는 소스코드를 말함. 모듈 내부의 함수, 클래스 등의 내부 코드는 포함되지 않는다.

4가지 타입으로 구분하는 이유는 소스코드의 타입에 따라 실행 컨텍스트를 생성하는 과정과 관리 내용이 다르기 때문이다.

## 23.2 소스코드의 평가와 실행

- 소스코드의 평가
    1. 실행 컨텍스트 생성
    2. 변수, 함수 등의 선언문만 먼저 실행 후 실행 컨텍스트가 관리하는 스코프에 등록
- 소스코드 실행
    1. 선언문을 제외한 소스코드가 순차적으로 실행 = 런타임 시작
    2. 스코프에서 검색하여 필요한 정보 취득
    3. 변수 값 변 경 등 소스코드의 실행 결과는 다시 스코프에 등록

## 23.3 실행 컨텍스트의 역할

스코프, 식별자, 코드 실행 순서등의 관리가 필요한데 이것이 **실행 컨텍스트**이다.  
실행 컨텍스트는 소스코드를 실행하는 데 필요한 환경을 제공하고, 코드의 실행 결과를 실제로 관리하는 영역이다!

즉, 식별자를 등록하고 관리하는 스코프와 코드 실행 순서 관리를 구현한 내부 메커니즘으로, 모든 코드는 실행 컨텍스트를 통해 실행되고 관리된다.

- 식별자와 스코프 → 실행 컨텍스트의 **렉시컬 환경**으로 관리
- 코드 실행 순서 → **실행 컨텍스트 스택**으로 관리

## 23.4 실행 컨텍스트 스택

생성된 실행 컨텍스트는 스택(stack) 자료구조로 관리한다. = 실행 컨텍스트 스택

## 23.5 렉시컬 환경

= 스코프를 구분하여 식별자를 등록하고 관리하는 저장소 역할

- 환경 레코드(Environment Record)
    - 스코프에 포함된 식별자 등록
    - 등록된 식별자에 바인딩된 값을 관리하는 저장소
- 외부 렉시컬 환경에 대한 참조(Outer Lexical Environment Reference)
    - 외부 렉시컬 환경에 대한 참조는 상위 스코프를 가리킴

## 23.6 실행 컨텍스트의 생성과 식별자 검색 과정

### 전역 객체 생성

전역 객체는 전역 코드가 평가되기 이전에 생성된다.

전역 객체도 Object.prototype을 상속 받는다 = 전역 객체도 프로토타입 체인의 일원

### 전역 코드 평가

소스코드가 로드되면 자바스크립트 엔진은 전역 코드를 평가한다.

1. 전역 실행 컨텍스트 생성
2. 전역 렉시컬 환경 생성
    1. 전역 환경 레코드 생성
        1. 객체 환경 레코드 생성
        2. 선언적 환경 레코드 생성
    2. this 바인딩
    3. 외부 렉시컬 환경에 대한 참조 결정

### 전역 코드 실행

전역 코드 평가가 끝나면 전역 코드가 순차적으로 실행되기 시작한다.

식별자 결정을 위해 식별자를 검색할 때는 실행 중인 실행 컨텍스트에 식별자를 검색하기 시작한다.

선언된 식별자는 실행 컨텍스트의 렉시컬 환경의 환경 레코드에 등록되어 있다.

### 함수 코드 평가

함수가 호출되면 전역 코드의 실행을 일시 중단하고 함수 내부로 코드의 제어권이 이동한다. 그리고 함수 코드를 평가하기 시작한다.

1. 함수 실행 컨텍스트 생성
2. 함수 렉시컬 환경 생성
    1. 함수 환경 레코드 생성
    2. this 바인딩
    3. 외부 렉시컬 환경에 대한 참조 결정

### 함수 코드 실행

함수 코드 평가가 끝나면 함수 코드를 실행한다. = 런타임 시작

### 중첩 함수 코드 평가

함수 코드 안에 중첩되어 있는 함수가 있다면, **중첩 함수 호출 시** 현재 함수를 일시 중단 시키고 중첩 함수 내부로 코드의 제어권이 이동한다.

### 중첩 함수 코드 실행

똑같이 런타임이 시작된다.

1. console 식별자 검색
    1. 없다면 상위 함수에서 console 식별자 검색
    2. 또 없다면 상위 스코프에서 console 식별자를 검색한다.
2. log 메서드 검색
3. 표현식 평가 (console.log 메서드에 전달할 인수)
4. console.log 메서드 호출

### 중첩 함수 코드 실행 종료

console.log 메서드가 호출되고 종료하면 중첩 함수 코드의 실행이 종료되고, 상위 함수가 다시 실행 컨텍스트가 된다. 그리고 중첩 함수는 실행 컨텍스트에서 제거되지만, 중첩 함수 렉시컬 환경까지 즉시 소멸하는 것은 아니다. 객체를 포함한 모든 값은 누군가에 의해 참조되지 않을 때 가비지 컬렉터에 의해 메모리 공간 확보가 해제되어 소멸한다. 만약 중첩 함수가 소멸되었다 하더라도 누군가 중첩 함수 렉시컬 환경을 누군가 참조하고 있다면, 중첨 함수 렉시컬 환경은 소멸하지 않는다.

### 함수 코드 실행 종료

중첩 함수가 종료하면 더 이상 실행할 코드가 없으므로 현재 함수 코드도 실행이 종료 된다.

### 전역 코드 실행 종료

전역 식행 컨텍스트도 실행 컨텍스트 스택에서 제거된다.

## 23.7 실행 컨텍스트와 블록 레벨 스코프

var 키워드와 다르게 let, const 키워드는 블록 레벨 스코프를 따른다. 그래서 함수, if 문 등 모든 코드 블록에서 let 또는 const를  사용하면 그 코드 블록은 새로운 렉시컬 환경을 생성한다.