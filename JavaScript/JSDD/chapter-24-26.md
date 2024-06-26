# 24장. 클로저

MDN왈: 클로저는 함수와 그 함수가 선언된 렉시컬 환경과의 조합이다.

### 렉시컬 스코프

자바스크립트 엔진은 함수를 어디서 호출했는지가 아니라 함수를 어디에 정의했는지에 따라 상위 스코프를 결정한다. 이를 렉시컬 스코프(정적 스코프)라 한다.

### 함수 객체의 내부 슬롯 [[Environment]]

함수는 자신의 내부 슬롯 [[Environment]]에 자신이 정의된 환경, 즉 상위 스코프의 참조를 저장한다.

### 클로저와 렉시컬 환경

외부 함수보다 중첩 함수가 더 오래 유지되는 경우 중첩 함수는 이미 생명 주기가 종료한 외부 함수의 변수를 참조할 수 있다. 이러한 중첩 함수를 클로저(closure)라 부른다.

클로저는 중첩 함수가 상위 스코프의 식별자를 참조하고 있고 중첩 함수가 외부 함수보다 더 오래 유지되는 경우에 한정하는 것이 일반적이다.

### 클로저의 활용

클로저는 상태(state)를 안전하게 변경하고 유지하기 위해 사용한다. = 상태를 안전하게 은닉하고 특정 함수에게만 상태 변경을 허용하기 위해 사용


<br><br><br>

# 25장. 클래스

클래스는 함수이며 기존 프로토타입 기반 패턴을 클래스 기반 패턴처럼 사용할 수 있도록 하는 새로운 객체 생성 메커니즘이다.

클래스는 생성자 함수보다 엄격하며 생성자 함수에서는 제공하지 않는 기능도 제공한다.

## 클래스 정의

- class 키워드 사용하여 정의
- 파스칼 케이스 사용이 일반적
- 표현식으로도 정의 가능 = 일급 객체

```jsx
// 클래스 선언문
class Person {
	// 생성자
	constructor(name) {
		// 인스턴스 생성 및 초기화
		this.name = name; // name 프로퍼티는 public하다.
	}

	// 프로토타입 메서드
	sayHi() { console.log(`Hi! My name is ${this.name}`) };

	// 정적 메서드
	static sayHello() { console.log('Hello') };
}

// 인스턴스 생성
const me = new Person('yooni');

// 인스턴스의 프로퍼티 참조
console.log(me.name); // yooni

// 프로토타입 메서드 호출
me.sayHi(); // Hi! My name is yooni

// 정적 메서드 호출
me.sayHello(); // TypeError, 정적메서드는 인스턴스로 호출 못함.
Person.sayHello(); // Hello! (항상 클래스로 호출 해야 한다)
```

|  | 클래스 | 생성자 함수 |
| --- | --- | --- |
| new 연산자 없이 호출 시 | 에러 발생 | 일반 함수로서 호출됨 |
| 상속을 지원하는 extends 와 super 키워드 제공 | 지원 함 | 지원 안함 |
| 호이스팅 | TDZ에 빠짐 | 발생 |
| strict mode | 암묵적 설정 | 직접 설정 |
| constructor, 프로토타입 메서드, 정적 메서드 | 프로퍼트 어트리뷰터 [[Enumerable]] = false | 프로퍼트 어트리뷰터 [[Enumerable]] = true |

### 정적 메서드 vs. 프로토타입 메서드

- 정적 메서드와 프로토타입 메서드는 자신이 속해 있는 프로토타입 체인이 다름
- 정적 메서드는 클래스로 호출, 프로토타입 메서트는 인스턴스로 호출
- 정적 메서드는 인스턴스 프로퍼티 참조 불가, 프로토타입 메서드는 인스턴스 프로퍼티 참조 가능

### 상속에 의한 클래스 확장

프로토타입 기반 상속과 다른 개념임. 기존 클래스를 상속받아 새로운 클래스를 확장(extends)하여 정의하는 것이다.

<br><br><br>

# 26장. ES6 함수의 추가 기능

- ES6 이전의 모든 함수는 일반함수로서 호출할 수 있는 것은 물론, 생성자 함수로서도 호출이 가능하다.
- ES6 사양에서 메서드는 메서드 축약 표현으로 정의된 함수만을 의미한다. 그리고 그 메서드는 인스턴스를 생성할 수 없는 non-constructor다.
- ES6 메서드는 자신을 바인딩한 객체를 가리키는 내부 슬롯 [[HomeObject]]를 갖는다.
- `화살표 함수`는 function 키워드 대신 화살표( ⇒, fat arrow)를 사용하는 함수이며, 인스턴스를 생성할 수 없는 non-constructor다. 일반함수와 다르게 중복된 매개변수 이름을 선언할 수 없다. 그리고 this, super, arguments 바인딩을 갖지 않아서 일반함수와 다르게 동작한다.
- `Rest 파라미터` 는 함수에 전달된 **인수들의 목록을 배열로** 전달받는다.
- 매개변수에 기본값을 줄 수 있다.
