### 1. 动画和过渡

- 动画 动画是位置发生改变 animation transform translate
- 过渡 过渡是属性发生改变 transition

### 2. Vue3 制作动画效果

进入
.v-enter-active{

}
进出
.v-leave-active {

}

### 3. Vue3 制作过渡效果

- 1. `.v-enter-from {}`，`.v-enter-to {}`这两个样式就是控制进入前 DOM 的样式和进入完成时 DOM 的样式
- 2. `.v-leave-from {}`，`.v-leave-to {}`这两个固定的 CSS 选择器控制离开时 DOM 的样式
- 3. dom 元素出现消失的透明度过渡效果可利用固定的选择器，配合<transition></transition>标签即可实现

### 5. 第三方动画库 Animate 使用

- 1. 可通过在`<transition></transition>`增加 `name`属性的情况，进而修改类名`v-leave-from`,`v-leave-to`,`v-enter-from`,`v-enter-to`,`v-enter-active`,`v-leave-active`为`name-leave-from`,`name-leave-to`,`name-enter-from`,`name-enter-to`,`name-enter-active`,`name-leave-active`
- 2. 自行制定 CSS 选择名称，在`<transition></transition>`标签中直接指定一个 CSS 样式，这种方式用得比较多

  ```
    <style>
      .v-enter-from,
      .v-leave-to {
        opacity: 0;
      }
      .v-enter-to,
      .v-leave-from {
        opacity: 1;
      }
      .come,
      .go {
        transition: 3s opacity ease-out;
      }
    </style>
    <transition enter-active-class="come" leave-active-class="go">
      <div v-if="isShow"><h2>Sera is pretty!</h2></div>
    </transition>
  ```

### 5. Animate.css 使用技巧

Animate.css 中有很多动画样式可以使用[animate.css](https://animate.style/)
引入 Animate.css 动画 `<link
  rel="stylesheet"
  href="https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.min.css"
/>`
使用：
`  <div>
     <transition
                   enter-active-class="animate__animated animate__fadeIn"
                   leave-active-class="animate__animated animate__fadeIn"
                 >
     <div v-if="isShow"><h2>Sera is pretty!</h2></div>
     </transition>
     <button @click="toggle">隐藏/显示</button>
     </div>`

### 6.过渡和动画一起使用的技巧

- 问题：当过渡和动画同时作用且二者时间不一致时，可能会产生 bug

```
  .v-enter-active {
    animation: comeIn 3s;
    transition: opacity 7s ease-out;
  }
  .v-leave-active {
    animation: leaveOut 3s;
    transition: opacity 7s ease-out;
  }
```

- 解决 1：transition 和 animation 同时存在，以时间短的效果为主，在 transition 标签上增加 type 属性，type="animation" type="transition" 来控制以哪个为主

```
  // 动画和过渡同时存在时，以动画animation为主
  <transition type="animation">
    <h2 v-if="isShow">Sera is a talent.</h2>
  </transition>
```

- 解决 2：使用`transition`标签上的 duration 属性，`duration="1000"`动画和过渡的时间都是 1s; `:duration="{animation: 1000, duration: 3000}"` 动画持续 1s，过渡持续 3s (注意： 当 duration 的值是 1000 时，可不加 v-bind;当 duration 的值为对象的时候，需要加 v-bind)

```
  <transition duration="1000">
    <h2 v-if="isShow">Sera is a talent.</h2>
  </transition>
  <transition :duration="{animation: 1000,transition: 3000}">
    <h2 v-if="isShow">Sera is a talent.</h2>
  </transition>
```

### 7. JS 和 Vue3，利用钩子函数，制作动画

`<transition></transition>`标签使用`:css="false"`

钩子函数，在某一时刻自动执行，类似生命周期函数

- `before-enter` 进入动画开始之前
- `enter` 进入动画开始时
- `after-enter` 进入动画结束
- `before-leave` 离场动画开始之前
- `leave` 离场动画执行开始时
- `after-leave` 离场动画执行结束

```
<transition :css="false"
  @before-enter="handleBeforeEnter"
  @enter="handleEnter"
  @after-enter="handleEnterEnd"
  @before-leave="handleBeforeEnter"
  @leave="handleEnter"
  @after-leave="handleEnterEnd"
>
  <h2 v-if="isShow">Sera is a pretty girl.</h2>
</transition>
```

### 8. 两个 dom 元素切换的效果

利用<transition></transition>标签的 mode 属性，可以控制是先显示离场和入场动画的顺序的。

appear 属性 初次对某一个元素进行默认显示的时候也进行动画显示。

### 9. 两个组件的切换
