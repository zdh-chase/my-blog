---
title: VUE（二.vue-router 路由配置）
tags:
     - vue.js
     - vue-router
---

## 基本配置
``` bash
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'HelloWorld',
      component: HelloWorld
    }
  ]
})
```
<!--more-->
## 动态路由
``` bash
export default new Router({
  routes: [
    {
      path: '/test/:testId',//单个动态参数
      //path: '/test/:testId/user/:name',多个动态参数
      name: 'HelloWorld',
      component: HelloWorld
    }
  ]
})
```
通过{{ $route.params.testId }}可以获取当前页面动态参数的值

### router-link映射路由
方式一：
``` bash
<router-link to="/test/1111">跳转</router-link>//直接传入参数值拼接url
//如果有多个动态参数
<router-link to="/test/1111/user/2222">跳转</router-link>
```
方式二：
``` bash
<router-link  v-bind:to="{ name:'HelloWorld', params: {testId: 555} }">跳转</router-link>//name为指定页面的name值，params中为动态参数需要的参数和值
//如果有多个动态参数
<router-link  v-bind:to="{ name:'HelloWorld', params: {testId: 555, userId: 333} }">
```
### url显示形式
默认的url打开在端口号后面会跟一个#，例如：
``` bash
http://localhost:8080/#/test/123
```
这是默认的hash写法，如果要替换成传统写法，需要在router中，修改mode的值
``` bash
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'

Vue.use(Router)

export default new Router({
  mode: 'history',//传统写法
  //mode: 'hash',//hash写法，默认
  routes: [
    {
      path: '/',
      name: 'HelloWorld',
      component: HelloWorld
    }
  ]
})
```
## 嵌套路由
首页在父级页面添加<router-view>，用来渲染通过路由映射过来的组件
``` bash
<template>
  <div class="hello">
    父级页面
    <router-view/>
  </div>
</template>
```
修改路由配置，用children去嵌套子路由
``` bash
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'
import Test1 from '@/page/test1'
import Test2 from '@/page/test2'

Vue.use(Router)
export default new Router({
  mode: 'history',
  routes: [
    {
      path: '/test',
      name: 'HelloWorld',
      component: HelloWorld,
      children: [
        {
          path: 'test1',
          name: 'test1',
          component: Test1
        },
        {
          path: 'test2',
          name: 'test2',
          component: Test2
        }
      ]
    }
  ]
})
```
在配置的路由后面，添加 children，并在 children 中添加二级路由，就能实现路由嵌套
配置 path 的时候，以 " / " 开头的嵌套路径会被当作根路径，所以子路由的 path 不需要添加 " / "
父页面中添加<router-link>映射路由
``` bash
<template>
  <div class="hello">
    父级页面
    <router-link to="/test/test1">测试一</router-link>
    <router-link to="/test/test2">测试二</router-link>
    <router-view/>
  </div>
</template>
```
### query参数传递
router-link映射路由
``` bash
//方式一：
<router-link to="/test/test1?name=zdh">跳转</router-link>
//方式二：
<router-link v-bind:to="{path: '/test/test1', query: { name: 'zdh' }}">跳转</router-link>
```
获取当前页面的参数传值
``` bash
{{ $route.query.name }}
```

### params参数传递
用params传值需要动态路由配置，上面已经提到过动态路由的配置了，params传值有两种方式，一种是显示在url上，一种是不显示在url上
#### 显示在url上
路由配置
``` bash
export default new Router({
  mode: 'history',
  routes: [
    {
      path: '/test',
      name: 'HelloWorld',
      component: HelloWorld,
      children: [
        {
          path: 'test1/:num',//写上需要传参的值
          name: 'test1',
          component: Test1
        }
      ]
    }
  ]
})
```
这样在view层用vue-link映射时，都会在url上显示传参的值
#### 不显示在url上
路由配置
``` bash
export default new Router({
  mode: 'history',
  routes: [
    {
      path: '/test',
      name: 'HelloWorld',
      component: HelloWorld,
      children: [
        {
          path: 'test1/',//需要传参的值不填写
          name: 'test1',
          component: Test1
        }
      ]
    }
  ]
})
```
vue-link映射
``` bash
<router-link v-bind:to="{ name:'test1', params: {num: 888} }">跳转</router-link>
```
总结：query是页面url之间的参数传递，params是路由之间的参数传递