---
title: Gulp 配置（二）---sass篇
tags:
     - gulp
     - css
     - sass
---
sass、less、stylus都是css预处理框架，它可以提高开发效率，编写富有表现力的、动态的、健壮的CSS。

*  这里选择sass

## gulp css task

#### 需要引入的依赖
``` bash
var minifycss = require('gulp-minify-css'), //压缩css
    rev = require('gulp-rev');                                  //- 对文件名加MD5后缀
```
<!--more-->
``` bash
gulp.task('mergeCss',['cleanCss'], function() {                                //- 创建一个名为 mergeCss 的 task
        return gulp.src(['./src/css/*.css'])    //- 需要处理的css文件，放到一个字符串数组里
            //.pipe(concat(*))   //- 合并后的文件名
            .pipe(minifycss())          //- 压缩处理成一行
            .pipe(rev())                                            //- 文件名加MD5后缀
            .pipe(gulp.dest('./build/css/'))   //- 输出文件本地*/
            .pipe(rev.manifest())                                   //- 生成一个rev-manifest.json
            .pipe(gulp.dest('./rev'));                              //- 将 rev-manifest.json 保存到 rev 目录内
});
```
普通css文件的gulp task任务，主要是css压缩+MD5时间戳+HTML路径更换


## sass

在开发过程中，sass需要编译才能使用，可以用命令行编译，gui编译（推荐koala），编辑器编译，在线编译等等，这里我结合gulp引入gulp-sass来编译。
sass文件后缀分两种，scss和sass，一个使用{}，一个不使用，我选择scss，可读性高一些。
#### 引入依赖
``` bash
var sass = require('gulp-sass'); 
```
#### task任务

``` bash
gulp.task('mergeScss', ['cleanScss'],function (){
        return gulp.src(['./src/scss/*.scss'])
            .pipe(sass().on('error', sass.logError))
            .pipe(rev())                                            //- 文件名加MD5后缀
            .pipe(gulp.dest('./build/scss/'))
            .pipe(rev.manifest())                                   //- 生成一个rev-manifest.json
            .pipe(gulp.dest('./rev'));                              //- 将 rev-manifest.json 保存到 rev 目录内
});
```

这里编译后的css文件名是通过原文件名和md5以连字符形式命名的（例：a-f7e3d6869b.css），每次修改css后生成的文件名都不一样，需要清除原有的旧文件，否则会堆积很多没用的css，我更喜欢在html中引入css时在路径后面加‘?v={time}’的形式，这里我选择去修改node_modules里面的js文件来满足我的需求。

## 打开 node_modules\gulp-rev\index.js

第144行：
``` bash
manifest[originalFile] = revisionedFile;
```
更新为：
``` bash
manifest[originalFile] = originalFile + '?v=' + file.revHash; //file.revHash是md5字符串，可以换成new Date().getTime()来获取当前时间的毫秒戳
```

## 打开 nodemodules\gulp-rev\nodemodules\rev-path\index.js

第10行：
``` bash
return filename + '-' + hash + ext;
```
更新为：
``` bash
return filename + ext;
```

## 打开 node_modules\gulp-rev-collector\index.js

第31行：
``` bash
if ( !_.isString(json[key]) || path.basename(json[key]).replace(new RegExp( opts.revSuffix ), '' ) !==  path.basename(key) ) {
```
更新为：
``` bash
if ( path.basename(json[key]).split('?')[0] !== path.basename(key) ) {
```
## 路径替换

引入依赖
``` bash
var revCollector = require('gulp-rev-collector'); 
```
gulp task

``` bash
gulp.task('scssrev',['mergeScss'],function() {
  return gulp.src( ['./rev/*.json','./build/view/*.html'])                                    //- 读取 rev-manifest.json 文件以及需要进行css名替换的文件
               .pipe(revCollector(
                   {
                    replaceReved: true
                    /*dirReplacements: {//指定路径替换
                        '/static/scss': '/build/scss'
                    }*/
                   }
                ))                                   //- 执行文件内css名的替换
               .pipe(gulp.dest("./build/view/"));                   //- 替换后的文件输出的目录    
});
```
最后这些编译后的css文件会以*.css?v={time}的形式引入，解决浏览器缓存机制。
over