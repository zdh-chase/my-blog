---
title: VUE（三.vue-resource）
tags:
     - vue-resource
---

## vue-resource
### 全局配置
``` bash
Vue.http.options.root = '/root';
```
### 组件实例配置
在实例化组件时可以传入http选项来进行配置
``` bash
new Vue({
    http: {
        root: '/root',
        headers: {
            Authorization: ''
        }
    }
})
```
<!--more-->
### 全局调用
``` bash
Vue.http(option);
```
### 组件实例调用
``` bash
this.$http(option)
```
### vue-resource 提供的便捷方法
``` bash
get(url, [data], [options]);

post(url, [data], [options]);

put(url, [data], [options]);

patch(url, [data], [options]);

delete(url, [data], [options]);

jsonp(url, [data], [options]);
```

### get请求
``` bash
this.$http.get(url, {
  params: {
    userId: '11'
  },
  headers: {
    token: 'aaa'
  }
}).then(res => {
  //成功回调
},error => {
  //失败回调
})
```
### post请求
``` bash
this.$http.post(url, {
  userId: '111'
},{
  headers: {
    token: 'aaa'
  }
}).then(res => {
   //成功回调
})
```
### jsonp请求
``` bash
this.$http.jsonp(url, {
  userId: '111'
}).then(res => {
   //成功回调
})
```
### api请求
``` bash
this.$http({
  url: '',
  method: 'GET',
  params: {
    userId: '111'
  },
  headers: {
    token: 'aaa'
  },
  timeout: 50,
  before: function() {
    
  }
}).then(function (res) {
  //成功回调
})
```
## 拦截器
### 拦截器的注册
``` bash
Vue.http.interceptors.push({
    request: function ( request ) {
        // 更改请求类型为POST
        request.method = 'POST';
        return request;
    },
    response: function ( response ) {
        // 修改返回数据
        response.data = [{
            custom: 'custom'
        }];
        return response;
    }
});
```
### 工厂函数注册
``` bash
Vue.http.interceptors.push(function () {
    return {
        request: function ( request ) {
            return request;
        },
        response: function ( response ) {
            return response;
        }
    }
});
Vue.http.interceptors.push(function ( request, next ) {
    // 请求发送前的处理逻辑
    next(function () {    
        // 请求发送后的处理逻辑
        // 更具请求的状态， response参数会返回给 successCallback或errorCallback
        return response
    });    
});
```
### 实现的功能：
AJAX请求的loading界面
``` bash
Vue.http.interceptors.push((request, next) => {
    // 通过控制 组件的'v-show'值显示loading组件
    loading.show = true;
    next((response) => {
        loading.show = false
        return response
    });
});
```
## 请求选项对象
| 参数          | 类型                    | 描述                                       |
| ----------- | --------------------- | ---------------------------------------- |
| url         | string                | 请求的URL                                   |
| method      | string                | 请求的HTTP方法，例如：'GET', 'POST'或其他HTTP方法      |
| body        | Object,FormDatastring | request body                             |
| params      | Object                | 请求的URL参数对象                               |
| headers     | Object                | request header                           |
| timeout     | number                | 单位为毫秒的请求超时时间 (0 表示无超时时间)                 |
| before      | function(request)     | 请求发送前的处理函数，类似于jQuery的beforeSend函数        |
| progress    | function(event)       | ProgressEvent回调处理函数                      |
| credentials | boolean               | 表示跨域请求时是否需要使用凭证                          |
| emulateHTTP | boolean               | 发送PUT, PATCH, DELETE请求时以HTTP             |
| emulateJSON | boolean               | 将request body以application/x-www-form-urlencoded content type发送 |