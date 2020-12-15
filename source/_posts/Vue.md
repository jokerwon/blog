---
title: Vue
tag: [Frontend, JavaScript, Vue]
---

### 一、 入门

Vue.js（读音 /vjuː/, 类似于 view） 是一套构建用户界面的渐进式框架。Vue 只关注视图层， 采用自底向上增量开发的设计。Vue 的目标是通过尽可能简单的 API 实现响应的数据绑定和组合的视图组件。

<!-- more -->

#### 1.1  常用指令

- 插值表达式

- v-cloak

  解决插值表达式闪烁的问题

  ~~~html
  [v-cloak]{
   display: none;
  }
  
  <div id='app' v-cloak>
      {{ msg }}
  </div>
  ~~~

  ~~~js
  new Vue({
      el: '#app',
      data: {
          msg: 'Hello Vue'
      }
  })
  ~~~

- v-text

- v-html

- v-bind

- v-on

- v-model

- v-for

- v-if

- v-show

#### 1.2  事件修饰符

- .stop
- .prevent
- .capture
- .self

#### 1.3  Vue实例

#### 1.4  过滤器

#### 1.5  计算方法

#### 1.6  自定义指令

* 自定义全局指令

~~~javascript
/* Vue.directive(arg0,arg1)： 
 * @param： 指令名，引用时加前缀 -v
 * @param： 钩子函数对象
 *
 * 钩子函数参数：
 *  + el: 指令所绑定的元素，可以用来直接操作 DOM
 *  + binding: 一个对象，包含以下属性
 *      - name: 指令名，不包括 v- 前缀
 *  - value: 指令的绑定值，例如 v-my-directive="1+1",value值是 2
 *  - expression： 绑定值的字符串形式，例如上例中则为 "1+1"
 *  - oldValue: 指令绑定的前一个值，仅在 update 和 componentUpdated 钩子中可用
 *   - arg： 传给指令的参数。例如 v-my-directiv:foo, arg 的值为 "foo"
 *  - modifiers: 一个包含修饰符的对象。例如 v-my-directiv.foo.bar ,则其值为 { foo: true, bar: true }
 * + vnode: Vue 编译生成的虚拟节点
 * + oldVnode: 上一个虚拟节点，仅在 update 和 componentUpdated 钩子中可用
 */
Vue.directive('focus',{
    bind: function(el,binding,vnode) {},  //当指令绑定到元素上的时候执行，只执行一次
    inserted: function(el,,binding,vnode){},  //元素插入 DOM 中时执行，只执行一次
    updated: function(el,binding,vnode,oldVnode){},  //当 Vnode 更新时执行，可能多次
    unbind: function(el,binding,vnode){}  //当指令被解绑时执行，只执行一次
})
~~~

* 自定义私有指令

~~~js
new Vue({
    el: '#app',
    diretives: {
        'fontweight': {
            bind: function(el, binding){...},
   ...
        },
        //函数简写,该代码会应用到 bind 和 update 钩子函数中
        'focus': function(el, binding) {...}
    }
})
~~~

#### 1.7  Vue 实例的生命周期

- beforeCreate : 实例创建之前，此时 data 和 method 未被初始化。
- created：实例创建完成，data 和 method 初始化完成。最早可以操作 data 和 method 的时机。
- beforeMount：表示模板在内存中已经编译完成，但尚未渲染到页面中。
- mounted：模板已经被挂载到页面中。
- beforeUpdated： Model 和 View 中的一方数据被更新，另一方未被同步
- updated：双方的数据已经同步完成。
- beforeDestroy：即将销毁实例，但实例中的数据、方法等依旧可用。
- destroyed：实例销毁完成。

***在 create 之后 beforeMount 之前，Vue 的模板在内存中编译，在 mounted 之后才会渲染到页面中***

#### 1.8  axios 实现请求

### 二、 组件

==**template 的模板内容必须有且仅有一个唯一的根元素**==

#### 2.1  创建全局组件的方式

1. 使用 Vue.extend 方法创建

~~~js
//创建组件
var com1 = Vue.extend({
    template: '<h3>这是 Vue.extend 创建的组件</h3>',  //指定组件要展示的 html 的结构
})
Vue.component('myCom',com1)  //arg0: 组件名，arg1: 创建的组件对象。
//当组件名称用驼峰命名时，在引用的时候要用 - 连接符
~~~

```html
<div id="app">
    <my-com></my-com>
</div>
```

2. 直接使用 Vue.component 创建

~~~js
Vue.component('mycom1',{
    template: '<h3>这是 Vue.component 创建的组件</h3>'
})
~~~

3. 使用 template 标签创建

~~~html
<div id="app">
    <mycom2></mycom2>
</div>

<template id="temp">
 <div>
        <h2>这是 template 标签创建的组件</h2>
    </div>
