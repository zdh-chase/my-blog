---
title: Gulp 配置（四）---模板篇
tags:
     - gulp
     - gulp-file-include
---
提取公共html页面为实际开发提供了便捷，这里引入gulp-file-include做公共模块复用。

## gulp-file-include

#### 需要引入的依赖

``` bash
var fileinclude  = require('gulp-file-include');  
```
<!--more-->

新建一个task:
``` bash
gulp.task('fileinclude', function() {
    // 适配src中所有文件夹下的所有html，排除src下的include文件夹中html
    gulp.src(['./src/view/*.html','!./src/view/common/*.html'])
        .pipe(fileinclude({
          prefix: '@@',
          basepath: '@file'
        }))
    .pipe(gulp.dest('./build/view/'));
});
```
gulp task已经写好了，这里新建两个公共html页面，header.html和footer.html

``` bash
header.html
<div>这是头部</div>

footer.html
<div>这是页脚</div>
```

## 引入公共组件

``` bash
 @@include('common/header.html')
    <div>主内容</div>
 @@include('common/footer.html')
```
## 编译后生产环境下的html

``` bash
<div>这是头部</div>
<div>主内容</div>
<div>这是页脚</div>
```

至此完成了html模板复用，结合上一章的自动刷新，更新下watch，将fileinclude放入watch中监听html，并刷新
``` bash
gulp.task('watch', function() {
  gulp.watch(paths.html,['fileinclude','html']);
  gulp.watch(paths.scripts,['mergeScripts','html']);
  gulp.watch(paths.scss,['mergeScss','html']);
})
```

over