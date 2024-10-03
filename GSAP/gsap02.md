# tween control, from drawback

- [1. 트윈 컨트롤](#1-트윈-컨트롤)
- [2. from() 트윈의 문제점](#2-from-트윈의-문제점)

## 1. 트윈 컨트롤

- 트윈을 참조하는 변수를 설정한다.

```jsx
let tween = gsap.to(".box", { x: 600 });
```

- 자동재생 막기(1)

```jsx
let tween = gsap.to(".box", { x: 600, paused: true });
```

- 자동재생 막기(2)

```jsx
tween.pause();

/*
pause뿐만 아니라 여러 형태로 호출 가능
tween.play();
tween.resume();
tween.reverse();
tween.restart();
*/
```

<br/>

## 2. from() 트윈의 문제점

원을 하나 만든 후 마우스를 올리면 원이 작아졌다가 다시 원래 상태로 되돌아오게 만들어보자.

```jsx
circle.addEventListener("mouseenter", () => {
  gsap.from(".circle", { scale: 0 });
});
```

이때 `from()` 트윈의 문제점이 발견할 수 있다.

마우스를 올렸다가 scale이 원래 상태인 1이 되기 전에 마우스를 빼는 동작을 빠르게 반복하게 되면, tween은 원래 상태가 scale이 1이 아닌 더 작은 상태로 인식한다. 그래서 동작을 반복하게 되면 원래상태가 0과 가까워져 더 이상 scale이 1로 돌아오지 않게 된다.

그래서 `fromTo()` 를 이용해야한다!

```jsx
circle.addEventListener("mouseenter", () => {
  gsap.fromTo(".circle", { scale: 0 }, { scale: 1 });
});
```
