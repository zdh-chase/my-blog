---
title: 移动端开发笔记本
tags:
     - 移动端wap开发
---
移动wap开发笔记...

<!--more-->

## 1.移动端rem布局

通过js动态计算html的font-size值，通过rem作为单位进行开发，推荐使用sass、scss等预编译css开发工具，方便数值转换，或者用sublime插件转换
js代码:
``` bash
function resize(){
  var e=(navigator.userAgent,+document.documentElement.clientWidth/750)*100;
  window.remFontSize=e=100>e?e:100,
  document.documentElement.style.fontSize=e+"px"
}
var b=null,ua=navigator.userAgent;window.addEventListener("resize",function(){clearTimeout(b),b=setTimeout(resize,50)},!1),resize()
```
或者采用手淘flexible计算
``` bash
;(function(win, lib) {
    var doc = win.document;
    var docEl = doc.documentElement;
    var metaEl = doc.querySelector('meta[name="viewport"]');
    var flexibleEl = doc.querySelector('meta[name="flexible"]');
    var dpr = 0;
    var scale = 0;
    var tid;
    var flexible = lib.flexible || (lib.flexible = {});

    if (metaEl) {
        //console.warn('将根据已有的meta标签来设置缩放比例');
        var match = metaEl.getAttribute('content').match(/initial\-scale=([\d\.]+)/);
        if (match) {
            scale = parseFloat(match[1]);
            dpr = parseInt(1 / scale);
        }
    } else if (flexibleEl) {
        var content = flexibleEl.getAttribute('content');
        if (content) {
            var initialDpr = content.match(/initial\-dpr=([\d\.]+)/);
            var maximumDpr = content.match(/maximum\-dpr=([\d\.]+)/);
            if (initialDpr) {
                dpr = parseFloat(initialDpr[1]);
                scale = parseFloat((1 / dpr).toFixed(2));
            }
            if (maximumDpr) {
                dpr = parseFloat(maximumDpr[1]);
                scale = parseFloat((1 / dpr).toFixed(2));
            }
        }
    }

    if (!dpr && !scale) {
        var isAndroid = win.navigator.appVersion.match(/android/gi);
        var isIPhone = win.navigator.appVersion.match(/iphone/gi);
        var devicePixelRatio = win.devicePixelRatio;
        if (isIPhone) {
            // iOS下，对于2和3的屏，用2倍的方案，其余的用1倍方案
            if (devicePixelRatio >= 3 && (!dpr || dpr >= 3)) {
                dpr = 3;
            } else if (devicePixelRatio >= 2 && (!dpr || dpr >= 2)){
                dpr = 2;
            } else {
                dpr = 1;
            }
        } else {
            // 其他设备下，仍旧使用1倍的方案
            dpr = 1;
        }
        scale = 1 / dpr;
    }

    docEl.setAttribute('data-dpr', dpr);
    if (!metaEl) {
        metaEl = doc.createElement('meta');
        metaEl.setAttribute('name', 'viewport');
        metaEl.setAttribute('content', 'initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no');
        if (docEl.firstElementChild) {
            docEl.firstElementChild.appendChild(metaEl);
        } else {
            var wrap = doc.createElement('div');
            wrap.appendChild(metaEl);
            doc.write(wrap.innerHTML);
        }
    }

    function refreshRem(){
        var width = docEl.getBoundingClientRect().width;
        // if (width / dpr > 540) {
        //     width = 540 * dpr;
        // }
        //var rem = width / 10;
        var rem = 100 * (width / 750);
        docEl.style.fontSize = rem + 'px';
        flexible.rem = win.rem = rem;
    }

    win.addEventListener('resize', function() {
        clearTimeout(tid);
        tid = setTimeout(refreshRem, 300);
    }, false);
    win.addEventListener('pageshow', function(e) {
        if (e.persisted) {
            clearTimeout(tid);
            tid = setTimeout(refreshRem, 300);
        }
    }, false);

    if (doc.readyState === 'complete') {
        doc.body.style.fontSize = 12 * dpr + 'px';
    } else {
        doc.addEventListener('DOMContentLoaded', function(e) {
            doc.body.style.fontSize = 12 * dpr + 'px';
        }, false);
    }


    refreshRem();

    flexible.dpr = win.dpr = dpr;
    flexible.refreshRem = refreshRem;
    flexible.rem2px = function(d) {
        var val = parseFloat(d) * this.rem;
        if (typeof d === 'string' && d.match(/rem$/)) {
            val += 'px';
        }
        return val;
    }
    flexible.px2rem = function(d) {
        var val = parseFloat(d) / this.rem;
        if (typeof d === 'string' && d.match(/px$/)) {
            val += 'rem';
        }
        return val;
    }

})(window, window['lib'] || (window['lib'] = {}));

```
## 2.移动端IOS click事件点击无效
解决方案：
  1.将 click 事件直接绑定到目标元素（即 .target）上
  2.将目标元素换成 a或者 button 等可点击的元素
  3.将 click 事件委托到非 document 或 body 的父级元素上
  4.给目标元素加一条样式规则 cursor: pointer;
推荐使用第4种方案。

## 3.移动端wap视频
开发需求：检测当前网络状态，如果处于非wifi状态下，提示用户该网络处于非wifi状态，播放视频需要消耗手机流量
html5组件：video
第三方组件：tvp.player.js   zepto.js
开发思路：
1.网络状态检测
code:
``` bash
var wifi = true;
var ua = window.navigator.userAgent;
var con = window.navigator.connection;
// 如果是微信
if(/MicroMessenger/.test(ua)){
// 如果是微信6.0以上版本，用UA来判断
if(/NetType/.test(ua)){
if(ua.match(/NetType\/(\S*)/)[1] != 'WIFI'){
wifi = false;
}
// 如果是微信6.0以下版本，调用微信私有接口WeixinJSBridge
}else{
document.addEventListener("WeixinJSBridgeReady",function onBridgeReady(){
WeixinJSBridge.invoke('getNetworkType',{},function(e){
if(e.err_msg != "network_type:wifi"){
wifi = false;
}
});
});
}
// 如果支持navigator.connection
}else if(con){
var network = con.type;
if(network != "wifi" && network != "2" && network != "unknown"){  //unknown是为了兼容Chrome Canary
wifi = false;
}
};
```
2.初始化video组件
``` bash
var video = new tvp.VideoInfo();
var videoId = '';//视频vid
var videoPic = '';//默认图片地址
video.setVid(videoId);//视频vid
var player = new tvp.Player();//视频高宽
player.setCurVideo(video);
player.addParam("autoplay","0");//是否自动播放，1为自动播放，0为不自动播放
player.addParam("showend",0);
player.addParam("adplay",0);
player.addParam("wmode","transparent");
player.addParam("controls","0");
player.addParam("preload","none");
player.addParam("pic",videoPic);//默认图片地址
//  player.addParam("flashskin", "");//是否调用精简皮肤，不使用则删掉此行代码
player.write("videoCon");
```
3.判断网络状态选择不同的显示方式
4.点击播放时，Android会自动全屏并显示加载动画百分比，ios会延迟反应，等加载一段才弹出视频窗口，这里对ios做一个加载检测处理，在弹出视频窗口之前做一个动画过渡
``` bash
video.onplay = function() {
var checkPlay=setInterval(function(){//检测currentTime是否开始播放
if(video.currentTime>0) {
//去掉加载动画，结束定时器检测
clearInterval(checkPlay);
};
},50)
}
//点击继续播放事件
$('body').on('click','播放按钮',function(){
//去掉遮罩，显示加载动画
video.play();
})
```