# 27장. 배열

배열은 여러 개의 값을 순차적으로 나열한 자료구조다.

배열 리터럴을 통해 생성 가능하다.

```jsx
const arr = ['사과', '바나나', '오렌지'];
```

`‘사과’, '바나나', '오렌지'` 를 요소(element)라 부르며, 각 자신의 위치를 나타내는 인덱스(index)를 갖는다. 그리고 배열의 길이를 나타내는 length 프로퍼티를 갖는다.

| 구분 | 객체 | 배열 |
| --- | --- | --- |
| 구조 | 프로퍼티 키와 프로퍼티 값 | 인덱스와 요소 |
| 값의 참조 | 프로퍼티 키 | 인덱스 |
| 값의 순서 | X | O |
| length 프로퍼티 | X | O |

<br><br>

## 자바스크립트의 배열은 희소 배열이다.

자바스크립트의 배열은 일반적인 의미의 배열과 다르다. 즉, 배열의 요소를 위한 각각의 메모리 공간은 동일한 크기를 갖지 않아도 되며, 연속적으로 이어져 있지 않을 수도 있다. 이런 것을 희소 배열(sparse array)이라 한다.

희소 배열은 length와 배열 요소의 개수가 일치하지 않는다. 희소 배열의 lengths는 희소 배열의 실제 요소 개수보다 언제나 크다.

```jsx
const arr = [1];

arr.length = 3;

console.log(arr); // [1, empth x 2]
```

<br><br>

## 배열 생성법

### 배열 리터럴

```jsx
const arr = [1, 2, 3];
```

### Array 생성자 함수

```jsx
const arr = new Array(3);
console.log(arr); // [empty x 10]
```

이렇게 작성하면 희소배열이 탄생한다. 그래서 아래 처럼 만들면 배열 리터럴 처럼 배열을 생성할 수 있다.

```jsx
// 빈 배열을 만든다.
new Array();
new Array(1, 2, 3); // [1, 2, 3]
```

### Array.of

```jsx
Array.of(1, 2, 3); // [1, 2, 3]
```

### Array.from

`유사 배열 객체` 또는 `이터러블 객체`를 인수로 전달받아 **배열로 변환**하여 반환한다.

```jsx
Array.from({
	length: 3,
	0: 1,
	1: 2,
	2: 3
}) // [1, 2, 3]
```

<br><br>

## Mutator method vs. Accessor method

배열에는 원본 배열(배열 메서드를 호출한 배열, 즉 this가 가리키는 객체)을 직접 변경하는 메서드(mutatormethod)와 원본 배열을 직접 변경하지 않고 새로운 배열을 생성하여 반환하는 메서드(accessor method)가 있다.

### Array.isArray

전달된 인수가 배열이면 true, 아니라면 false 반환

### Array.prototype.indexOf

원본 배열에서 인수로 전달된 요소를 검색하여 인덱스를 반환.

배열에 특정 요소가 존재하는지 확인할 때 유용.

```jsx
const arr = [1, 2, 3, 4];

// 배열에서 요소 2를 검색 후 첫 번째로 검색된 요소의 인덱스를 반환
arr.indexOf(4); // 3

// 없는 요소일 땐 -1 반환
arr.indexOf(5); // -1
```

### Array.prototype.push

원본 배열의 마지막 요소로 추가하고 변경된 length 프로퍼티 값 반환

```jsx
const arr = [1, 2];

arr.push(3, 4); // [1, 2, 3, 4]
```

### Array.prototype.pop

원본 배열에서 마지막 요소를 제거하고 제거된 요소를 반환

```jsx
const arr = [1, 2];

arr.pop(); // 2
```

### Array.prototype.unshift

원본 배열의 선두에 요소를 추가하고 변경된 length 프로퍼티 값 반환

```jsx
const arr = [1, 2];

arr.unshift(3, 4); ; // [3, 4, 1, 2]
```

### Array.prototype.shift

원본 배열의 첫 번재 요소를 제거하고 제거한 요소 반환

```jsx
const arr = [1, 2];

arr.shift(); // 1
```

### Array.prototype.concat

원본 배열의 마지막 요소로 추가한 새로운 배열을 반환

```jsx
const arr1 = [1, 2];
const arr2 = [3, 4];

arr1.concat(arr2); // [1, 2, 3, 4]
```

### Array.prototype.splice

원본 배열의 중간에 요소를 추가하거나 제거하는 경우 사용

```jsx
const arr = [1, 2, 3, 4];

// 인덱스 1부터 2개의 요소를 제거하고 그 자리에 새로운 20, 30을 삽입
arr.splice(1, 2, 20, 30) // [1, 20, 30, 4]
```

### Array.prototype.join

원본 배열의 모든 요소를 문자열로 변환한 후 인수로 전달 받은 구분자로 연결한 문자열을 반환

```jsx
const arr = [1, 2, 3, 4];

arr.join(); // '1,2,3,4'

arr.join(' ') // '1234'

arr.join(':') // '1:2:3:4'
```

### Array.prototype.reverse

원본 재열의 순서를 반대로 뒤집음

