---
title: 日常踩坑笔记本
tags:
     - 坑坑洼洼踩踩踩
---
记录日常开发各种坑坑洼洼，吾日三省吾身....

<!--more-->

## 1.IE8、IE9  ajax跨域不发起请求

W3C CORS（跨域资源共享）是对于跨域请求的一个规范，目前各个主流浏览器都实现了这个规范，IE8开始，增加了XDomainRequest作为跨域请求的对象，而Firefox与Chrom则CORS直接实现在XMLHttpRequest对象中，更方便使用。不过IE10中，XMLHttpRequest对象也实现了CORS。

XDomainRequest:
1.仅只是 GET 和 POST 两个方法。
2.支持的事件有：onerror，onload，onprogress，ontimeout
3.提供的方法：abort，open，send
4.提供的属性：contentType， responseText，timeout

demo:
``` bash
var url = 'xxxxxxxxx';
var xdr = new XDomainRequest();
xdr.onload = function() {           
    console.log(xdr.responseText);
};
xdr.onerror = function() {
    console.log("error");
};
 
xdr.timeout = 3000;
xdr.ontimeout = function() {
    console.log("timeout");
};
 
xdr.open("get", url);
xdr.send();

//兼容处理
if (window.XDomainRequest) {
  
}else if (window.XMLHttpRequest) {
  
}
```

## 2.IE10以下高德api接口返回数据未知错误

高德地图api接口
``` bash
http://restapi.amap.com/v3/geocode/geo?key=<您的key>&address=杭州
```
但在ie10以下返回的结果时未知错误。。查阅高德地图文档错误后，给出的反馈是传参时中文字符进行url编码转换
``` bash
'https://restapi.amap.com/v3/geocode/geo?key=<您的key>&address=' + encodeURI(地址)
```