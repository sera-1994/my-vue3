## 前言

开始于 20230214

19 年刚工作的时候，磕磕绊绊用了 vue2，不到一年，改制重组，开始使用 react 类式组件，21 年换工作后使用 react 函数式组件，现在，想看看 vue 和 react 之间到底有何区别，还可以提高自己的竞争力，开始学习 vue3！

## 基础知识

1. 指令
   `v-on:click` 单击事件 `@click`
   `v-for` 循环指令
   `v-model` 数据双向绑定
   `v-bind:index="index"` `:index`
   `v-once` 插值表达式只做一次渲染
   `v-html`
2. mvvm 含义 model 数据 view 视图 vm viewModel 视图数据连接层
3. 声明周期函数 创建 渲染 更新 销毁
   - `beforeCreate` 在实例生成之前自动执行的函数,
   - `created` 在实例生成之后自动执行的函数,
   - `beforeMount` 在 template 模板渲染完成之前自动执行的函数,
   - `mounted` 在 template 模板渲染之后自动执行的函数
   - `beforeUpdate` 当 data 中的数据发生变化时，会立即自动执行的函数
   - `updated` 当 data 中的数据发生变化，页面重新渲染完后，会自动执行的函数
   - `beforeUnmount` 当 Vue 应用失效时，会自动执行的函数
   - `unmounted` 当 Vue 应用失效时，且 DOM 完全销毁之后，会自动执行
4. 字面量，即插值表达式 {{xxx}}，利用这种形式，可以使用 data 中的变量展示在模板中
5. 默认响应事件， form 表单中有个属性 action，点击 form 表单中的 submit 按钮，表单就会自动提交到 action 中标明的网址，所以经常会需要阻止默认事件，在 Vue 中，提供了模板修饰符，可以使用`@click.prevent` 即可直接阻止默认行为
6. 简写

- `v-bind:title` 可简写为 `:title`
- `v-on:click` 可简写为 `@click`

7. 模板动态参数

- `v-bind:[name]` ，name 的值可在 data 中规定，如 name=title，则指 `v-bind:title`

8. `v-if` `v-if-else` `v-else`

```
<h2 v-if="message=='sera'" class="one" @click="handleClick"></h2>{{message}}</h2>
<h2 v-if="message=='xixi'" class="two" @click="handleClick">{{message}}</h2>
```

9. 计算属性 computed, 必须要返回一个值，而且在页面渲染的同时就会执行里边的业务逻辑。
10. 侦听器 watch 只有发生变化时才会执行，也就是说值没有变化，它是不执行里边业务逻辑的。
    `computed` 和 `method`都能实现的功能，建议使用`computed`，因为有缓存，不用渲染页面就刷新。
    `computed` 和 `watch` 都能实现的功能，建议使用`computed`，因为更加简洁。
11. 模板样式绑定, 可以在 data 中定义 classString 、 classObject 、classArray 等形式，然后再模板中使用 v-bind 语法绑定 `:class="classArray"`

```
   classString: "red",
   classObject: {
      red: true,
      green: true,
      background: true,
   },
   classArray: ["red", "green", { background: true }],
   styleString: "color: pink; font-size: 44px",
   style: {
      background: "yellow",
      color: "green",
      "font-size": "24px",
   },
```

12. `v-show` 和 `v-if` 的区别
    `v-show` 实际 dom 元素存在，通过样式控制显示和隐藏 `display: none`
    `v-if` 整个 dom 元素不存在
    `v-if` 还可以用 `v-if-else`、`v-else`

    如果 dom 元素显示/隐藏切换频繁，可使用 `v-show`

13. `v-for`循环, `v-for`循环的优先级高于`v-if`判断的优先级(同个 dom 元素使用 v-for 的时候，再用 v-if 是不生效的)， `v-for="item in 99"`可用来循环 1-99 的数字
14. `<template></template>` 标签 不会形成 dom 元素，类似<React.Fragment />
15. 绑定事件
    `@click="handleAdd"`这个时候在 handleAdd 方法中可以直接获得 event 事件对象

```
   const app = Vue.createApp({
      data() {
        return {
          count: 0,
        };
      },
      methods: {
        handleAdd(event) {
          console.log(event.target);
          this.count += 1;
        },
      },
      template: `
      <div>已点甜筒数量{{count}}.</div>
      <button @click="handleAdd">再点一个甜筒</button>
      `,
    });
```

- 如果是多参数情况，则要 `@click=handleClick(3, $event)`这时，event 前需要添加$符号

```
   methods: {
      handleAdd(num, event) {
         console.log(event.target);
         this.count += num;
      },
   },
   template: `
   <div>已点甜筒数量{{count}}.</div>
   <button @click="handleAdd(2, $event)">再点一个甜筒</button>
   `,
```

- 可以直接在 dom 元素事件绑定中编写事件处理语句

```
   template: `
      <div>目前已点数量{{count}}.</div>
      <div>目前价格{{price}}.</div>
      <button @click="count+=2;price-=1;">再点一个甜筒</button>
   `,
```

- 一个按钮调用两个方法, 方法后要加括号，方法之间用逗号间隔（分号试了一下也可以）

```
<button @click="handleAdd(),handleClick()">再点一个甜筒</button>
```

- stop 修饰符，可以阻止冒泡 `<button @click.stop="addCountClick()">增加</button>`
- self 修饰符，只有点击自己的时候(不包括子元素)才会被执行，比如加在外层元素上
- prevent 修饰符，阻止默认提交事件
- capture 修饰符，将默认冒泡形式改成捕获形式，需要用在父元素上
- once 修饰符，事件只执行一次
- passive 修饰符，可解决滚动时性能的修饰符

16. 鼠标按键修饰符
    单个按键修饰符，按下键盘上某个特定的按键后事件才会响应，有： enter、 tab、 delete、 esc、 up、 down、 left、 right
    鼠标修饰符：按下鼠标上的某个键时，才会响应，有： left、 right、 middle
    `<input @keydown.enter="handleEnter"/>`

17. 数据双向绑定

```
   data() {
      return {
         lesson: "计算机概论",
         chose: true,
         girls: [],
         girl: "",
         text: "这是一段很长很长的文字",
      };
   },
   methods: {},
   template: `
      <div>{{lesson}}<input type="text" v-model="lesson"/></div>
      <div>{{chose}}<input type="checkbox" v-model="chose"/></div>
      <div>
         {{girls}}
         青下<input type="checkbox" v-model="girls" value="青下" />
         蓝上<input type="checkbox" v-model="girls" value="蓝上" />
         紫下<input type="checkbox" v-model="girls" value="紫下" />
      </div>
      <div>
         {{girl}}
         青下<input type="radio" v-model="girl" value="青下" />
         蓝上<input type="radio" v-model="girl" value="蓝上" />
         紫下<input type="radio" v-model="girl" value="紫下" />
      </div>
      <div>
         {{text}}
         <textarea v-model="text"/>
      </div>
      `,
```

18. `true-value` `false-value`

19. input 双向数据绑定 v-model 可以用

- 1. `lazy`修饰符: input 框输入完失去焦点后，data 中相应的值才跟着变化`<input v-model.lazy="name"/>`;
- 2. `number`修饰符: html 底层逻辑会导致不论输入的是 number 还是 string 类型，最终都会变成 string 类型，但加上 `number` 修饰符，只要输入的是数字，最终会变为数字`<input v-model.number="age"/>`;
- 3. `trim`修饰符: `<input v-model.trim="message" />` 去掉收尾空格。
     {{typeof name}}<input v-model.lazy.trim.number="name" /> 叠加使用也可以的
