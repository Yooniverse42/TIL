# 16장. 프로퍼티 어트리뷰트

## 16.1 내부 슬롯과 내부 메서드

이중 대괄호 `([[…]])` 로 감싼 이름들이 내부 슬롯과 내부 메서드.  
but. 개발자가 직접 접근 못함. → 그래서 간접적으로 접근해야 함.  
ex. `[[Prototype]]` 의 내부 슬롯의 경우, `__proto__` 를 통해 간접적으로 접근

<br>

## 16.2 프로퍼티 어트리뷰트와 프로퍼티 디스크립터 객체

**프로퍼티를 생성할 때 프로퍼티 어트리뷰트를 기본값으로 자동 정의함.**

프로퍼티 어트리뷰트? 자바스크립트 엔진이 관리하는 내부 상태 값인 내부 슬롯.
⇒ `[[Value]]` , `[[Writable]]` , `[[Enumerable]]` , `[[Configurable]]`

그래서 object.getOwnProperyDescriptor 메서드를 사용하여 간접적으로 확인 가능

```jsx
const person = {
  name: "Lee",
};

// 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체를 반환
console.log(Object.getOwnPropertyDescriptor(person, "name"));
// person: 객체 참조를 전달, 'name': 프로퍼티 키를 문자열로 전달
// {value: "Lee", wriable: true, enumerable: true, configurable: true}
```

<br>

## 16.3 데이터 프로퍼티와 접근자 프로퍼티

- 프로퍼티
  - 데이터 프로퍼티
  - 접근자 프로퍼티

### 데이터 프로퍼티

```jsx
const person = {
  name: "Lee",
};

// 프로퍼티 동적 생성
person.age = 20;

console.log(Object.getOwnPropertyDescriptor(person));
/* 
{
	name: {value: "Lee", wriable: true, enumerable: true, configurable: true},
	age: {value: 20, wriable: true, enumerable: true, configurable: true}
}
*/
```

⇒ 프로퍼티가 생성될 때 `[[Value]]` 의 값은 **프로퍼티 값으로 초기화** 되며 `[[Writable]]` , `[[Enumerable]]` , `[[Configurable]]` 의 값은 true로 초기화 된다!

### 접근자 프로퍼티

- 자체적으로 값을 갖지 않음.
- 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 **접근자 함수로 구성된 프로퍼티**

접근자 함수는 getter/setter 함수라고도 부름. 모두 정의 또는 하나만 정의 가능

<br>

## 16.4 프로퍼티 정의

= 새로운 프로퍼티를 추가하면서 프로퍼티 어트리뷰트를 명시적으로 정의하거나,  
기존 프로퍼티의 프로퍼티 어트리뷰트를 재정의 하는 것.  
ex. 프로퍼티 값을 갱신 가능하도록 할 것인가? 프로퍼티를 열거 가능하도록? 프로퍼티 재정의 가능하도록? 등  
`Object.defineProperty` 메서드 사용하면 프로퍼티의 어트리뷰트 정의 가능

```jsx
const person = {};

// 데이터 프로퍼티 정의
Object.defineProperty(person, "firstName", {
  value: "yooni",
  writable: true,
  enumerable: true,
  configurable: true,
});

Object.defineProperty(person, "lastName", {
  value: "Kim",
});

let descriptor = Object.getOwnPropertyDescriptor(person, "firstName");
console.log("firstName", descriptor);
// firstName {value: "yooni", writable: true, enumerable: true, configurable: true}

// 디스크립터 객체의 프로퍼티를 누락시키면 **undefined, false가 기본값**이다.
descriptor = Object.getOwnPropertyDescriptor(person, "lastName");
console.log("lastName", descriptor);
// lastName {value: "Kim", writable: false, enumerable: false, configurable: false}

// [[Enumerable]]의 값이 false인 경우
// 해당 프로퍼티는 for... in 문이나 Object.keys 등으로 열거할 수 없다.
// lastName 프로퍼티는 [[Enumerable]]의 값이 false이므로 열거되지 않는다.
console.log(Object.keys(person)); // ["firstName"]

// [[Writable]]의 값이 false인 경우 해당 프로퍼티의 [[Value]]의 값을 변경할 수 없다.
// lastName 프로퍼티는 [[Writable]]의 값이 false이므로 값을 변경할 수 없다.
// 이때 값을 변경하면 에러는 발생하지 않고 무시된다.
person.lastName = "Kim";

// [[Configurable]]의 값이 false인 경우 해당 프로퍼티를 삭제할 수 없다.
// lastname 프로퍼티는 [[Configurable]]의 값이 false이므로 삭제할 수 없다.
// 이때 프로퍼티를 삭제하면 에러는 발생하지 않고 무시된다.
delete person.lastName;

// [[Configurable]]의 값이 false인 경우 해당 프로퍼티를 재정의할 수 없다.
// Object.defineProperty(person, 'lastName', { enumerable: true });
// Uncaught TypeError: Cannot redefine property: lastName

descriptor = Object.getOwnPropertyDescriptor(person, "lastName");
console.log("lastName", descriptor);
// lastName {value: "Kim", writable: false, enumerable: false, configurable: false}

// 접근자 프로퍼티 정의
Object.defineProperty(person, "fullName", {
  // getter 함수
  get() {
    return `${this.firstName} ${this.lastName}`;
  },
  // setter 함수
  set(name) {
    [this.firstName, this.lastName] = name.split(" ");
  },
  enumerable: true,
  configurable: true,
});

descriptor = Object.getOwnPropertyDescriptor(person, "fullName");
console.log("fullName", descriptor);
// fullName {get: f, set: f, enumerable: true, configurable: true}

person.fullName = "Juyeon Lee";
console.log(person); // {firstName: "Juyeon", lastName: "Lee"}
```

