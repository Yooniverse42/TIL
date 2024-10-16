# Timeline

- [1. 타임라인이 중요한 이유](#1-타임라인이-중요한-이유)
- [2. Position Parameter](#2-position-parameter)

## 1. 타임라인이 중요한 이유

```jsx
gsap.from(".sun", { duration: 1, opacity: 0, x: 50, y: 50 });
gsap.from(".gress", { delay: 1, duration: 1, opacity: 0, y: 100 });
gsap.from(".bird", { delay: 2, duration: 1, opacity: 0, y: 100 });
gsap.from(".music", { delay: 3, duration: 1, opacity: 0, x: 100, y: 100 });
```

`delay`를 계산하여 하나씩 직접 넣어줘야한다.

만약 중간 요소의 `delay`시간이 바뀐다면 뒤에 모든 요소들도 하나하나 바꿔줘야 한다.. so 귀찮..

그래서 타임라인을 쓰는 이유!

```jsx
let tl = gsap.timeline();

tl.from(".sun", { duration: 1, opacity: 0, x: 50, y: 50 });
tl.from(".gress", {
  duration: 1,
  opacity: 0,
  y: 100,
  stagger: {
    each: 0.2,
    from: "center",
  },
});
tl.from(".bird", { duration: 1, opacity: 0, y: 100 });
tl.from(".music", { duration: 1, opacity: 0, x: 100, y: 100 });
```

delay를 준 것과 똑같이 순서대로 작동한다.

## 2. Position Parameter

GSAP의 타임라인을 사용하면 기본적으로 한 애니메이션이 끝난 후 다음 애니메이션이 시작된다. 예를 들어, 첫 번째 요소의 `duration`이 2초라면, 두 번째 요소의 애니메이션은 2초 후에 시작될 것이다. 하지만 만약 첫 번째 요소가 2초 동안 진행되는 동안, 두 번째 요소의 애니메이션을 1초 후에 시작하고 싶다면?! 이때 필요한 것이 <mark>Position Parameter</mark>이다. **Position Parameter**를 통해 타임라인 상의 애니메이션 시작 시점을 더 세밀하게 제어 가능하다.

- 기본

```jsx
let animation = gsap.timeline();
animation
  .to("#one", { duration: 1, x: 1150 })
  .to("#two", { duration: 2, x: 1150 })
  .to("#three", { duration: 1, x: 1150 })
  .to("#four", { duration: 1, x: 1150 });
```

- relative(상대적)

```jsx
let animation = gsap.timeline();
animation
  .to("#one", { duration: 1, x: 1150 })
  .to("#two", { duration: 2, x: 1150 }, "+=1") // one끝나고 1초 뒤 시작
  .to("#three", { duration: 1, x: 1150 }, "-=0.5") // two끝나기 0.5초 전 시작
  .to("#four", { duration: 1, x: 1150 }, "<"); // three와 똑같이 시작
```

- absolute(절대적)

```jsx
let animation = gsap.timeline();
animation
  .to("#one", { duration: 1, x: 1150 })
  .to("#two", { duration: 2, x: 1150 })
  .to("#three", { duration: 1, x: 1150 })
  .to("#four", { duration: 1, x: 1150 }, 1); // 앞 요소들이 안나와도 무조건 1초부터 시작
```