</template>
~~~

~~~js
Vue.component('mycom2',{
    template: '#temp'
})
~~~

#### 2.2  创建私有组件的方式

~~~js
new Vue({
    el: '#app',
    components: {
        login: {
            template: '<h1>只是私有的组件<h1/>'
        }，
        index: {
         template: '<div></div>'
     }
    }
})
~~~

#### 2.3  组件中的 data

~~~js
Vue.component('mycom1',{
    template: '<h3>这是 Vue.component 创建的组件, {{ msg }}</h3>'，
    data: function(){
     return {
         msg: 'Hello World'
     }
 },
    method: {}
})
~~~

* 组件可以有自己的 data 数据
* 组件的 data 必须是个方法，且返回值必须是对象
* 组件的 data 的使用方式同实例的 data 一样。

#### 2.4  组件切换

~~~html
<div id="app">
    <!-- is 用来指定要显示的组件的名称 -->
    <component :is="comName"></component>
</div>
~~~

~~~js
Vue.component('login',{
    template: '<h3>登录组件</h3>'
})
Vue.component('register',{
    template: '<h3>注册组件</h3>'
})
new Vue({
    el: '#app',
    data: {
        comName: 'login'
    }
})
~~~

#### 2.5  组件过渡

~~~html
<div id='app'>
    <!-- 通过 mode 属性，设置切换的模式。-->
    <transition mode='out-in'>
     <component :is="comName"></component>
    </transition>
</div>
~~~

#### 2.6  组件通信

##### 2.6.1  通过 Prop 向子组件传值

~~~js
Vue.component('login',{
    template: '<h3>Hello {{ parent-msg }}</h3>',
    props: ['parent-msg']
})

new Vue({
    el: '#app',
    data: {
        msg: 'joker'
    }
})
~~~

~~~html
<div id='app'>
    <login :parent-msg='msg'></login>
</div>
~~~

​ **数据验证**

~~~js
Vue.component('my-component',{
    props: {
        propA: Number,
        propB: [String, Number],  //字符串或树值型
        propC: {
            type: Boolean,
            default: false  //默认为false
        },
        propD: {
            type: Number,
            required: true   //必传
        },
        //如果是数组或者对象，默认值必须是由一个函数来返回
        propE: {
            type: Arry,
            default: function(){
                return []
            }
        },
        //自定义验证函数
        propF: {
            validator: function(data) {
                return data>10
            }
        }
    }
})
~~~

##### 2.6.2  通过 $emit 向父组件传值

~~~html
<div id='app'>
    <com @func="show"></com>
</div>

<template id='tmpl'>
 <div>
        <button @click='myClick'>
            子组件的按钮
        </button>
    </div>
</template>
~~~

~~~js
var com = {
    template: '#tmpl',
    data(){
        return {
            son-data: { name: 'ha', age: 6 }
        }
    }
    methods: {
        myClick(){
            this.$emit('func',this.son-data);
        }
    }
}
new Vue({
    el: '#app',
    data: {
        data-from-son: null
    },
    method: {
        show(data){
            this.data-from-son = data
        }
    },
    components: {
        //com: com
        com
    }
})
~~~

#### 2.7  ref 获取 DOM 和引用组件

~~~html
<div id='app'>
    <h3 ref='myh3'>我是标题</h3>
    <button ref='mybtn'>子组件的按钮</button>
    <com ref='mycom'></com>
</div>

<template id='tmpl'>
    <h3>我也是标题</h3>
</template>
~~~

~~~js
new Vue({
    el: '#app',
    methods: {
        getElement(){
            console.log(this.$refs.myh3)
            console.log(this.$refs.mybtn)
            console.log(this.$refs.mycom.msg)
            this.$refs.mycom.show()
  }
    },
    components: {
     com: {
      template: '#tmpl',
            data(){
                return {msg: '...'}
            },
            methods: {
                show(){}
         }
  }
    }
})
~~~

### 三、 Render 函数

#### 3.1  createElement 用法

##### 3.1.1  基本参数

~~~js
createElement(
 //{ String | Object | Function }
    //一个 HTML 标签，组件组件选项，或一个 return 上述其一的函数
    'div',
    
    //{ Object }
    {},
    
    //{ String | Array }
    //子节点（VNodes），可选
    [
        createElement('h1','Hello World'),
        createElement(MyComponent,{
            props: {
                someProp: 'foo'
            }
        }),
        'bar'
    ]
)
~~~

**createElement( arg0, [ arg1, arg2 ] )**

1. arg0

   必选，可以是一个HTML标签，也可以是一个组件huo函数。

