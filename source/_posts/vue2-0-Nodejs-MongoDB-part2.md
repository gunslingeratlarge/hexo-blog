title: vue2.0 + Nodejs + MongoDB part2
author: gsal
tags:
  - vue
categories:
  - 前端
date: 2018-05-03 20:15:00
---
### 分模块开发

每一个.vue文件就是一个模块，每一个模块分三部分，template、script、style，分别表示html的内容，逻辑和样式。而template标签下只能有一个大的根标签。 组件的数据不再只是一个对象了，而应该是一个方法，返回值为一个对象，（为什么呢？）
应该写为

``` js
export default {
  data: function() {
    return {
        inputValue: ''
    }
  }
}
```

在ES6中可以简化为：

``` js
data () {
    }
```

### vue-router
用来开发spa。

路由的内容主要有两块，一块是router文件夹下index.js中的内容，一块是每个组件里面的内容。  
在index.js中：
``` js

export default new Router({
    routes: [
        {
            path: '/goods/',
            name: 'GoodsList',
            component: GoodsList,
            children: [
                {
                    path:'title',
                    name: 'GoodTitle',
                    component:GoodTitle
                }, {
                    path:'image',
                    name: 'GoodImage',
                    component:GoodImage
                }
            ]

        },
        {
            path:'/cart',
            name:'cart',
            component:Cart
        }
    ],
    mode: 'history'
})

```
主要就是这个routes数组，这个数组是由几个对象组成的，每个对象都有自己的路径，还可以嵌套路径，一个路径对应一个组件。而这个组件放到哪里呢？放到其父组件的`<router-view></router-view>`位置。比如这里的GoodTitle就放到GoodsList的.vue文件中的`<router-view><router-view>`位置。  
在每个组件里，跳转到某个地址的a连接不再是a了，改为用`<router-link></router-link>`，而路径也可以通过`goods/:goodsId`的形式传递给组件，在组件的模板中使用{{$route.params.goodsId}}来获取。  
也可以直接通过编程式的路由在写跳转，如下：
``` js
    <template>
        <button @click="goToCart">通过编程式路由跳转到购物车</button>
    </template>
    <script>
    export default {
        data() {
            return  {}
        },
        methods: {
            goToCart() {
                this.$router.push({path:'/cart?goodsId=123'});
            }
        }
    }
</script>

```

#### 命名路由
``` js
<router-link v-bind:to"{name:'cart',params:{cartId:123}}">跳转到购物车页面</router-link>
```
这个就对应着跳转到/cart/:cartId这个页面，并且把cartId=123作为params传递过去。