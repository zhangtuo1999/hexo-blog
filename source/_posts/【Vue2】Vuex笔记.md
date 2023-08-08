---
title: 【Vue2】Vuex笔记
tags:
  - 前端
  - Vue
  - Vuex
excerpt: Vuex 3.x。State、Mutations、Actions、Getters，mapStates、mapGetters、mapMutations、mapActions，模块化、命名空间等等。
typora-root-url: ..
abbrlink: 7a98a4dd
date: 2022-03-02 21:23:13
index_img: ../images/封面图/vue2.jpg
---

# 一、Vuex 介绍

>Vuex 是一个专为 Vue.js 应用程序开发的**状态管理模式**。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

简言之，Vuex 可以管理一些数据，以便所有组件共享。

# 二、Vuex 工作原理图

![Vuex工作原理图](/images/Vuex笔记/vuex-16477676604351.png)

# 三、Vuex 使用

## 3.1 安装 Vuex

NPM 安装

```bash
npm install vuex@3 --save	//Vue2 对应 Vuex3
```

## 3.2 在 Vue 项目中使用 Vuex

以实现计数功能为例：

![image-20220319191059347](/images/Vuex笔记/image-20220319191059347.png)

在 src/store/index.js 中：

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
  state: {
      count:0						// 状态
  },
  mutations: {
      HANDLE_ADD(state, num) {		// 显式改变state的mutation
         state.count += num
      }
  },
})
```

在 src/main.js 中：

```js
import Vue from 'vue'
import App from './App.vue'
import store from './store'		// 引入store

Vue.config.productionTip = false

new Vue({
  store,						// 将store注入vue
  render: h => h(App)
}).$mount('#app')

```

在 src/componentsChild.vue 中：

```vue
<template>
  <div>
    <h1>count:{{ count }}</h1>
    <input v-model.number="num">	
    <button @click="handleClick">点击count++</button>
  </div>
</template>

<script>
export default {
  name: "Child",
  data() {
    return {
        num: 1
    }
  },
  computed: {
    count() {
      return this.$store.state.count
    }
  },
  methods: {
    handleClick() {
      this.$store.commit('HANDLE_ADD',this.num)
    }
  }
}
</script>
```

在 App.vue 中：

```vue
<template>
  <div id="app">
    <Child></Child>
  </div>
</template>

<script>
import Child from "@/components/Child";

export default {
  name: 'App',
  components: {
    Child,
  }
}
</script>
```

## 3.3 State

state 中保存需要用 vuex 来管理的数据。

在 src/store/index.js  中：（Vuex）

```js
state: {
	count:0		//交给 Vuex 管理的数据
},
```

在 Child.vue 中：（组件）

```vue
computed: {
    count() {
    	return this.$store.state.count
    }
},
```

## 3.4 Mutations

>不能直接改变 store 中的状态。改变 store 中的状态的唯一途径就是显式地**提交 (commit) mutation**。这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。

>更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。Vuex 中的 mutation 非常类似于事件：每个 mutation 都有一个字符串的 **事件类型 (type)** 和 一个 **回调函数 (handler)**。这个回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数：


在 src/store/index.js中

```js
mutations: {
    HANDLE_ADD(state, num) {	//此处的 num 被称为载荷。如果需要传参较多，载荷应该为含有多个字段的对象
    	state.count += num
    }
}
```

不能直接调用一个 mutation handler。这个选项更像是事件注册：“当触发一个类型为 `increment` 的 mutation 时，调用此函数。”要唤醒一个 mutation handler，你需要以相应的 type 调用 **store.commit** 方法：

在 Child.vue 中

```js
methods: {
    handleClick() {
        this.$store.commit('HANDLE_ADD', this.num)	//组件中提交 mutation
    }
}
```

注意：

* 一条重要的原则就是要记住 **mutation 必须是同步函数**。异步操作放入 **action** 中。
* 一般把 mutation 中的函数名写成大写。

## 3.5 Actions

>Action 类似于 mutation，不同在于：
>
>- Action 提交的是 mutation，而不是直接变更状态。
>- Action 可以包含任意异步操作。
>

在 src/store/index.js 中

```js
actions: {
    handleAdd(context,num){
        context.commit('HANDLE_ADD',num)
    }
},
```

注意：

* Action 函数接受一个与 store 实例具有相同方法和属性的 context 对象，因此你可以调用 `context.commit` 提交一个 mutation，或者通过 `context.state` 和 `context.getters` 来获取 state 和 getters。

* Action 通过 `store.dispatch` 方法触发，在 Child.vue 中

  ```vue
  methods: {
      handleClick() {
      	this.$store.dispatch('handleAdd',this.num)
      }
  }
  ```

* Action 中可以执行**异步操作**。`store.dispatch` 可以处理被触发的 action 的处理函数返回的 Promise，并且 `store.dispatch` 仍旧返回 Promise。

## 3.6 Getters

> 有时候我们需要从 store 中的 state 中派生出一些状态。比如 count 的10倍。

![image-20220319193429568](/images/Vuex笔记/image-20220319193429568.png)
在 Child.vue 中：

```js
computed: {
  bigCount () {
    return this.$store.state.count * 10
  }
}
```

> 如果有多个组件需要用到此属性，我们要么复制这个函数，或者抽取到一个共享函数然后在多处导入它——无论哪种方式都不是很理想。
>
> Vuex 允许我们在 store 中定义“getter”（可以认为是 store 的计算属性）。就像计算属性一样，getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算。
>
> Getter 接受 state 作为其第一个参数：

在 store/index.js 中：

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
    state: {
        count: 0
    },
    mutations: {
        HANDLE_ADD(state, num) {
            state.count += num
        }
    },
    getters: {
        bigCount(state){				// bigCount 是 state 中 count 的 10 倍
            return state.count * 10	
        }
    },
})

```

