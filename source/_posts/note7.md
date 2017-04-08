---
title: 前后端分离实践-gulp+express
tags:
     - 前后端分离
     - gulp
     - express
     - browser-sync
     - seajs
     - sass
     - ejs
---
## 技术栈：gulp+express+browser-sync+seajs+sass+ejs 
1.前端打包工具：gulp
2.express 做中间件和路由配置
3.browser-sync 浏览器自动刷新
4.seajs 模块化管理js
5.sass css预编译
6.ejs 模板引擎

<!--more-->

## 1.安装express

``` bash
$ npm install -g express-generator
```

## 2.新建工程

``` bash
$ express -e demo
$ cd demo && npm install
```

## 3.修改routes引入方式
app.js
``` bash
var express = require('express');
var routes = require('./routes/index');
var app = express();
routes(app);
```
/routes/index.js
``` bash
module.exports = function(app) {
  app.get('/', function (req, res) {
    res.render('index', { title: 'Express' });
  });
};
```


## 4.nodemon+browser-sync浏览器自动刷新
引入依赖
``` bash
var browserSync = require('browser-sync');
var nodemon = require('gulp-nodemon');
```

``` bash
// 浏览器同步，用7000端口去代理Express的3000端口
gulp.task('browser-sync', ['nodemon'], function() {
  browserSync.init(null, {
    notify: false,//关闭页面通知
    proxy: "http://localhost:3000",
    files: ["dist/views/*.*","dist/public/scss/*.*","dist/public/javascripts/*.*","dist/public/images/*.*"],
    browser: "chrome",
    port: 7000,
  });
});

// 开启Express服务
gulp.task('nodemon', function (cb) {
  
  var started = false;
  
  return nodemon({
    script: 'bin/www'
  }).on('start', function () {
    //为了避免nodemon开始多次
    if (!started) {
      cb();
      started = true; 
    } 
  });
}); 
```


## 5.http-proxy-middleware跨域

``` bash
app.use('/api', proxy({target: 'http://localhost:8080', changeOrigin: true}));
```

## 6.gulp配置

和之前的一样

## 7.ejs基本语法

无缓冲的条件语句元素
``` bash
<% code %>
```
转义HTML，该code并且会打印出来
``` bash
<%= code %>
```
非转义的buffering，该code并且会打印出来
``` bash
<%- code %>
```
内嵌别的文件
``` bash
<% include file %>
```
指定布局文件
``` bash    
<% layout(file) -%>
```
包含js脚本文件
``` bash
<% script(file) -%>
```
包含css文件
``` bash    
<% stylesheet(file) -%>
```
指定块内容
``` bash      
<% block(code, code) -%>  
```
## 8.已上传github
<a href="https://github.com/zdh-chase/gulp-cli" target="_blank">https://github.com/zdh-chase/gulp-cli</a>

over