```jsx
const arr = [1, 2, 3, 4];

arr.reverse(); // [4, 3, 2, 1]
```

### Array.prototype.fill

인수로 전달받은 값을 배열의 처음부터 끝가지 요소로 채움

```jsx
const arr = [1, 2, 3, 4];

arr.fill(0); // [0, 0, 0, 0]
```

```jsx
const arr = [1, 2, 3, 4];

arr.fill(0, 1, 3); // [1, 0, 0, 4]
```

### Array.prototype.includes

배열 내의 특정 요소가 포함되어 있는지 확인 후 불리언으로 반환

```jsx
const arr = [1, 2, 3, 4];

arr.includes(5); // false
// 요소 1이 포함되어 있는지 인덱스 1부터 확인
arr.includes(1, 1); // false
```

### Array.prototype.flat

인수로 전달한 깊이만큼 재귀적으로 배열을 평탄화함.

```jsx
[1, [2, 3, 4, 5].flat(); // [1, 2, 3, 4, 5]

[1, [2, [3, [4, 5].flat(1); // [1, 2, [3, [4, 5]]]

[1, [2, [3, [4, 5].flat(2); // [1, 2, 3, [4, 5]]]
[1, [2, [3, [4, 5].flat().flat(); // [1, 2, 3, [4, 5]]]

[1, [2, [3, [4, 5].flat(Infinity); // [1, 2, 3, 4, 5]
```

<br><br>

## 배열 고차 함수

고차 함수는  함수를 인수로 전달 받거나 함수를 반환하는 함수임. 불변성을 지향하는 함수형 프로그래밍에 기반을 두고 있음.

함수형 프로그래밍이란, 조건문과 반복문을 제거하고 변수 사용을 억제하여 상태 변경을 피하려는 프로그래밍 패러다임이다.

### Array.prototype.sort

원본 배열을 변경하며, 정렬된 배열을 반환. 기본적으로 오름차순으로 요소 정렬

```jsx
const fruits = ['Banana', 'Orange', 'Apple'];

fruits.sort(); // ['Apple', 'Banana', 'Orange']

// sort 메서드가 원본 배열을 변경해서 바로 reverse 메서드를 사용했을 때 아래처럼 나옴
fruits.reverse(); // ['Orange', 'Banana', 'Apple']
```

숫자 사용 시 주의. 유니코드 코드 포인트의 순서를 따르기 때문에 비교 함수 사용해야한다.

```jsx
[2, 10].sort(); // [10, 2], 유니코드 코드 포인트 순서 따름

// 오름차순 정렬
const points = [40, 100, 1, 5, 2, 25, 10];
points.sort((a, b) => a - b); // [1, 2, 5 ,10, 25, 40, 100]

// 최소/최대값 구하기
console.log(points[0], points[points.length -1])' ; // 1, 100

```

### Array.prototype.forEach

for문은 반복을 위한 변수를 선언해야 하므로 함수형 프로그래밍이 추구하는 바와 맞지 않음.

forEach 메서드는 for문을 대체할 수 있는 고차 함수임.

```jsx
const numbers = [1, 2, 3];
const pows = [];

numbers.forEach(item => pows.push(item ** 2)); // [1, 4, 9]
```

### Array.prototype.map

콜백 함수의 반환값들로 구성된 새로운 배열 반환

```jsx
const numbers = [1, 4, 9];

const roots = numbers.map(item => Math.sqrt(item)); // [1, 2, 3]
```

### Array.prototype.filter

콜백 함수의 반환값이 true인 요소로만 구성된 새로운 배열 반환

```jsx
const numbers = [1, 2, 3, 4, 5];

const odds = numbers.filter(item => item % 2); // [1, 3, 5]
```

### Array.prototype.reduce

콜백 함수를 호출하여 하나의 결과값을 만들어 반환

```jsx
const values = [1, [2, 3], 4, [5, 6]];

const flatten = values.reduce((acc, cur) => acc.concat(cur), []);
// [1, 2, 3, 4, 5, 6]
```

### Array.prototype.some

콜백 함수의 반환값이 단 한번이라도 참이면 true, 모두 거짓이면 false 반환

```jsx
[5, 10, 15].some(item => item > 10); // true

[5, 10, 15].some(item => item > 20); // false
```

### Array.prototype.every

some 메서드와 반대로 콜백 함수의 반환값이 모두 참이면 true, 단 한번이라도 거짓이면 false 반환

```jsx
[5, 10, 15].every(item => item > 3); // true

[5, 10, 15].every(item => item > 20); // false
```

### Array.prototype.find

콜백 함수를 호출하여 반환값이 **true인 첫 번째 요소 반환**, 없으면 undefined

```jsx
const users = [
	{id: 1, name: 'kim'},
	{id: 2, name: 'Lee'},
	{id: 3, name: 'Park'}
]

users.find(u => u.id === 2); // {id: 2, name: 'Lee'}
```

### Array.prototype.findIndex

콜백 함수를 호출하여 반환값이 **true인 첫 번째 인덱스 반환**, 없으면 -1

