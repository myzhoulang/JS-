# 使用nuxt.js实现Vue SSR项目总结

>在当前的项目开发中，前后端分离已成为趋势，服务端提供API，客户端通过ajax调用API 获取数据，动态的将数据插入到模板中，实现前后端分离。这样也可以多客户端(移动端，h5...)对应一个服务端。但是这样内容是通过 js 去获取后在动态插入的，一些搜索引擎是不会去爬去js代码的，导致爬虫抓取的内容都是一些无用的，SEO有问题。怎么样既能前后端完全分离，又解决SEO问题？ 使用nuxt.js 是可以实现这种需求的一种方式。 
>
>nuxt.js 基于vue.js 的一个实现SSR框架。SSR(Server Side Rendering)服务端渲染。就是页面内容在服务端拼装完成后，直接将内容发送给客户端，也称之为页面直出。但是nuxt.js 也有缺点， nuxt.js 是基于vue的，而vue是不能支持IE8的。如果不需支持IE9以下的项目，则完全可以使用nuxt.js。



### 初始化一个项目

> 可以使用vue 的脚手架工具实现一个项目的初始化，它提供了多种模板，方便用户根据自己的喜好， 选择对应的模板。非常方便简单。下面的express 是其中模板的一种。 其他的还有： starter 、koa

```sh
vue init nuxt/express <项目名称>
```

> 完成后，需要在创建的项目根目录下执行以下命令，安装依赖

```sh
cd <项目名称>
yarn
```

> 依赖完成后执行

```sh
yarn run dev
```

> 这时候nuxt 就为你在本地开启3000端口的web服务, 你可以在浏览器中访问： http://127.0.0.1:3000，可以看到nuxt.js 提供的demo 。nuxt 会观测目录中的文件，只要文件被修改，浏览器会自动做局部刷新。当修改了配置文件或者添加page,都无需自己手动重启服务，nuxt已经帮你做好了这些。



### 代理的配置

> 前后端分离的项目 跨域是不可避免的一个问题。nuxt.js 可以使用nuxt 官方提供模块中的proxy 包。
>
> 使用一下命令将这个包安装到开发依赖。上线可以使用Nginx解决跨域。

```sh
yarn add @nuxtjs/proxy --save-dev
```

> 在nuxt.config.js中配置需要代理的服务，ws 是是否代理websockets， 默认是true, 这里设置了false
>
> 代理中的选项有很多 具体可查看： https://github.com/chimurai/http-proxy-middleware#shorthand

```js
{
    ...,
    modules: [
    ['@nuxtjs/proxy']
  ],
  proxy: {
    '/webapi/v2': {
      target: api 服务地址,
      ws: false
    },
    '/sso': {
      target: api 服务地址,
      ws: false
    }
  },
}
```

> nuxt.js 默认会安装axios 这个ajax请求库 如果我们使用这个库的话 需要在配置一下这个库
>
> 可以在plugins文件夹中新建一个JS 文件 在里面判断服务端渲染的时候配置这个代理地址。

```js
import * as axios from 'axios'

let options = {
  params: {
    t: +new Date()
  }
}
// The server-side needs a full url to works
if (process.server) {
  options.baseURL = '代理服务器地址'
}

export default axios.create(options)
```

> 项目中使用请求需导出这个js文件 这个axios.js 是包装过后的， 你可以使用任意合法的名称

```js
import axios from '~/plugins/axios'

// 使用方式和 官方的axios一样
axios.get(`/api/xxx`)
```



### 样式文件指定预编译助理器

> 在项目中可以使用 scss,less等一些预处理文件，只需在nuxt.config.js中指定需要哪一个预处理器处理即可。新版本无需指定，内部已经根据文件后缀名自动处理了

```js
module.exports = {
  css: [
    '~assets/css/main.css',						// 直接指定css 文件 无需使用预处理器
    { src: '~assets/css/index', lang: 'scss'},	// 指定一个模块 里面可以 import 多个scss
    {src: '~assets/css/a.scss', lang: 'scss'}	// 指定具体某个scss文件
  ]
}
```

### Meta 标签覆盖

> 在子组件中需要覆盖父组件meta 标签，需要使用hid键配置一个唯一的标识编号。
>
> 可以使用组件中的数据属性

```js
export default {
  data (){
    return {
      count: 5
    }
  },
  heade() {
    return {
    	title: '内页title',
      	meta: [
        	{ hid: 'description', name: 'description', content: `${ count }内页描述`}
      	]  
    }
  }
}
```

> 

### plugins 文件夹

> 项目的 filter,directive,和第三方UI组件等等，需要放在这个目录下，然后在nuxt.config.js配置文件中指定
>
> SSR 代表是否在服务端渲染时候处理 因为服务端没有BOM和DOM 对象。如果js 里面执行了这些， 就会报错
>
> 可以使用 ssr: false 这个选项

nuxt.config.js

```js
{
  ...,
    plugins: [
      '~plugins/element-ui',
      '~plugins/filter',
      '~plugins/directive',
      '~plugins/checkLogin',
      { src: '~plugins/baidu', ssr: false }
    ],
}
```

