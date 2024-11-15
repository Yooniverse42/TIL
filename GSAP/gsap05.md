# keyframes

[참고 페이지](https://gsap.com/resources/keyframes)

> CSS keyframes의 default ease값은 “power1.inOut”
> GSAP keyframes의 default ease값은 “power1.out”

## 예제1. 어몽이 점프 시키기

```html
<div class="stage">
  <div class="among"></div>
  <div class="among"></div>
  <div class="among"></div>
  <div class="among"></div>
  <div class="among"></div>
  <div class="among"></div>
  <div class="among"></div>
  <div class="among"></div>
</div>
```

```css
body {
  background: black;
}

.stage {
  /*   visibility:hidden; */
  width: 500px;
  height: 350px;
  background: gray;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  overflow: hidden;
  perspective: 700px;
}

.among {
  width: 80px;
  height: 90px;
  background: url(https://simseonbeom.github.io/Final/assets/among/Crew/Red.png);
  background-size: cover;
  position: absolute;
  top: 100px;
  transform: translateX(-100%);
}
```

```jsx
gsap.to(".among", {
  keyframes: {
    "0%": {},
    "25%": { y: 0, ease: "sine.out" },
    "50%": { y: -100, rotation: 360, ease: "sine.in" },
    "75%": { y: 0 },
    "100%": { x: 500 },
  },
  duration: 2,
  stagger: {
    each: 0.5,
  },
});
```

![image.png](keyframes%2013e8c534c7b080f78cd8fbb3859eb99a/image.png)

<br/>

## Easing keyframes

gsap은 가속도를 전체에도 줄 수 있고, 각 트윈들에게 따로 줄 수 있음.

- ease: 전체 흐름에 가속도 부여
- easeEach: 각 트윈들에게 가속도 부여

```jsx
gsap.to(".box", {
  keyframes: {
    "0%": { yPercent: 0, scaleX: 1, scaleY: 1 },
    "7%": { yPercent: 5, scaleY: 0.9, scaleX: 1.1, ease: "sine.in" }, // (2)
    "25%": { yPercent: 100, scaleY: 1.15, scaleX: 0.9, ease: "sine.in" },
    "50%": { yPercent: 500, scaleX: 1, scaleY: 1, ease: "none" },
    "60%": { scaleX: 1.6, scaleY: 0.4, ease: "none" },
    "65%": { yPercent: 500, scaleX: 1, scaleY: 1 },
    "100%": { yPercent: 0, scaleX: 1, scaleY: 1 },
    easeEach: "sine.out", // (1)
  },
  duration: 0.8,
  repeat: -1,
  transformOrigin: "center bottom",
});
```

`(1)` : 각 트윈들에게 ‘sine.out’을 쓰고 `(2)` 처럼 그 트윈에게 직접 가속도를 부여하면 그 가속도를 쓰겠다는 말! 그래서 7%, 15%, 50%, 60%의 트윈들은 각자에게 부여한 ease 속성이 적용됨.

<br/>

```jsx
gsap.to(".box", {
  keyframes: {
    y: [0, 80, -10, 30, 0], // (1)
    ease: "none", // (2)
    easeEach: "power2.inOut", // (3)
  },
  rotate: 180,
  ease: "elastic", // (4)
  duration: 5,
  stagger: 0.2,
});
```

`(1)` : 배열 기주으로 keyframes를 넣음  
`(2)` : 전체에 ease(가속도) 부여  
`(3)` : 각 트윈들(각 배열값)에게 가속도 부여  
`(4)` : keyframes를 제외한 rotate(회전)에 대한 가속도 부여

<br/>

## 예제2. 어몽이 커지면서 제자리 회전시키기

```jsx
const tl = gsap.timeline();

tl.from(".stage", { autoAlpha: 0 }) // (1)
  .to(".among", {
    keyframes: {
      "0%": { scale: 1 },
      "10%": { scale: 0.5 },
      "70%": { scale: 3, rotation: 360 },
      "100%": { scale: 1 },
    },
    duration: 1.5,
  });
```

`(1)` : autoAlpha: 0이 css visibility: hidden, opacity: 0 대신해줌. ⇒ 하는 이유는 FOUC 해결하기 위해.

> Flash of Un-styled Content (FOUC)란?
>
> 스타일이 지정되어 있지 않은 요소들이 화면에 랜더링 될 경우 콘텐츠의 깜빡이는 플래시 효과를 나타내는 용어.  
> 가장 일반적으로 웹폰트가 로드되기 전에 페이지 렌더링 상태에서 기본 글꼴이 나오고 적용된 웹폰트로 변경되는 모습

<br/>

## 예제3. 어몽이 커지면서 x축 회전 시키기

```jsx
const tl = gsap.timeline();

tl.from("stage", { autoAlpha: 0 }).to(".among", {
  keyframes: {
    "0%": {},
    "30%": { x: 420 },
    "50%": { scale: 2 },
    "60%": { x: 0 },
    "70%": { scale: 1 },
    "100%": { x: 420 },
    easeEach: "back(3)",
  },
  duration: 2,
});
```
