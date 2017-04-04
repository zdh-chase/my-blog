---
title: Gulp 配置（四）---图片篇
tags:
     - base64
     - spriter
---
图片整合成雪碧图，几kb的图片转化成base64写入css中，减少http请求。

## gulp-base64

#### 需要引入的依赖

``` bash
var base64 = require('gulp-base64');
```
<!--more-->

新建一个task:
``` bash
gulp.task('base64', function() {
    return gulp.src('./build/scss/*')
        .pipe(base64({
            baseDir: './build/scss/',
            extensions: ['svg', 'png', /\.jpg#datauri$/i],
            exclude:    [/\.server\.(com|net)\/dynamic\//, '--live.jpg'],
            maxImageSize: 8*1024, // bytes 
            debug: true
        }))
        .pipe(gulp.dest('./build/scss'));
});
```

## gulp-css-spriter

#### 需要引入的依赖

``` bash
var spriter = require('gulp-css-spriter');
```

``` bash
gulp.task('mergeScss', ['cleanScss'],function (){
        var timestamp = +new Date();
        return gulp.src(['./static/scss/*.scss'])
            .pipe(sass().on('error', sass.logError))
            .pipe(spriter({
            // 生成的spriter的位置
            'spriteSheet': './build/img/sprite-'+timestamp+'.png',
            // 生成样式文件图片引用地址的路径
            // 如下将生产：backgound:url(../images/sprite20324232.png)
            'pathToSpriteSheetFromCSS': '../img/sprite-'+timestamp+'.png'
            }))
            .pipe(minifycss())
            .pipe(rev())                                            //- 文件名加MD5后缀
            .pipe(gulp.dest('./build/scss/'))
            .pipe(rev.manifest())                                   //- 生成一个rev-manifest.json
            .pipe(gulp.dest('./rev'))                              //- 将 rev-manifest.json 保存到 rev 目录内
            .pipe(notify({ message: 'mergeScss task ok' }));
});
```

css:
``` bash
.x{width: 50px;height: 50px;background: url(../img/x.png?__spriter) 0 0 no-repeat;}
.y{width: 50px;height: 50px;background: url(../img/y.png) 0 0 no-repeat;}
```

## 配置

gulp-css-spriter默认会对样式文件里，所有的background/background-image的图片合并，

但实际项目中，我们不是所有的图片都需要合并。

``` bash
background-image:url(../img/x.png?__spriter);//有?__spriter后缀的合并

background-image:url(../img/y.png);//不合并
```
修改gulp-css-spriter源文件

第43行：
``` bash
if(transformedDeclaration.property === 'background-image') {
			return cb(transformedDeclaration, declarationIndex, declarations);
		}
		// Background is a shorthand property so make sure `url()` is in there
		else if(transformedDeclaration.property === 'background') {
			var hasImageValue = spriterUtil.backgroundURLRegex.test(transformedDeclaration.value);

			if(hasImageValue) {
				return cb(transformedDeclaration, declarationIndex, declarations);
			}
		}
```
更新为：
``` bash
if(transformedDeclaration.property === 'background-image' && /\?__spriter/i.test(transformedDeclaration.value)) {

                    transformedDeclaration.value = transformedDeclaration.value.replace('?__spriter','');
                    return cb(transformedDeclaration, declarationIndex, declarations);
                }
                // Background is a shorthand property so make sure `url()` is in there 且判断url是否有?__spriter后缀
                else if(transformedDeclaration.property === 'background' && /\?__spriter/i.test(transformedDeclaration.value)) {

                    transformedDeclaration.value = transformedDeclaration.value.replace('?__spriter','');
                    var hasImageValue = spriterUtil.backgroundURLRegex.test(transformedDeclaration.value);

                    if(hasImageValue) {
                        return cb(transformedDeclaration, declarationIndex, declarations);
                    }
                }
```

over