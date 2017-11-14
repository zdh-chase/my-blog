---
title: Node.js 路由配置及模板引入
tags:
     - node.js
     - nunjucks
     - express
     - request-promise
---
node.js的一些基本路由配置，以及前端模板引擎引入，多接口请求并发及服务端渲染数据，跨域请求转发等..

## express框架及nunjucks模板引入
``` bash
var express = require('express');
var nunjucks = require('nunjucks');

var app = express();
nunjucks.configure('views', {
    autoescape: true,
    express: app,
    watch:true,
    noCache: true
});
// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'html');
```
<!--more-->
## 路由配置routers.js
第一种写法：
``` bash
var express = require('express');
var router = express.Router();

router.get('/', function(req, res, next) {
    res.render('页面地址', { title: ''});
});
module.exports = router;
```
第二种写法：
``` bash
module.exports = function(app) {
  app.get('自定义路由地址', function (req, res) {
    res.render('页面地址', { title: ''});
  });
};
```
## 服务端接口请求
单条接口请求
``` bash
var express = require('express');
var router = express.Router();
var request = require('request');

router.get('/', function(req,res,next) {
   request({url: '',gzip:true,headers:{'User-Agent': 'chrome'}},function(error, response, body) {
      var data =  JSON.parse(body);//数据处理
      res.render('页面地址', { data: data});
  }})
})
```
多条接口并发请求
``` bash
var request = require('request-promise');
var obj = function(app) {
  app.get('路由地址', function (req, res) {
    var api1 = request({url:'接口地址',gzip:true,headers:{'User-Agent': 'chrome'}});
    var api2 = request({url:'接口地址',gzip:true,headers:{'User-Agent': 'chrome'}});
    Promise.all([api1,api2]).then(function(results) {
        var data1 = JSON.parse(results[0]).result.obj;
        var data2 = JSON.parse(results[1]).result.obj;
        res.render('页面地址', { title: '', data1: data1, data2: data2});
    }).catch(function(){
      
    });
  });
}
module.exports = obj;
```
## app.js 引入路由配置
第一种：
``` bash
var obj = require('router文件夹下的单个路由配置文件');
var app = express();
app.use('自定义路由地址', obj);
```
第二种：
``` bash
var obj = require('router文件夹下的单个路由配置文件');
var app = express();
obj(app);
```
## 错误路由指向
当页面发生404、500等错误时，路由配置指向
``` bash
app.use(function(err, req, res, next) {
  //set locals, only providing error in development
  //res.locals.message = err.message;
  //res.locals.error = req.app.get('env') === 'development' ? err:err;

  // render the error page
  res.status(err.status);//http错误状态码
  res.render('error页面地址',{error:err});//根据错误状态码渲染不同模板数据
});
```