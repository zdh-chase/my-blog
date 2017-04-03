---
title: Gulp 配置（三）---service篇
tags:
     - gulp
     - gulp-watch
     - gulp-connect
---
实际开发中，如果每次修改html、sass、js都要手动去执行编译命令，这将是一件很痛苦的事，所以配置一个本地服务，让它实时编译刷新是很必要的。

## gulp-connect

#### 需要引入的依赖

``` bash
var connect = require('gulp-connect');
```
<!--more-->

新建一个task:
``` bash
gulp.task('connect', function() {
    connect.server({
       // host: '192.168.x.x', //地址，可不写，不写的话，默认localhost
        port: 3000, //端口号，可不写，默认8000
        root: './', //当前项目主目录
        livereload: true //自动刷新
    });
});
```
这样就能在本地启一个服务了，但是这样还不够，我们需要去监听项目的文件是否发生更改，如果有更改就自动去编译，这里需要用到gulp-watch


## gulp-watch

引入依赖
``` bash
var sass = require('gulp-watch'); 
```
新建一个task

``` bash
var paths = {
  scripts: ['./src/js/*.js'],
  scss: './src/scss/*.scss',
};
gulp.task('watch', function() {
  gulp.watch(paths.scripts,['mergeScripts']);
  gulp.watch(paths.scss,['mergeScss']);
})
```

用watch去监听文件，当js和css发生变化时自动编译生成，但是每次都需要按F5才能更新页面，这里再写一个task去自动刷新页面，毕竟我这么懒，F5也懒得去按。

再新建一个task
``` bash
gulp.task('html', function() {
    gulp.src('./build/view/*.html')//这里的路径时生产文件的路径
        .pipe(connect.reload())
});
```
将它放入watch中
``` bash
gulp.watch(paths.html,['html']);
gulp.watch(paths.scripts,['mergeScripts','html']);
gulp.watch(paths.scss,['mergeScss','html']);
```

这样就可以在修改源文件的同时，让gulp去自动生成需要的生产文件，这里再合并一下task，丢在一个task里面，方便使用

``` bash
gulp.task('service', ['connect', 'watch']);
```

over