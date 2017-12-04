---
title: VUE（一.通过vue-cli搭建项目）
tags:
     - vue.js
     - webpack
---
通过vue-cli脚手架搭建项目，vue-cli的github地址：<a href="https://github.com/vuejs/vue-cli" target="_blank">https://github.com/vuejs/vue-cli</a>

## 安装node环境
node官网<a href="http://nodejs.cn/" target="_blank">http://nodejs.cn/</a>，安装完成后可以通过node -v和npm -v查看版本

<!--more-->
## 安装vue-cli
镜像安装
``` bash
npm install -g cnpm --registry=https://registry.npm.taobao.org
```
npm缓存清理指令
``` bash
npm cache clean
```
vue-cli全局安装
``` bash
cnpm install -g vue-cli
```
## 项目生成
生成指令
``` bash
vue init webpack vue-test
//或者 vue init webpack-simple vue-test
```
除了webpack模板还有其他的，详情<a href="https://github.com/vuejs-templates" target="_blank">https://github.com/vuejs-templates</a>
<img src="/img/vue1.png"/>

## 项目启动与打包
安装node_modules
``` bash
cnpm install
```
启动指令
``` bash
npm run dev
```
打包指令
``` bash
npm run build
```