2. arg1

   可选，数据对象，在 template 中使用。具体属性见下

   ~~~js
   {
       //和 v-bind:class 一样的API
       'class': {
           foo: true,
           bar: false
       },
       
       //和 v-bind:style 一样的API
       style: {
        color: 'red',
           fontSize: '14px'
    },
           
       //正常的 HTML 属性
       attr: {
        id: 'foo'        
    },
           
    //组件props
    props: {
     myProp: 'bar'
    },
           
       //DOM属性
    domProps: {
     innerHTML: 'baz'
    },
       
       //自定义事件监听器 on
       //不支持如 v-on:keyup.enter 的修饰器，需要手动匹配 keyCode
       on: {
     click: this.clickHandler
    }
       ...
   }
   ~~~

3. arg2

   可选，子节点。

### 四、 使用 webpack

#### 4.1  webpack 基础配置

1. 安装 webpack 和 webpack-dev-server

~~~shell
npm init
npm install webpack --save-dev
npm install webpack-dev-server --save-dev
~~~

2. 创建 webpack.config.js 配置文件

### 五、 路由与 vue-router

#### 5.1  路由的基本使用

1. 引入 vue-router

2. 创建组件模板对象

   ```js
   let login = {
       template: '<h1>登录组件</h1>'
   }
   let register = {
       template: '<h1>注册组件</h1>'
   }
   ```

3. 创建路由对象

```js
var routerObj = new VueRouter({
    //路由匹配规则
    routes: [
        //每条规则都是一个对象，有两个必需属性
        //1. path： 表示监听的路由地址
        //2. component: 匹配后表示的组件。且其值为属性模板对象，而非组件应用名称
        { path: '/', redirect: '/login' },
        { path: '/login', component: login },
        { path: '/register', component: register }
    ]
})
```

4. 将路由规则对象注册到 Vue 实例中

```js
new Vue({
    el: '#app',
    router: routerObj
})
```

5. 在 html 页面中使用

```html
<div id='app'>
    <a href='#\login'>登录</a>
    <a href='#\register'>注册</a>
    
    <!-- <router-link 默认渲染为 a 标签，可以通过 tag 属性修改为 span -->
    <router-link to='/login' tag='span'>登录</router-link>
    <router-link to='/register'>注册</router-link>
    
    <!-- 占位符，匹配到的组件会展示到这个中 -->
    <router-view></router-view>
</div>
```

#### 5.2  传递参数

1. 使用 query 方式传递参数

   ```html
   <router-link to='/login?id=1&name=joe' tag='span'>登录</router-link>
   ```

   ```js
   let login = {
       template: '<h1>登录组件</h1>',
       created(){
           console.log(this.$route.query)  //{ id: '1', name: 'joe' }
       }
   }
   ```

2. 使用 params 方式传递参数

```js
let router = new VueRouter({
    routes: [
        { path: '/login/:id', component: login }
    ]
})
```

```html
<router-link to='/login/1' tag='span'>登录</router-link>
```

```js
let login = {
    template: '<h1>登录组件</h1>',
    created(){
        console.log(this.$route.params)  //{ id: '1' }
    }
}
```

#### 5.3  路由嵌套

```html
<div id='app'>
    <router-link to='/account'>Account</router-link>
    <router-view></router-view>
</div>

<template id='tmpl'>
 <div>
        <h1>account组件</h1>
        <router-link to='/login'>登录</router-link>
        <router-link to='/register'>注册</router-link>
        <router-view></router-view>
    </div>
</template>
```

```js
let account = {
    template: '#tmpl'
}
let login = {
    template: '<h3>登录组件</h3>'
}
let register = {
    template: '<h3>注册组件</h3>l'
}

let router = new VueRouter({
    routes: [{
        path: '/account', 
        component: account,
        children: [
            //path中不加'/'
            { path: 'login', component: login },
            { path: 'register', component: register }
        ]
    }]
})

new Vue({
    el: '#app',
    router
})
```

#### 5.4  使用命名视图

~~~js
var header = {
            template: '<h1 class="head">Header</h1>'
        }
        var leftBox = {
            template: '<h1 class="left">Left</h1>'
        }
        var mainBox = {
            template: '<h1 class="main">Main</h1>'
        }
        //创建路由对象
        const router = new VueRouter({
            routes: [
                //components 指定显示的组件，
                //默认为 header，当 router-view 的 name='left'时显示 leftBox
                { path: '/', components: {
                    'default': header,
                    'left': leftBox,
                    'main': mainBox
                } },
            ]
        })

        var app = new Vue({
            el: '#app',
            data: {},
            router
        })
~~~

~~~html
<div id='app'>
    <!-- 默认视图，即装载 header 组件 -->
 <router-view></router-view>
    <!-- 容器，实现流式布局 -->
 <div class="container">
        <!-- 装载 leftBox 组件 -->
  <router-view name='left'></router-view>
        <!-- 装载 mainBox 组件 -->
        <router-view name='main'></router-view>
 </div>
</div>
~~~

### 六、 状态管理与 Vuex