### asyncData 

> 服务端渲染需要在渲染前获取数据，可以使用async在渲染组件之前获取异步数据。获取成功后会与data选项下的数据合并(可以在组件里面使用这些属性)
>
> 需要注意**这个方法只会在页面组件** (page 目录下的组件)加载前被调用。其他地方都不会执行这个函数。
>
> 在这个函数中是没有办法使用`this`来获取组件的其他信息，因为这个时候组件还没有被初始化
>
> 这个函数在被调用时候第一个参数被设为当前页面的上下文对象 ，这个对象有很多属性可以使用
>
> 更多属性查看：https://zh.nuxtjs.org/api

```javascript
async asyncData({ query }){
    const [books, musics] = await Promise.all([
        axios.get(`/api/books`),
      	axios.get(`/api/musics`)
    ])
    
    return {
      books: books,
      musics: musics
    }
}
```





### nuxtServerInit

> `asyncData` 和`fetch` 只能在页面组件中才会被调用。当我们需要在其他组件中调用的api数据需要做服务端渲染的时候。我们可以在页面渲染前，在服务端先将数据请求后存储到`stroe` 中。`nuxtServerInit`可以实现这些。我们只需要在`stroe`中指定这个`nuxtServerInit` 方法。

```javascript
actions: {
    async nuxtServerInit({ commit }, { req }){
        const { user } = await axios.get(`/api/user`)
        commit('SET_USER', user)
    }
}
```



### Fetch

> fetch 用于在渲染页面前填充store, 它不会设置组件的数据。同时他也只能在**页面组件下**才会被调用
>
> 需要异步的时候，需要返回一个promise或使用async await 语法

```js
export default {
  fetch ({ store, params }) {
    return axios.get(`http://api.douban.com/books`)
    	.then((res) {
          store.commit('SET_BOOKS', res.data)
    	})
  }
}

// 或者
export default {
  async fetch ({ store, params }) {
    const { data } = await axios.get(`http://api.douban.com/book`)
    store.commit('SET_BOOKS', data)
  }
}
```



### asyncData、fetch 和 nuxtServerInit的区别

|                | 执行区      | 数据存储       | 执行机制      |
| -------------- | -------- | ---------- | --------- |
| asyncData      | pages目录下 | 数据插入到模板    | 页面刷新及路由跳转 |
| fetch          | page目录下  | 数据存储在store | 页面刷新及路由跳转 |
| nuxtServerInit | store中   | 数据存储在store | 页面刷新      |



#### Nuxt 路由

> Nuxt 根据 pages 的目录结构生成 vue-router 路由文件配置 

### 基础路由

`pages` 目录结构如下：

   ```javascript
pages/
--| users/
----| shop.vue
----| index.vue
--| index.vue
   ```

Nuxt 生成对应的路由配置

如下：

```javascript
router: {
  routes: [
    {
      path: '/',
      name: 'index',
      component: 'pages/index.vue'
    },
    {
      path: '/users',
      name: 'users',
      component: 'pages/users/index.vue'
    },
    {
      path: '/users/shop',
      name: 'users-shop',
      component: 'pages/users/shop.vue'
    }
  ]
}
```

#### 动态路由

`pages`目录结构如下：

```javascript
pages/
--| users/
----| index.vue
----| _id.vue
--| index.vue
```

Nuxt 生成对应的路由配置：

```javascript
router: {
  routes: [
    {
      path: '/',
      name: 'index',
      component: 'pages/index.vue'
    },
    {
      path: '/users',
      name: 'users',
      component: 'pages/users/index.vue'
    },
    {
      path: '/users/:id',
      name: 'users-id',
      component: 'pages/users/_id.vue'
    }
  ]
}
```

#### 嵌套路由

`pages`目录如下：

```javascript
pages/
--| shops/
----| _id/
------| index.vue
------| product.vue
----| _id.vue
----| index.vue
--| index.vue
```

Nuxt 生成对应的路由配置：

```javascript
router: {
  routes: [
    {
      path: '/',
      name: 'index',
      component: 'pages/index.vue'
    },
    {
      path: '/shops',
      name: 'shops',
      component: 'pages/shops/index.vue'
    },
    {
      path: '/shops/:id',
      component: 'pages/shops/_id.vue',
      children: [
        {
          path: '',
          name: 'shops-id',
          component: 'pages/shops/_id/index.vue'
        },
        {
		  path: '/product',
          name: 'shops-id-product',
          component: 'pages/shops/_id/product.vue'
        }
      ]
    }
  ]
}
```
#### 自定义路由

> 在nuxt.config.js中可以使用`extendRoutes`选项来扩展路由。

```js
const resolve = require('path').resolve

module.exports = {
  router : {
    extendRoutes (routes) {
      routes.push({
        name: 'custom router',
        path: '/customRouter.html',
        component: resolve(__dirname, 'pages/custom.vue')
      })
    }
  }
}
```

