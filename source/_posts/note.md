---
title: Gulp 配置（一）---seaJs篇
tags:
     - gulp
     - seajs
     - gulp-seajs-transport
---
Gulp.js 是基于 Node.js 构建的，可以建项目并减少频繁的 IO 操作的化构建工具
这里我采用 <a href="http://seajs.org/" target="_blank">seajs</a>来管理我的javascript代码，提高JavaScript代码的可读性和清晰度，解决目前JavaScript编程中普遍存在的依赖关系混乱和代码纠缠等问题，方便代码的编写和维护。
*  gulp安装就不赘述

## 引入seajs



``` bash
<script type="text/javascript" src="../../src/js/sea.js"></script>
```


<!--more-->


## 配置seajs

``` bash
seajs.config({
	 paths: {
             'src': '../../src/'  //路径配置
	 },
         alias: {//别名配置
             jquery:'jquery.js',
             index:'src/js/index.js'
         },
         map: [//映射配置
	     [ /^(.*\.(?:css|js))(.*)$/i, '$1?201703281819' ]
	 ],
	 preload: ['jquery']  // 预加载项
	 });
seajs.use(["jquery","index"]);  //模块加载
```

<a href="http://yslove.net/seajs/" target="_blank">更多配置项>></a>

## 新建两个js文件a.js和b.js

``` bash
a.js:
define(function(require, exports, module) {
	var AJS={
        init:function(){

        },
        aDom:function(){
        	console.log('来自a.js的内容');
        }
	};
	module.exports=AJS;
})

b.js:
define(function(require, exports, module) {
	var BJS={
        init:function(){

        },
        aDom:function(){
        	console.log('来自b.js的内容');
        }
	};
	module.exports=BJS;
})
```

## index.js引入a.js和b.js的方法
``` bash
define(function(require, exports, module) {
        var AJS=require('a');
        var BJS=require('b');
	var INDEX={
        init:function(){
            console.log('来自index.js的内容');
            AJS.aDom();
            BJS.bDom();
        }
	};
	module.exports=INDEX.init();
})
```

## 打开引入index.js的html文件

``` bash
seajs.use(["index"]); //引入index.js
```
<img src="/img/img1.png" />

这里可以看到index.js中成功引入了a.js和b.js的方法，再看一下Network
<img src="/img/img2.png" />
这边加载了三个js文件，这并不符合我的需求，我希望我引入某个js文件的方法时，只需要加载那个方法，而不是整个js文件都加载进来，所以我需要合并js，添加一个gulp task任务
``` bash
gulp.task('mergeScripts',function () {
        return gulp.src(['./src/js/a.js','./src/js/b.js','./src/js/index.js'])    
            .pipe(uglify({//压缩js
             mangle: true,//类型：Boolean 默认：true 是否修改变量名
             mangle: { except: ['require', 'exports', 'module', '$'] }//排除混淆关键字
             }))
            .pipe(concat('index.js'))  //生成js
            .pipe(gulp.dest('./build/js/'));  //生成合并后的js的路径   
});

```
``` bash
gulp mergeScripts 
```
<img src="/img/img3.png" />
合并是合并了，但是还是加载了三个js
这里需要引入gulp-seajs-transport来具象化js模块
```javascript
var transport = require('gulp-seajs-transport');
.pipe(transport());
```
<img src="/img/img4.png" />
完成，既加载了我所需要的方法，又减少了http请求
over