`Object.defineProperty` 메서드는 한 번에 하나의 프로퍼티만 정의 가능.

`Object.defineProperties` 메서드는 여러 개의 프로퍼티를 한 번에 정의 가능

```jsx
const person = {};

// 데이터 프로퍼티 정의
Object.defineProperties(person, {
  fistName: {
    value: "yooni",
    writable: true,
    enumerable: true,
    configurable: true,
  },
  lastName: {
    value: "Kim",
    writable: true,
    enumerable: true,
    configurable: true,
  },
  // 접근자 프로퍼티 정의
  fullName: {
    // getter 함수
    get() {
      return `${this.firstName} ${this.lastName}`;
    },
    // setter 함수
    set(name) {
      [this.firstName, this.lastName] = name.split(" ");
    },
    enumerable: true,
    configurable: true,
  },
});

person.fullName = "Juyeon Lee";
console.log(person);
// 책에서는.. {firstName: "Juyeon", lastName: "Lee"}
/* 실제 입력.. 
{
  fistName: 'yooni',
  lastName: 'Lee',
  fullName: [Getter/Setter],
  firstName: 'Juyeon'
}
*/
```

<br>

## 16.5 객체 변경 방지

객체는 변경 가능한 값 = 재할당 없이 직접 변경 가능 = 프로퍼티 추가 or 삭제 or 갱신 가능

| 구분 | 메서드 | 프로퍼티
추가 | 프로퍼티
삭제 | 프로퍼티
값 읽기 | 프로퍼티
값 쓰기 | 프로퍼티 어트리뷰트 재정의 |
| --- | --- | --- | --- | --- | --- | --- |
| 객체 확장 금지 | Object.preventExtensions | X | O | O | O | O |
| 객체 밀봉 | Object.seal | X | X | O | O | X |
| 객체 동결 | Object.freeze | X | X | O | X | X |

### 객체 확장 금지

`Object.preventExtensions` 메서드는 객체의 확장을 금지함. = 프로퍼티 추가 금지

```jsx
const person = {...}
// 사용법
Object.preventExtensions(person);
```

위 코드와 같이 메서드를 사용하게 되면 그 이후에 프로퍼티를 추가하려고 해도 추가되지 않는다.  
확장이 가능한 객체인지 여부는 Object.isExensible 메서드 사용. → 불리언 값 반환

```jsx
console.log(Object.isExensible(person)); // false
```

### 객체 밀봉

`Object.seal` 메서드는 객체를 밀봉함 = 프로퍼티 추가, 삭제, 어트리뷰트 재정의 금지  
밀봉 여부 = Object.isESealed 메서드 사용

### 객체 동결

`Object.freeze` 메서드는 객체를 동결 = 프로퍼티 값 읽기 외 모두 금지  
동결 여부 = Object.isFrozen 메서드 사용

### 불변 객체

객체 확장 금지, 객체 밀봉, 객체 동결 메서드처럼 변경 방지 메서드들은 **얕은 변경 방지**임.  
→ 그래서 Object.freeze 메서드는 중첩 객체까지 동결 불가능  
→ 객체를 값으로 갖는 모든 프로퍼티에 대해 재귀적으로 Object.freeze 메서드 호출

<br>
<br>
<br>

# 17장. 생성자 함수에 의한 객체 생성

다양한 객체 생성 방식 중 하나!

## 17.1 Object 생성자 함수

```jsx
// 빈 객체의 생성
const person = new Object();

// 프로퍼티 추가
person.name = "Kim";
person.sayHello = function () {
  console.log("Hi! My name is " + this.name);
};

console.log(person); // {name: "Kim", sayHello: f}
person.sayHello(); // Hi! My name is Kim
```

생성자 함수(constructor) = new 연산자와 함께 호출하여 **객체(인스턴스)를 생성하는 함수**  
→ 빌트인 생성자 함수 제공

- Object
- String
- Number
- Boolean
- Function
- Array
- Date
- RegExp.
- Promise

등..

> Object 생성자 함수를 사용할 특별한 이유가 없다면 그다지 유용해 보이지 않는다?

<br>

## 17.2 생성자 함수

### 객체 리터럴에 의한 객체 생성 방식의 문제점

