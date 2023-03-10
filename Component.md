## 关于组件的一些事儿

### 1. 全局组件 只要定义，处处可以使用，性能不高，但是使用起来简单

```
app.component("Projects", {
      data() {
        return {
          products: ["机器人", "红外", "文献"],
          newItem: "",
        };
      },
      methods: {
        addProduct() {
          this.products.push(this.newItem);
          this.products = Array.from(new Set(this.products));
          this.newItem = "";
        },
      },
      template: `
      <div>
        <ul><li v-for="(item, index) in products" key="item+index">{{item}}</li></ul>
        <div><input v-model="newItem" @keydown.enter="addProduct"/><button @click="addProduct">提交</button></div>
      </div>`,
    });
```

### 2. 局部组件

定义局部组件，首字母大写，采用驼峰式命名方式

```
<!-- 采用对象形式定义局部组件 -->
  const Counter = {
    data() {
      return {
        num: 0,
      };
    },
    methods: {
      add() {
        this.num++;
      },
    },
    template: `<div>{{num}}<button @click="add">再来一个</button></div>`,
  };

  const app = Vue.createApp({
    <!-- 在父组件中注册局部组件 -->
    components: { Counter },
    data() {
      return {};
    },
    methods: {},
    template: `<div><Counter /></div>`,
  });
```

### 3. 父子组件的静态和动态传值

父子组件传递的参数是函数时，该函数是否必须在 data 属性中定义？不是的, 可以用 methods 中的方法
在子组件中调用 props 中的函数时，必须要用 this 的形式么？ 是的

- 动态传值：采用 v-bind 绑定 data 属性中定义的字段，动态传值可以是 number、 string、函数等；
- 静态传值只能传字符串形式的值。

### 4. 组件传值时的校验操作

- required 必填项
- default 默认值
- 精准校验 validator

```
props: {
  name: {
    type: String,
    validator: (value)=> {
      return value.search("sera") != -1;
    }
  }
}
```

### 5. 组件中的单向数据流机制

prop， 所有的 prop 都使得其父子 prop 之间形成了一个单向下行绑定，父级 prop 的更新会向下流动到子组件中，但是反过来则不行。

### 6. [组件]Non-props 属性

- 子组件没有接受父组件传递过来的参数，而子组件完全不变的复制了属性到自己的标签上，就叫做`Non-props`属性

- inheritAttrs: false 不复制父组件传过来的值,默认值为 true，即复制父组件传过来的值。

- 坑：当子组件的根节点只有一个才会复制父组件传过来的值

- Non-Props 多节点失效解决办法，可使用 $attrs 属性
  `<h2 v-bind:style="$attrs.style">Hello World<h2>`

- 如果 inheritAttrs 属性的值为 false，但这时在子组件 dom 元素中，使用`v-bind=$attrs`那么依然能获得 non-props 属性

```
  const Hello = {
    inheritAttrs: true,
    methods: {
      getInfo() {
        console.log(this.$attrs);
      },
    },
    template: `
    <div v-bind="$attrs">
      <h2>Hello world</h2>
      <h2>{{$attrs.message}}</h2>
      <button @click="getInfo">点一点</button>
    </div>
    <div :style="$attrs.style">这是一个机会</div>
    `,
  };

  const app = Vue.createApp({
    components: {
      Hello,
    },
    data() {
      return {
        name: "era is a pretty girl.",
      };
    },
    template: `<div>
      <Hello style="color: red" :message="name"/>
      </div>`,
  });
```

### 7. 组件间通过事件进行通信

- 1. 子组件调用父组件事件/方法的编写
- 2. 子组件向父组件传递参数
- 3. 传递参数时，如何进行校验

```
  const Hello = {
    inheritAttrs: true,
    props: ["num"],
    emits: {
      add: (value) => {
        return value > 20 ? true : false;
      },
    },
    methods: {
      getInfo() {
        console.log("ha ha");
        this.$emit("add", this.num + 3);
      },
    },
    template: `
    <div>这是一个机会<button @click="getInfo">点一点哦</button></div>
    `,
  };

  const app = Vue.createApp({
    components: {
      Hello,
    },
    data() {
      return {
        num: 0,
      };
    },
    methods: {
      handleAdd(param) {
        this.num = param;
      },
    },
    template: `<div>{{num}}
      <Hello :num="num" @add="handleAdd"/>
      </div>`,
  });
```

### 8. 插槽

`<slot></slot>` 感觉类似 React 中的{{children}}
父组件使用子组件 可以写 组件、dom 元素、动态数据，子组件使用插槽接收
<slot></slot> 中的内容是默认值，如果父组件中没有写，则展示默认内容

### 9. 具名插槽使用

父组件中使用`<template v-slot:one>...</template>` ，子组件中使用<slot name="one"></slot>

具名插槽简写方式`<template #one></template>`

### 10. 作用域插槽的使用

子组件中渲染的列表，标签类型由父组件使用的时候决定，可以使用插槽方式。

```
  <div id="app"></div>
  <script>
    const app = Vue.createApp({
      template: `
      <div id="parent">
        个人经历
        <personal-skills v-slot="props"><div class="parent-skill-item">{{props.item}}</div></personal-skills>
      </div>
      `,
    });
    app.component("PersonalSkills", {
      data() {
        return {
          skillsList: [
            "js",
            "ts",
            "css",
            "html",
            "react",
            "vue",
            "material ui",
            "element ui",
            "antd",
            "less",
            "scss",
            "sass",
            "原型设计",
          ],
        };
      },
      template: `
      <div id="child"><slot v-for="item in skillsList" :item="item" /></div>
      `,
    });
    app.mount("#app");
  </script>
```

### 11. 动态组件和状态保存

动态组件使用`<component :is=""></component>` 标签，is 的值就是子组件的名称；

状态保存可使用`<keep-alive></keep-alive>`标签包裹动态组件标签`<component />` 可实现数据不丢，比如`<input />`标签已经输入的内容，再切换回来还是存在的。

### 12. 异步组件和 Promise 讲解

异步组件的定义
异步组件的用处：1. 后台请求很多数据的时候使用 2. 大项目拆成很多小的异步组件时使用

```
  app.component(
    "AsyncCom",
    Vue.defineAsyncComponent(() => {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          resolve({ template: `<div>这是一个异步组件</div>` });
        }, 3000);
      });
    })
  );
```

### 13. provide 和 inject 多级组件传值

多级组件中传值，可以用 provide 和 inject 间隔传值

父组件中：

```
  data(){
    return {...}
  },
  provide: {
    newHouse: '北京房子一套'
  },
```

孙子组件中:

```
  inject: ['newHouse'],
```