* Getter 也可以接受其他 getter 作为第二个参数：

  ```js
  getters: {
      bigCount(state){
          return state.count * 10
      },
      largeCount(state, getters){	// largeCount 等于 state 中 count 加上 getters 中的 bigCount
          return state.count + getters.bigCount
      }
  },
  ```

  ![image-20220319194434725](/images/Vuex笔记/image-20220319194434725.png)

# 四、组件绑定的辅助函数

## 4.1 mapState

>当一个组件需要获取多个状态的时候，将这些状态都声明为计算属性会有些重复和冗余。为了解决这个问题，我们可以使用 `mapState` 辅助函数帮助我们生成计算属性，让你少按几次键：

### 4.1.1 对象写法

在 Child.vue 中

```vue
<template>
  <div>
    <h1>count:{{ count }}</h1>
    <input v-model.number="num">
    <button @click="handleClick">点击count+num</button>
  </div>
</template>

<script>
import {mapState} from 'vuex'		//借助 mapState 简化state在computed中的写法

export default {
  name: "Child",
  data() {
    return {
      num: 1
    }
  },
  computed: {
    ...mapState({count:'count'})	// mapState 在 computed 中的第一种写法：对象写法
  },
  methods: {
    handleClick() {
      this.$store.commit('HANDLE_ADD', this.num)
    }
  }
}
</script>

```

注意：

* 对象写法中第一个 `count` 指的是在这个组件中使用的名称，第二个 `'count'` 指的是在 Vuex 中 state 的 key 值。即映射 `this.count` 为 `store.state.count`
* `mapState` 函数返回的是一个对象。我们如何将它与局部计算属性混合使用呢？使用对象展开运算符即`...`

### 4.1.2 数组写法

>当映射的计算属性的名称与 state 的子节点名称相同时，我们也可以给 `mapState` 传一个字符串数组。

```vue
computed: {
	...mapState(['count'])	//映射 this.count 为 store.state.count
}
```

## 4.2 mapGetters

用法等同于 mapState：

在 Child.vue 中：

```vue
<template>
  <div>
    <h1>count:{{ count }}</h1>
    <h1>bigCount:{{ bigCount }}</h1>
    <input v-model.number="num">
    <button @click="handleClick">点击count+num</button>
  </div>
</template>

<script>
import {mapState,mapGetters} from 'vuex'

export default {
  name: "Child",
  data() {
    return {
      num: 1
    }
  },
  computed: {
    ...mapState(['count']),
    ...mapGetters(['bigCount'])
  },
  methods: {
    handleClick() {
      this.$store.commit('HANDLE_ADD', this.num)
    }
  }
}
</script>
```

效果：