- 생성 방식 : 직관적, 간편
- but 단 하나의 객체만 생성

```jsx
const circle1 = {
  radius: 5,
  getDiameter() {
    return 2 * this.radius;
  },
};

console.log(circle1.getDiameter()); // 10

const circle2 = {
  radius: 10,
  getDiameter() {
    return 2 * this.radius;
  },
};

console.log(circle2.getDiameter()); // 20
```

circle1 객체와 circle2 객체는 프로퍼티 구조가 동일 + getDiameter 메서드 완전 동일

### 생성자 함수에 의한 객체 생성 방식의 장점

```jsx
// 생성자 함수
function Circle(radius) {
  // 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// 인스턴스의 생성
const circle1 = new Circle(5); // 반지름이 5인 Circle 객체를 생성
const circle2 = new Circle(10); // 반지름이 10인 Circle 객체를 생성

console.log(circle1.getDiameter()); // 10
console.log(circle2.getDiameter()); // 20
```

> 2개가 아닌 여러 개의 circle 객체를 만들어 낸다면 객체 리터럴에 비해 훨씬 사용하기 좋을 것 같다.

new 연산자와 함께 생성자 함수를 호출하지 않으면 일반 함수로 동작하기에 조심!

### 생성자 함수의 인스턴스 생성 과정

생성자 함수의 역할 = 인스턴스 생성(필수) + 생성된 인스턴스 초기화(옵션)

#### 인스턴스 생성과 this 바인딩

```jsx
function Circle(radius) {
  // 1. 암묵적으로 인스턴스 생성되고 this에 바인딩된다.
  console.log(this); // Circle {}

  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}
```

#### 인스턴스 초기화

this에 바인딩되어 있는 인스턴스를 초기화 함. = 그 인스턴스에 프로퍼티나 메서드 추가, 초기값 할당

```jsx
function Circle(radius) {
  // 1. 암묵적으로 인스턴스 생성되고 this에 바인딩된다.

  // 2. this에 바인딩되어 있는 인스턴스를 초기화 한다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}
```

#### 인스턴스 반환

생성자 함수 내부에서 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 this를 암묵적으로 변환

```jsx
function Circle(radius) {
  // 1. 암묵적으로 인스턴스 생성되고 this에 바인딩된다.

  // 2. this에 바인딩되어 있는 인스턴스를 초기화 한다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
  // 3. 완성된 인스턴스가 바인딩된 this가 암묵적으로 반한환다.
}

// 인스턴스 생성. Circle 생성자 함수는 암묵적으로 this를 반환한다.
const circle = new Circle(1);
console.log(circle); // Circle {radius: 1, getDiameter: f}
```

생성자 함수 내부에 return문이 있으면 this가 아닌 다른 값을 반환하기 때문에 반드시 생략해야함.

### 내부 메서드 [[Call]]과 [[Construct]]

함수는 객체이므로 일반 객체와 동일하기 동작. 그래서 일반 객체가 가지고 있는 내부 슬롯과 내부 메서드 사용 가능  
하지만 일반 객체는 호출할 수 없지만 함수는 호출할 수 있다는 점에서 다름.  
그래서 함수 객체만을 위한 [[Environment]], [[FormalParameters]] 등의 내부 슬롯과 [[Call]],[[Construct]] 같은 내부 메서드를 추가로 가지고 있다.  
함수가 일반 함수로서 호출되면? → 함수 객체의 내부 메서드인 [[Call]] 호출,  
생성자 함수로서 호출되면? → [[Construct]] 호출

```jsx
function foo() {}

// 일반적인 함수 호출: [[Call]]이 호출
foo();

// 생성자 함수 호출: [[Construct]]가 호출
new foo();
```

하지만 모든 함수 객체를 생성자 함수로 호출할 수 있는 것은 아님! = [[Construct]]를 모든 함수 객체가 가지는 것이 아님!  
즉, 호출 모두 가능? but constructor vs. non-constructor 로 나뉨.

### constructor와 non-constructor의 구분

함수 객체를 생성할 때 함수 정의 방식에 따라 구분!

- constructor: 함수 선언문, 함수 표현식, 클래스
- non-constructor: 메서드, 화살표 함수

그래서 메서드와 화살표 함수는 생성자 함수를 호출하지 못한다.

### new 연산자

생성자 함수를 호출하면 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킴!  
하지만 일반적인 함수로서 호출하면 함수 내부의 this는 전역 객체(window)를 가리킴  
그리고 생성자 함수는 파스칼 케이스로 명명하여 일반 함수와 구별할 수 있도록 하자!

```jsx
Ex.
const circle = new Circle();
```

### new.target

생성자 함수가 new 연산자 없이 호출되는 것을 방지하기 위함.  
함수 내부에서 new.target을 사용하면 new 연산자와 함께 생성자 함수로서 호출되었는지 확인 가능!

- new 연산자와 함께 생성? 함수 자신을 가리킴
- 없이 생성? undefined
