# Button Animation Effects

## 1. 단일 메뉴 효과

> CSS로 간단한 애니메이션을 적용할 수 있지만, 재생・되돌아가기・속도・가속도 설정과 같은 섬세한 작업을 수행하기 위해선 자바스크립트가 필요하다.  
> 그 중에서도 <mark>GSAP</mark>을 사용하면 더 쉽고 빠르게 작업할 수 있다!

```html
<ul>
  <li class="item">
    <div class="dash"></div>
    <div class="text">Home</div>
  </li>
</ul>
```

```css
body {
  background: black;
  font-weight: bold;
}

ul {
  font-size: 30px;
  color: gray;
}
li {
  display: flex;
  align-items: center;
}

.dash {
  width: 15px;
  height: 4px;
  background: #3e3e3e;
  margin-right: 10px;
  opacity: 0;
}
```

```jsx
const item = document.querySelector(".item");

let tl = gsap
  .timeline({ paused: true }) // (1)
  .to(".dash", { opacity: 1, x: -5, backgroundColor: "yellow" })
  .to(".text", { x: 5, color: "#fff" }, "<"); // (2)

item.addEventListener("mouseenter", () => {
  tl.play();
});
item.addEventListener("mouseleave", () => {
  tl.reverse();
});
```

`(1)` : 바로 실행이 되지 않도록 paused를 true로!

`(2)` : `tl.to()` 로 적어도 되고, `timeline()` 뒤에 `;(세미콜론)` 삭제 후 이어서 바로 `timeline().to().to()` 로 적어도 됨.

## 2. 다중 메뉴 효과

> 각 항목에 대한 타임라인을 작성하기 위해 `forEach()` 루프 만들기

단일 메뉴와 다르게 li요소가 많은 다중 메뉴일 때! 각 메뉴 하나씩 애니메이션을 주고 싶다면?

```jsx
const items = document.querySelectorAll(".item");

// gsap.defaults({duration:0.3}) // (3)

items.forEach((item) => {
  let tl = gsap.timeline({ paused: true });

  tl.to(item.querySelector(".text"), { color: "#fff", x: 15, duration: 0.3 }) // (1)
    .to(
      item.querySelector(".dash"),
      { opacity: 1, x: -5, backgroundColor: "yellow", duration: 0.3 },
      0
    ); // (2)

  item.addEventListener("mouseenter", () => {
    tl.play();
  });
  item.addEventListener("mouseleave", () => {
    tl.reverse();
  });
});
```

`(1)` : forEach를 사용했기 때문에 선택자를 바로 `'.text'` 가 아닌 `item.querySelector('.text')` 로 해야한다.

`(2)` : 같은 duration을 주고 싶을 때, 모든 트윈들에게 걸면 코드가 길어졌을 때 복잡함. 그래서 각 트윈들에게 duration을 주는 것 대신 `(3)` 처럼 `gsap.defaults({duration:0.3})` 으로 코드를 작성 가능.

`(3)` : defaults를 따로 주게 되면 모든 애니메이션에 해당이 되므로 주의해야함.  
그래서 하나의 애니메이션에만 defaults를 주고 싶다면 아래처럼 사용하면 됨.

```jsx
/* 모든 애니메이션 적용 (one, two 모두 적용됨) */
const one = gsap.timeline();
const two = gsap.timeline({ paused: true });

gsap.defaults({ opacity: 0, duration: 0.3 });

/* 내가 원하는 애니메이션에만 적용(one에만 적용됨) */
const one = gsap.timline({
  defaults: { opacity: 0, duration: 0.3 },
});

const two = gsap.timeline({ paused: true });
```