![image-20220319193751731](/images/Vuex笔记/image-20220319193751731.png)

## 4.3 mapMutations

在 Child.vue 中：

```vue
<template>
  <div>
    <h1>count:{{ count }}</h1>
    <h1>bigCount:{{ bigCount }}</h1>
    <h1>largeCount:{{ largeCount }}</h1>
    <input v-model.number="num">
    <button @click="handleCommit(num)">点击commit</button>
  </div>
</template>

<script>
import {mapState, mapGetters, mapMutations} from 'vuex'

export default {
  name: "Child",
  data() {
    return {
      num: 1
    }
  },
  computed: {
    ...mapState(['count']),
    ...mapGetters(['bigCount', 'largeCount'])
  },
  methods: {
    ...mapMutations({handleCommit: 'HANDLE_ADD'}),
  }
}
</script>
```

## 4.4 mapActions

在 Child.vue 中：

```vue
<template>
  <div>
    <h1>count:{{ count }}</h1>
    <h1>bigCount:{{ bigCount }}</h1>
    <h1>largeCount:{{ largeCount }}</h1>
    <input v-model.number="num">
    <button @click="handleCommit(num)">点击commit</button>
    <button @click="handleDispatch(num)">点击dispatch</button>
  </div>
</template>

<script>
import {mapState, mapGetters, mapActions, mapMutations} from 'vuex'

export default {
  name: "Child",
  data() {
    return {
      num: 1
    }
  },
  computed: {
    ...mapState(['count']),
    ...mapGetters(['bigCount', 'largeCount'])
  },
  methods: {
    ...mapMutations({handleCommit: 'HANDLE_ADD'}),
    ...mapActions({handleDispatch: 'handleAdd'}),

  }
}
</script>
```

# 五、模块化

## 5.1 模块化实现

>由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。
>
>为了解决以上问题，Vuex 允许我们将 store 分割成**模块（module）**。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割：

在 src/store 下新建一个文件夹，命名为 modules。在 modules 下新建一个 countAbout.js 用于存放和计数相关的 store 相关子模块。

```js
export default {
    state: {
        count: 0
    },
    getters: {
        bigCount(state) {
            return state.count * 10
        },
        largeCount(state, getters) {
            return state.count + getters.bigCount
        }
    },
    mutations: {
        HANDLE_ADD(state, num) {
            state.count += num
        }
    },
    actions: {
        handleAdd(context, num) {
            context.commit('HANDLE_ADD', num)
        }
    }
}
```

在 src/store/index.js 中：

```js
import Vue from 'vue'
import Vuex from 'vuex'
import countAbout from "@/store/modules/countAbout";

Vue.use(Vuex)

export default new Vuex.Store({
    modules: {
        countAbout
    }
})
```

## 5.2 命名空间

在模块中使用 `namespaced:true` 开启命名空间。

在 countAbout.js 中：

```javascript
export default {
    namespaced:true,
    state: {
        count: 0
    },
    getters: {
        bigCount(state) {
            return state.count * 10
        },
        largeCount(state, getters) {
            return state.count + getters.bigCount
        }
    },
    mutations: {
        HANDLE_ADD(state, num) {
            state.count += num
        }
    },
    actions: {
        handleAdd(context, num) {
            context.commit('HANDLE_ADD', num)
        }
    }
}
```

在 Child.vue 中：

```vue
<template>
  <div>
    <h1>count:{{ count }}</h1>
    <h1>bigCount:{{ bigCount }}</h1>
    <h1>largeCount:{{ largeCount }}</h1>
    <input v-model.number="num">
    <button @click="handleCommit(num)">点击commit</button>
    <button @click="handleDispatch(num)">点击dispatch</button>
  </div>
</template>

<script>
import {mapState, mapGetters, mapActions, mapMutations} from 'vuex'

export default {
  name: "Child",
  data() {
    return {
      num: 1
    }
  },
  computed: {
    ...mapState('countAbout', ['count']),
    ...mapGetters('countAbout', ['bigCount', 'largeCount'])
  },
  methods: {
    ...mapMutations('countAbout',{handleCommit:'HANDLE_ADD'}),
    ...mapActions('countAbout',{handleDispatch:'handleAdd'}),

  }
}
</script>
```

# 六、参考

* https://v3.vuex.vuejs.org/zh/

