---
title: 【Vue2】Vue Router笔记
tags:
  - 前端
  - Vue
  - Vue Router
excerpt: Vue Router 3.x。主要内容：基本路由、动态路由、路由优先级、嵌套路由、命名路由、命名视图、重定向和别名、路由组件传参、编程式路由导航等等。
typora-root-url: ..
abbrlink: 64b7bd5b
date: 2022-03-08 20:12:41
index_img: ../images/封面图/vue2.jpg
---

[TOC]



>本篇笔记参考：
>
>* https://v3.router.vuejs.org/zh/
>* https://www.bilibili.com/video/BV1Zy4y1K7SH
>
>本篇笔记代码地址：https://github.com/zhangtuo1999/study-vue-router

# 一、介绍

> Vue Router 是 [Vue.js ](http://cn.vuejs.org/)官方的路由管理器。它和 Vue.js 的核心深度集成，让构建单页面应用变得易如反掌。

# 二、安装

## 2.1 NPM 

如果在一个模块化工程中使用 Vue Router：

```bash
npm install vue-router
```

```js
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)
```

## 2.2 Vue CLI

如果在一个 Vue CLI 项目中使用 Vue Router：

```bash
vue add router
```

CLI 可以生成 2.1 节中的代码及两个示例路由。**并覆盖 `App.vue`**。

# 三、基本路由

## 3.1 基本路由使用

在 src/router/index.js 中：

```js
import Vue from 'vue'
import VueRouter from 'vue-router'
import HomeView from '../views/HomeView.vue'

Vue.use(VueRouter)

const routes = [
  {
    path: '/',
    name: 'home',
    component: HomeView
  },
  {
    path: '/about',
    name: 'about',
    // route level code-splitting
    // this generates a separate chunk (about.[hash].js) for this route
    // which is lazy-loaded when the route is visited.
    component: () => import(/* webpackChunkName: "about" */ '../views/AboutView.vue')
  }
]

const router = new VueRouter({
  routes
})

export default router

```

在 src/App.vue 中：

```vue
<template>
  <div id="app">
    <nav>
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </nav>
    <router-view/>
  </div>
</template>
```

## 3.2 动态路由匹配

### 3.2.1 动态路径参数

我们经常需要把某种模式匹配到的所有路由，全都映射到同个组件。例如，我们有一个 `User` 组件，对于所有 ID 各不相同的用户，都要使用这个组件来渲染。那么，我们可以在 `vue-router` 的路由路径中使用“动态路径参数”(dynamic segment) 来达到这个效果。

在路由配置中：

```js
const routes = [   
    {
        path: '/user/:id',	//动态路由参数
        name: 'user',
        component: () => import('../views/UserView.vue')
    }
]
```

一个“路径参数”使用冒号 `:` 标记。当匹配到一个路由时，参数值会被设置到 `this.$route.params`，可以在每个组件内使用。

在 UserView 中：

```vue
export default {
  name: "UserView",
  data() {
    return {
      currentID: undefined,
    }
  },
  created() {
    this.currentID = this.$route.params.id
  },
}
```

在 App.vue 中：

```vue
<router-link to="/user/1">User</router-link>
```

此时 `1` 作为参数，可以被组件 UserView 接收到。

### 3.2.2 监听路由参数的变化

动态路径参数变化时，由于组件是被复用的，组件的生命周期钩子不会被再次调用。因此，如果想对参数做出响应，应该监听 `$route` 对象。

在 UserView.vue 中：

```vue
watch: {
	$route(to, from) {
		this.currentID = to.params.id
		console.log('to:', to.params.id, 'from:', from.params.id)
	}
}
```

## 3.3 使用通配符路由

常规参数只会匹配被 `/` 分隔的 URL 片段中的字符。如果想匹配**任意路径**，我们可以使用通配符 (`*`)：

```js
{
    path: '/user-*',	// 会匹配以 `/user-` 开头的任意路径
    name: 'userInfo',
    component: () => import('../views/UserInfo')
},
{
     path: '*',			// 会匹配所有路径
     name: '404',
     component: () => import('../views/404')
}
```

当使用一个*通配符*时，`$route.params` 内会自动添加一个名为 `pathMatch` 参数。它包含了 URL 通过*通配符*被匹配的部分：

```vue
export default {
  name: "UserInfo",
  created() {
	// 如果路径是'/user-Tony',这里的 currentName 值为'Tony'
    this.currentName = this.$route.params.pathMatch
  },
  data() {
    return {
      currentName: ''
    }
  }
}
```

## 3.4 路由优先级

按照路由定义的顺序，越早定义优先级越高。

# 四、嵌套路由（多级路由）

实际生活中的应用界面，通常由多层嵌套的组件组合而成。同样地，URL 中各段动态路径也按某种结构对应嵌套的各层组件。使用嵌套路由配置，可以上述的这种关系。

比如想在 UersView 组件中嵌套 UserProfile 和 UserPosts 组件。在路由配置参数中，使用 `children` ：

```js
{
    path: '/user/:id',
    component: () => import('../views/UserView.vue'),
    children: [
        {
            path: 'profile',
            component: () => import('../views/UserProfile.vue'),
        },
        {
            path: 'posts',
            component: () => import('../views/UserPosts.vue'),
        }
    ]
},
```

注意：子路由的 path 不需要在前面加上 `/`。所有前面带 `/` 的 path 都被视为最高级别的路由，即跟路径。

这时访问 `/user/1` 时，User 的出口是不会渲染任何东西，这是因为没有匹配到合适的子路由。如果你想要渲染点什么，可以提供一个 空的 子路由：

```js
{
    path: '/user/:id',
    component: () => import('../views/UserView.vue'),
    children: [
        {
            path: '',		// 空的子路由
            component: () => import('../views/UserHome.vue')
        },
        {
            path: 'profile',
            component: () => import('../views/UserProfile.vue'),
        },
        {
            path: 'posts',
            component: () => import('../views/UserPosts.vue'),
        }
    ]
},
```

# 五、命名路由

可以使用 `name` 属性给路由设置名称：

```js
{
    path: '/school/:schoolName',
    name: 'school',
    component: () => import('../views/School.vue')
}
```

声明式导航到 `/school/BUCT`：

```vue
<router-link :to="{name:'school',params:{schoolName:'BUCT'}}">School</router-link>	
```

编程式导航到上述 `/school/BUCT`：

```js
this.$router.push({name:'school',params:{schoolName:'BUCT'}})
```

# 六、命名视图

如果想在同一个页面上展示多个视图，可以在界面中写多个单独命名的视图。如果不设置名字，那么默认是 `default` 。

在页面上：

```vue
<router-view></router-view>		// 默认名字是 default
<router-view name="helper"></router-view>	// 把视图命名为 helper
```

在路由配置中，使用 `components` 配置（带上s）：

```js
{
    path: '/settings',
    name: 'settings',
    component: UserSettings,
    children: [
        {
            path: 'email',
            name: 'email',
            component: () => import('../views/Settings/UserEmailSubscriptions')
        },
        {
            path: 'profile',
            name: 'profile',
            components: {
                default: () => import('../views/Settings/UserProfile'),
                helper: () => import('../views/Settings/UserProfilePreview')
            }
        }
   ]
}
```

# 七、重定向和别名

## 7.1 重定向

* 重定向到 path：

  ```js
  { path:'/redirectToAbout',redirect:'/about' }
  ```

* 重定向到 name：

  ```js
  { path:'/redirectToAbout',redirect:{name: 'about'} }
  ```

## 7.2 别名

可以给 path 起一个别名，例如：

```js
{
    path: '/about',
    name: 'about',
    alias: '/a',
    component: () => import('../views/AboutView.vue')
}
```

## 7.3 重定向和别名的区别

* 将 `/redirectToAbout`  重定向到  `about` 。相当于访问`/redirectToAbout`时，url 会被替换成 `/about`。
* 给 `/about` 起一个别名 `/a `。相当于，访问 `/a` 时，url 会保持为 `/a` ，但是就像访问 `/about` 一样。

# 八、路由组件传参

将组件与路由解耦：

未解耦前：

```js
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}
const router = new VueRouter({
  routes: [{ path: '/user/:id', component: User }]
})
```

解耦后：

```js
const User = {
  props: ['id'],
  template: '<div>User {{ id }}</div>'
}
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User, props: true },
  ]
})
```

这样便可以在任何地方使用该组件，使得该组件更易于重用和测试。

## 8.1 布尔模式

如果 `props` 被设置为 `true`，`route.params` 将会被设置为组件属性。

## 8.2 对象模式

如果 `props` 是一个对象，它会被按原样设置为组件属性。当 `props` 是固定不变的时候有用。

```js
const router = new VueRouter({
  routes: [
    {
      path: '/promotion/from-newsletter',
      component: Promotion,
      props: { newsletterPopup: false }
    }
  ]
})
```

# 九、push 模式和 replace 模式

## 9.1 push 模式	

在路由的 history 栈中压入一条记录。

## 9.2 replace 模式

替换路由的 history 栈顶的记录。

# 十、编程式路由导航

声明式的导航：

```vue
 <router-link to="/user/1">User1</router-link>
```

## 10.1 push

使用方法：

* 字符串

  ```js
  this.$router.push('/user/1')	// 字符串路径
  ```

* 对象

  ```js
  this.$router.push({path:'/'})	// 描述地址的对象
  ```

* 命名的路由

  ```js
  this.$router.push({name:'user',params:{userID:1}})
  ```

* 带查询参数（参数会在 url 后进行拼接）

  ```js
  router.push({ path: 'register', query: { plan: 'private' }})
  ```

* 注意：

  * **如果提供了 `path`，`params` 会被忽略**，需要提供路由的 `name` 或手写完整的带有参数的 `path`

    ```js
    this.$router.push({name:'user',params:{userID:1}})
    ```

  * 下面这种 `path` 和 `params` 同时存在的情况中，`params` 不生效

    ```js
    this.$router.push({path:'/user',params:{id:'1'}})
    ```

## 10.2 replace

跟 `router.push` 很像，唯一的不同就是，它不会向 history 添加新记录，而是跟它的方法名一样 —— 替换掉当前的 history 记录。

## 10.3 back

后退一步：

```js
router.back()
```

## 10.4 forward

前进一步：

```js
router.forward()
```

## 10.5 go

这个方法的参数是一个整数，意思是在 history 记录中向前或者后退多少步

```js
// 在浏览器记录中前进一步，等同于 history.forward()
router.go(1)

// 后退一步记录，等同于 history.back()
router.go(-1)

// 前进 3 步记录
router.go(3)

// 如果 history 记录不够用，那就默默地失败呗
router.go(-100)
router.go(100)
```

# 十一、缓存路由组件

默认情况下在切换路由链接时，前一个路由组件对象会被销毁掉。下次切换回来时，又是一个新的路由组件对象。反复切换时，开销较大。使用 `keep-alive` Vue 内置组件包裹 `router-view` 可以保证在来回切换路由时，路由组件对象不会被销毁掉而被缓存起来，节省了开销。

```vue
<keep-alive>
	<router-view></router-view>
</keep-alive>
```

* https://cn.vuejs.org/v2/api/#keep-alive

# 十二、两个新的生命周期钩子

## 12.1 activated 

- **类型**：`Function`

- **详细**：

  被 keep-alive 缓存的组件激活时调用。

  **该钩子在服务器端渲染期间不被调用。**

## 12.2 deactivated

- **类型**：`Function`

- **详细**：

  被 keep-alive 缓存的组件失活时调用。

  **该钩子在服务器端渲染期间不被调用。**

# 十三、路由守卫

`vue-router` 提供的导航守卫主要用来通过跳转或取消的方式守卫导航。有多种机会植入路由导航过程中：全局的，单个路由独享的， 或者组件级的。

**参数或查询的改变并不会触发进入/离开的导航守卫**。可以通过监视 $route 对象来应对这些变化，或使用 `beforeRouteUpdate` 的组件内守卫。

## 13.1 全局前置路由守卫

```js
router.beforeEach((to, from, next) => {
    // ...
})
```

* `to`：即将要进入的目标路由对象
* `from`：正要离开的路由对象
* `next`：调用该方法来 **resolve** 这个钩子。并且要确保严格调用一次。
  * **`next()`**: 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 **confirmed** (确认的)。
  * **`next(false)`**: 中断当前的导航。如果浏览器的 URL 改变了 (可能是用户手动或者浏览器后退按钮)，那么 URL 地址会重置到 `from` 路由对应的地址。
  * **`next('/')` 或者 `next({ path: '/' })`**: 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。你可以向 `next` 传递任意位置对象，且允许设置诸如 `replace: true`、`name: 'home'` 之类的选项以及任何用在 [`router-link` 的 `to` prop](https://v3.router.vuejs.org/zh/api/#to) 或 [`router.push`](https://v3.router.vuejs.org/zh/api/#router-push) 中的选项。
  * **`next(error)`**: (2.4.0+) 如果传入 `next` 的参数是一个 `Error` 实例，则导航会被终止且该错误会被传递给 [`router.onError()`](https://v3.router.vuejs.org/zh/api/#router-onerror) 注册过的回调。

## 13.2 全局后置路由守卫

```js
router.afterEach((to, from) => {

})
```

## 13.3 独享路由守卫

在路由配置上直接定义 beforeEnter 守卫

```js
{
    path: '/about',
    component: () => import('../views/AboutView.vue'),
    beforeEnter: (to, from, next) => {
        next()
    }
},
```

## 13.4 组件内的路由守卫

```js
const Foo = {
  template: `...`,
  beforeRouteEnter(to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建
  },
  beforeRouteUpdate(to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
  },
  beforeRouteLeave(to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
  }
}
```

可以通过传一个回调给 `next`来访问组件实例。在导航被确认的时候执行回调，并且把组件实例作为回调方法的参数。

```js
beforeRouteEnter (to, from, next) {
  next(vm => {
    // 通过 `vm` 访问组件实例
  })
}
```

## 13.5 完整的导航解析流程

1. 导航被触发。
2. 在失活的组件里调用 `beforeRouteLeave` 守卫。
3. 调用全局的 `beforeEach` 守卫。
4. 在重用的组件里调用 `beforeRouteUpdate` 守卫 (2.2+)。
5. 在路由配置里调用 `beforeEnter`。
6. 解析异步路由组件。
7. 在被激活的组件里调用 `beforeRouteEnter`。
8. 调用全局的 `beforeResolve` 守卫 (2.5+)。
9. 导航被确认。
10. 调用全局的 `afterEach` 钩子。
11. 触发 DOM 更新。
12. 调用 `beforeRouteEnter` 守卫中传给 `next` 的回调函数，创建好的组件实例会作为回调函数的参数传入。

# 十四、hash 模式和 history 模式

## 14.1 hash模式

url 中会有一个 `#`，例如：`http://localhost:8080/#/user/1`

## 14.2 history 模式

开启 history 模式：

```js
const router = new VueRouter({
  mode: 'history',
  routes: [...]
})
```

在 history 模式下，url 中没有 `#` ，例如：`http://localhost:8080/user/1`。但是这需要后端的支持，不然会出现访问 url 返回 404 的情况。

