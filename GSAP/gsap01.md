# GSAP - Basic Tween

- [1. GSAP을 공부하게 된 이유](#1-GSAP을-공부하게-된-이유)
- [2. What is GSAP?](#2-What-is-GSAP)
- [3. GSAP Object](#3-GSAP-Object)
  - [3-1. Tweens](#3-1-Tweens)
  - [3-2. Staggers](#3-2-Staggers)
  - [3-3. Timeline](#3-3-Timeline)
  - [3-4. Controlling Animations](#3-4-Controlling-Animations)

## 1. GSAP을 공부하게 된 이유

프로젝트를 진행하면서 웹페이지들이 너무 밋밋해 보이는게 아닌가.. 물론? 내가 못 만들어서 그럴 확률 높음ㅎㅋ 그래도 프론트엔드 개발자가 꿈이라면 기능 구현뿐만 아니라 시각적인 요소도 매우 중요하다고 생각한다. 그래서 애니메이션에 관심을 가지게 되었고, gsap에 대해 공부하기 시작해부렸다!

## 2. What is GSAP?

GSAP(GreenSock Animation Platform)이란, 자바스크립트가 만질 수 있는 모든 것에 애니메이션을 적용할 수 있고, 수많은 크로스브라우징을 지원하며 jQuery보다 20배 빠른 성능을 제공한다.
.. 그럼 안쓸 이유가 없겠죠?ㅎㅋ

## 3. GSAP Object

GSAP의 Create animations 파트에서는 크게 Tweens 과 Timelines 두가지의 오브젝트를 가지고 있다.

### 3-1. Tweens

- gsap.to()

```jsx
gsap.to(".elem", { duration: 1, x: 100, y: 100, rotation: 45 });
```

- gsap.from()

```jsx
gsap.from(".elem", { duration: 1, x: 100, y: 100, rotation: 45 });
```

- gsap.formTo()

```jsx
gsap.fromTo(
  ".elem",
  { duration: 1, x: -100, y: -100, rotation: -45 },
  { duration: 1, x: 100, y: 100, rotation: 45 }
);
```

### 3-2. Staggers

약간의 시차를 주고 싶을 때

```jsx
gsap.from(".box", {
  duration: 2,
  sacale: 0.5,
  opacity: 0,
  delay: 0.5,
  stagger: 0.2, // 0.2초에 하나씩 나온다!
  ease: "elastic",
  force3D: true,
});

// scale, opacity의 원래 값은 1. from은 어디서부터~ 이니까.
// box들이 하나씩 커지면서 나오게 되는 애니메이션을 만들 수 있음
```

기본값은 `stagger: {each:0.2}`

- each : x초에 하나씩 실행

```jsx
gsap.to(".box", { y: -100, stagger: { each: 0.2 } });
```

- amount: x초안에 하나씩 실행

```jsx
gsap.to(".box", { y: -100, stagger: { amount: 1 } });
```

```jsx
gsap.to(".box", { y: -100, stagger: { amount: 1, from: "center" } });
// from을 이용해서 어디서부터 나오게 할건지 정할 수 있음
// (end, center, edges 등)
```

### 3-3. Timeline

연쇄된 애니메이션 만들 때

```jsx
var tl = gsap.timeline();

tl.to(".green", { duration: 1, x: 200 }); // (1)
tl.to(".orange", { duration: 1, x: 200, scale: 0.2 }); // (2)
tl.to(".grey", { duration: 1, x: 200, scale: 2, y: 20 }); // (3)

// (1)이 끝난 후 (2)가 나오고, (2)가 끝나면 (3)이 나옴
```

### 3-4. Controlling Animations

tween을 컨트롤할 수 있는 method 제공

```html
<div class="container">
  <div class="flair flair--25"></div>
  <div class="nav light">
    <button id="play">play()</button>
    <button id="pause">pause()</button>
    <button id="resume">resume()</button>
    <button id="reverse">reverse()</button>
    <button id="restart">restart()</button>
  </div>
</div>
```

```jsx
let nav = document.querySelector(".nav");

let tween = gsap.to(".flair", {
  duration: 2,
  x: () => nav.offsetWidth, // animate by the px width of the nav
  xPercent: -100, // offset by the width of the box
  rotation: 360,
  ease: "none",
  paused: true,
});

// click handlers for controlling the tween instance...
document.querySelector("#play").onclick = () => tween.play();
document.querySelector("#pause").onclick = () => tween.pause();
document.querySelector("#resume").onclick = () => tween.resume();
document.querySelector("#reverse").onclick = () => tween.reverse();
document.querySelector("#restart").onclick = () => tween.restart();
```