```jsx
const users = [
	{id: 1, name: 'kim'},
	{id: 2, name: 'Lee'},
	{id: 3, name: 'Park'}
]

users.findIndex(u => u.id === 2); // 1
```

### Array.prototype.flatMap

map 메서드를 통해 생성된 새로운 배열을 평탄화함 = map + flat

```jsx
const arr = ['hello', 'world'];

arr.map(x => x.split('')).flat();
// ['h', 'e', 'l', 'l', 'o', 'w', 'o', 'r', 'l', 'd']

/* 위와 아래 코드 동일한 값 반환*/
arr.flatMap(x => x.split(''));
// ['h', 'e', 'l', 'l', 'o', 'w', 'o', 'r', 'l', 'd']
```

<br>

## 배열 고차 함수

고차 함수는  함수를 인수로 전달 받거나 함수를 반환하는 함수임. 불변성을 지향하는 함수형 프로그래밍에 기반을 두고 있음.

함수형 프로그래밍이란, 조건문과 반복문을 제거하고 변수 사용을 억제하여 상태 변경을 피하려는 프로그래밍 패러다임이다.

### Array.prototype.sort

원본 배열을 변경하며, 정렬된 배열을 반환. 기본적으로 오름차순으로 요소 정렬

```jsx
const fruits = ['Banana', 'Orange', 'Apple'];

fruits.sort(); // ['Apple', 'Banana', 'Orange']

// sort 메서드가 원본 배열을 변경해서 바로 reverse 메서드를 사용했을 때 아래처럼 나옴
fruits.reverse(); // ['Orange', 'Banana', 'Apple']
```

숫자 사용 시 주의. 유니코드 코드 포인트의 순서를 따르기 때문에 비교 함수 사용해야한다.

```jsx
[2, 10].sort(); // [10, 2], 유니코드 코드 포인트 순서 따름

// 오름차순 정렬
const points = [40, 100, 1, 5, 2, 25, 10];
points.sort((a, b) => a - b); // [1, 2, 5 ,10, 25, 40, 100]

// 최소/최대값 구하기
console.log(points[0], points[points.length -1])' ; // 1, 100

```

### Array.prototype.forEach

for문은 반복을 위한 변수를 선언해야 하므로 함수형 프로그래밍이 추구하는 바와 맞지 않음.

forEach 메서드는 for문을 대체할 수 있는 고차 함수임.

```jsx
const numbers = [1, 2, 3];
const pows = [];

numbers.forEach(item => pows.push(item ** 2)); // [1, 4, 9]
```

### Array.prototype.map

콜백 함수의 반환값들로 구성된 새로운 배열 반환

```jsx
const numbers = [1, 4, 9];

const roots = numbers.map(item => Math.sqrt(item)); // [1, 2, 3]
```

### Array.prototype.filter

콜백 함수의 반환값이 true인 요소로만 구성된 새로운 배열 반환

```jsx
const numbers = [1, 2, 3, 4, 5];

const odds = numbers.filter(item => item % 2); // [1, 3, 5]
```

### Array.prototype.reduce

콜백 함수를 호출하여 하나의 결과값을 만들어 반환

```jsx
const values = [1, [2, 3], 4, [5, 6]];

const flatten = values.reduce((acc, cur) => acc.concat(cur), []);
// [1, 2, 3, 4, 5, 6]
```

### Array.prototype.some

콜백 함수의 반환값이 단 한번이라도 참이면 true, 모두 거짓이면 false 반환

```jsx
[5, 10, 15].some(item => item > 10); // true

[5, 10, 15].some(item => item > 20); // false
```

### Array.prototype.every

some 메서드와 반대로 콜백 함수의 반환값이 모두 참이면 true, 단 한번이라도 거짓이면 false 반환

```jsx
[5, 10, 15].every(item => item > 3); // true

[5, 10, 15].every(item => item > 20); // false
```

### Array.prototype.find

콜백 함수를 호출하여 반환값이 **true인 첫 번째 요소 반환**, 없으면 undefined

```jsx
const users = [
	{id: 1, name: 'kim'},
	{id: 2, name: 'Lee'},
	{id: 3, name: 'Park'}
]

users.find(u => u.id === 2); // {id: 2, name: 'Lee'}
```

### Array.prototype.findIndex

콜백 함수를 호출하여 반환값이 **true인 첫 번째 인덱스 반환**, 없으면 -1

```jsx
const users = [
	{id: 1, name: 'kim'},
	{id: 2, name: 'Lee'},
	{id: 3, name: 'Park'}
]

users.findIndex(u => u.id === 2); // 1
```

### Array.prototype.flatMap

map 메서드를 통해 생성된 새로운 배열을 평탄화함 = map + flat

```jsx
const arr = ['hello', 'world'];

arr.map(x => x.split('')).flat();
// ['h', 'e', 'l', 'l', 'o', 'w', 'o', 'r', 'l', 'd']

/* 위와 아래 코드 동일한 값 반환*/
arr.flatMap(x => x.split(''));
// ['h', 'e', 'l', 'l', 'o', 'w', 'o', 'r', 'l', 'd']
```
