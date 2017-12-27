---
title: VUE（五. mock数据渲染、图片懒加载、nprogress进度条）
tags:
     - axios
     - devServer
     - vue-lazyload
---
vue本地数据请求配置，最新版vue-webpack-template去掉了dev-server.js和dev-client.js 改用webpack.dev.conf.js代替，同时更新了config/index.js开发模式dev下几个配置:
``` bash
autoOpenBrowser：设置启动dev服务器，打开一个新的浏览器选项卡。默认为false,不启动。
useEslint：在webpack中使用eslint-loader。有些人可能只想在他们的IDE中使用eslint，所以可以在这里停用eslint-loader。
showEslintErrorsInOverlay：如果使用eslint-loader，则此选项允许切换浏览器中显示的错误覆盖图中的eslint错误。错误仍然出现在控制台和终端，但不要再打破浏览器的工作流程。notifyOnErrors：将在构建失败时显示操作系统通知。
```
<!--more-->
## 旧版dev-server.js配置本地数据访问：
在const app = express()后，const compiler = webpack(webpackConfig)前配置即可：
``` bash
var appData = require('../data.json')
var data1 = appData.data1
var data2 = appData.data2
var apiRoutes = express.Router()
//post请求
apiRoutes.post('/data1', function (req, res) {
  res.json({
    errno: 0,
    data: data1
  });
})
//get请求
apiRoutes.get('/data2', function (req, res) {
  res.json({
    errno: 0,
    data: data2
  });
})
app.use('/api',apiRoutes)
```
## 新版webpack.dev.conf.js配置本地数据访问：
在const portfinder = require(‘portfinder’)后添加
``` bash
//首先
const express = require('express')
const app = express()
var appData = require('../data.json')//加载本地数据文件
var data1 = appData.data1//获取对应的本地数据
var data2 = appData.data2
var apiRoutes = express.Router()
app.use('/api', apiRoutes)
```

``` bash
//然后找到devServer,在里面添加
before(app) {
  //get请求
  app.get('/api/data1', (req, res) => {
    res.json({
      errno: 0,
      data: data1
    })
  }),
  //post请求
  app.post('/api/data2', (req, res) => {
    res.json({
      errno: 0,
      data: data2
    })
  })
}
```
## axios组件数据渲染
main.js
``` bash
import axios from 'axios'
Vue.prototype.$http = axios
```
页面组件
``` bash
export default {
  name: 'list',
  data () {
    return {
      list: []
    }
  },
  mounted() {
    this.getList();
  },
  methods: {
    getList() {
      this.$http.get('/api/list').then(res => {
        this.list = res.data.result;
      })
    }
  }
}
```
## axios post请求参数传递格式问题，改成键值对形式
方法一：
``` bash
getList() {
      var params = new URLSearchParams()
      params.append('type', '1111');
      this.$http.post('/api/list', params).then(res => {
        this.list = res.data.result;
      })
    }
```
方法二，引入qs组件格式化数据：
main.js
``` bash
import qs from 'qs'
Vue.prototype.$qs = qs
```

``` bash
getList() {
      const params = {
        type: 2222
      }
      this.$http.post('/api/list', this.$qs.stringify(params)).then(res => {
        this.list = res.data.result;
      })
    }
```
## 图片懒加载
引入VueLazyload组件，npm官网地址：<a target="_blank" href="https://www.npmjs.com/package/vue-lazyload">https://www.npmjs.com/package/vue-lazyload</a>
在main.js引入组件
``` bash
import VueLazyload from 'vue-lazyload'

Vue.use(VueLazyload, {
  preLoad: 1.3,
  error: 'dist/error.png',
  loading: 'dist/loading.gif',
  attempt: 1
})
```
页面组件
``` bash
<img v-lazy="item.img"  />
```
## nprogress进度条

npm地址： <a href="https://www.npmjs.com/package/nprogress" target="_blank">https://www.npmjs.com/package/nprogress</a>

安装：
``` bash
$ bower install --save nprogress
$ npm install --save nprogress
```

路由跳转中加入顶部进度条过渡
man.js
``` bash
import router from './router'
import NProgress from 'nprogress'
import 'nprogress/nprogress.css'

router.beforeEach((to,from,next) => {
  NProgress.start()
  next()
})
router.afterEach(() => {
  NProgress.done();
})

```