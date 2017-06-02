---
title: 初探mongodb（二）
tags:
     - mongodb
     - node
     - express
     - mongoose
     - body-parser
     - robomongo
---
mongodb安装完成后，开始实战操作，这里使用express框架和mongodb可视化工具Robomongo一起搭配使用。

<!--more-->

## 1.安装express

npm安装太慢，推荐cnpm,使用淘宝镜像
``` bash
npm install -g cnpm --registry=https://registry.npm.taobao.org
```
全局安装：cnpm install -g express
项目范围内安装： cnpm install express --save

安装完express，在项目根目录新建app.js
``` bash
var express = require('express');
var app = express();

app.get('/', function (req, res) {
  res.send('Hello World!');
});

var server = app.listen(3000, function () {
  var host = server.address().address;
  var port = server.address().port;

  console.log('Example app listening at http://%s:%s', host, port);
});
```
打开cmd，cd至项目目录，启动node app.js即可运行服务

## 2.可视化工具Robomongo

<a href="https://robomongo.org/" target="_blank" >官网传送门</a>
下载安装完成后，打开Robomongo
<img src="/img/img8.png" />
点击Create,输入Name，以及Address，端口默认为27017，地址Address和端口号一般不用更改。
<img src="/img/img9.png" />
这样名为test的mongodb连接创建完毕。

## 3.MongoDB和MySQL

<img src="/img/img10.png" />


## 4.创建数据库

<img src="/img/img11.png" /><img src="/img/img12.png" />
这里取名testDB,右键testDB下的collection新建表
<img src="/img/img13.png" />
这样一张表就建好了，然后往表里添加字段和数据，右键表名点击insert documet
MongoDB数据库中文档(每一行的数据)的数据结构和JSON基本一样，所有存储在集合中的数据都是BSON格式，BSON是一种类JSON的一种二进制形式的存储格式，简称Binary JSON。因此，我们在插入数据时，只要像JSON格式那样输入我们想要插入的数据。点击save保存即可成功插入数据
``` bash
{
    userName:'zdh',
    age:18,
    sex:'男',
    job:'前端'
}
```
点击save即可保存至表中：
<img src="/img/img14.png" />


## 5.连接数据库

使用MongoDB原生自带的API来创建连接，直接在app.js输入以下代码
``` bash
var MongoClient = require('mongodb').MongoClient,
    DB_CONN_STR = 'mongodb://localhost:27017/testDB'; //数据库为 testDB
var selectData = function(db, callback) {  
  //连接到user表  
  var collection = db.collection('user');
  //查询数据
  var whereStr = {"userName":'zdh'};
  collection.find(whereStr).toArray(function(err, result) {
    if(err)
    {
      console.log('Error:'+ err);
      return;
    }     
    callback(result);
  });
} 
MongoClient.connect(DB_CONN_STR, function(err, db) {
  console.log("连接成功！");
  selectData(db, function(result) {
    console.log(result);
    db.close();
  });
});
```

## 6.使用Mongoose连接数据库

除了使用MongoDB原生自带的API来创建连接外，也可以使用Mongoose配合操作，mongoose文档（<a href="http://www.nodeclass.com/api/mongoose.html" target="_blank" >传送门</a>）

app.js中使用mongoose:
``` bash
const pkg = require('./package')
const express = require('express')
const bodyParser = require('body-parser')
const mongoose = require('mongoose')
const user = require('./api/user')

mongoose.connect('mongodb://localhost/testDB')
mongoose.Promise = global.Promise
 const db = mongoose.connection;
db.on('error', function () {
    console.log('Database connection error.');
});
db.once('open', function () {
    console.log('The Database has connected.')
});


const app = express()
const port = process.env.PORT || 3000

app.use(bodyParser.json())
app.use(bodyParser.urlencoded({ extended: true }))
app.use(express.static('dist'))
app.use('/api',user)

app.listen(port, () => {
  console.log(`${pkg.name} listening on port ${port}`)
})

module.exports = app
```
在项目根目录下新建了api文件夹，用于存放操作数据库的接口方法，api文件下user.js如下：
``` bash
const express = require('express')
const router = express.Router()
const User = require('../models/user')

//查询user表中的数据
router.get('/userList', (req, res) => {
  User.find(function(err,persons){
    if (err) {
        res.json(err)
    }else {
        res.json(persons)
    }
 })
})
//往user表中插入数据
router.get('/insertUser', (req,res) => {
  var user = new Movie({
      userName : 'zengdh',                
      age : '20',
      sex : '男',
      job : '前端攻城狮'
  });
  user.save(function (err, results) {

      if (err) {
          res.send("Error:" + err)
      }
      else {
          res.send({
            message: '数据添加成功',
            data: results
          })
      }
  });
})
```
在项目根目录下新建models文件夹，用于存放数据模型，这里models下user.js内容如下：
``` bash
const mongoose = require('mongoose')

const userSchema =new mongoose.Schema({
  userName:  { type: String },//用户名
  age:  { type: Number }, // 年龄
  sex:  { type: String },// 性别
  job:  { type: String }, // 工作
},{collection:'user'})
// 这边collection如果不写的话，在调用api接口的时候，会在数据库中新建一张后缀名加了s的表，即users

const Movie = module.exports = mongoose.model('user',userSchema)
```

## 7.调用接口

在package.json中加了启动服务的命令：
``` bash
  "server": "nodemon app.js"
```
运行 npm run server，浏览器输入"localhost:3000/api/userList" ，这是刚才在api文件夹下user.js中写的获取数据的接口，调用成功后返回内容如下：
<img src="/img/img15.png" />
这是刚才通过数据库直接插入表中的数据，接下来调用一下插入信息的接口，浏览器输入"localhost:3000/api/insertUser" ，调用成功后返回内容如下：
<img src="/img/img16.png" />
然后看下数据库：
<img src="/img/img17.png" />
数据插入成功了，大功告成。

over