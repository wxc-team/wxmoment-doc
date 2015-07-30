title: "自定义详情页前端通用库"
date: "2014-03-16 18:17:16"
---

WxMoment 是由微信朋友圈广告团队面向广告详情页开发者提供的一个 JavaScript 库。通过使用 WxMoment，开发者可以简单的实现详情页中的常见功能，例如：微信分享、横屏提示、网页统计等。

前往 Github 查看 [WxMoment Example](https://github.com/wxc-team/WxMoment/tree/master/example)。

### 引用脚本

当前版本：0.0.3
最后更新：2015-07-24

```
<script src="//wximg.qq.com/wxp/libs/wxmoment/0.0.3/wxmoment.min.js"></script>
```
最新的脚本地址及文件将会在 [Github 库](https://github.com/wxc-team/WxMoment) 中更新。


### 数据统计

开发者使用数据统计功能前，请与商务团队提前确认统计监测点及统计的项目别名（例如：20150504WXMOMENT）

**注意：网页统计需要部署到腾讯服务器才可正常上报统计数据**

```javascript
var wa = new WxMoment.Analytics({
    //projectName 请与微信商务团队确认
    projectName: "20150504WXMOMENT"
});

```

支持自定义事件统计，例如预约按钮的点击事件、视频的播放事件等。

```javascript
$('#button01').click(function(){
    //两个必填参数，分别为: 事件分类、事件名称
    wa.sendEvent('click', 'button01');
});
```

### 横屏提示

若不支持横屏或横屏下体验不佳，请添加横屏提示。

```javascript
new WxMoment.OrientationTip();
```

注：开启后，PC 等大屏幕设备打开时，会显示移动版尺寸预览并提供页面二维码展示

### 资源预加载
 
当图片较多时，对于页面的打开速度和后续的体验都非常影响，所以，我们通常让资源提前加载，即有一个 `loading` 的过程，当资源加载好以后再进入实际页面。

```javascript
var basePath = "http://wximg.gtimg.com/some/path/"

var loader = new WxMoment.Loader();

//声明资源文件列表
var fileList = ['img/1.png', 'img/2.png', 'img/3.png', 'img/4.png'];

for (var i = 0; i < fileList.length; i++) {
    loader.addImage(basePath + fileList[i]);
}

//进度监听
loader.addProgressListener(function (e) {
    var percent = Math.round((e.completedCount / e.totalCount) * 100);
    console.log("当前加载了", percent, "%");
    //在这里做 Loading 页面中百分比的显示
});

//加载完成
loader.addCompletionListener(function () {
    //可以在这里隐藏 Loading 页面开始进入主内容页面
});

//启动加载
loader.start();
```

### 滑屏组件


```html
<!DOCTYPE html>
<html>
<head>
    <style>
        html, body {height: 100%;-webkit-box-sizing: border-box;box-sizing: border-box;overflow: hidden;}
        .wrap{width: 100%;height: 100%;overflow: hidden;}
        .screen{width: 100%;height: 100%;overflow: hidden;-webkit-backface-visibility: hidden;-webkit-perspective: 1000;}
    </style>
</head>
<body>
    <div class="wrap">
        <section class="screen screen1">
            <div class="screen-arrow"></div>
        </section>
        <section class="screen screen2">
            <div class="screen-arrow"></div>
        </section>
        <section class="screen screen3">
            <div class="screen-arrow"></div>
        </section>
    </div>
</body>
<html>
```

**脚本调用**

```javascript
var pageSlider = new WxMoment.PageSlider({
    pages: $('.screen')
});

//可用接口
pageSlider.prev() //上一屏
pageSlider.next() //下一屏
pageSlider.moveTo(n) //跳转到第 n 屏，有缓动效果
pageSlider.moveTo(n, true) //直接跳到第 n 屏，没有缓动效果
```



**可用参数**
```javascript
var pageSlider = new WxMoment.PageSlider({
    pages: $('.screen'),            //必需，需要切换的所有屏
    direction: 'vertical',          //可选，vertical 或 v 为上下滑动，horizontal 或 h 为左右滑动，默认为 vertical
    currentClass: 'current',        //可选, 当前屏的class (方便实现内容的进场动画)，默认值为 'current'
    rememberLastVisited: true,      //可选，记住上一次访问结束后的索引值，可用于实现页面返回后是否回到上次访问的页面
    animationPlayOnce: false        //可选，切换页面时，动画只执行一次
    oninit: function () {},         //可选，初始化完成时的回调
    onbeforechange: function () {}, //可选，开始切换前的回调
    onchange: function () {},       //可选，每一屏切换完成时的回调
    onSwipeUp: function () {},      //可选，swipeUp 回调
    onSwipeDown: function () {},    //可选，swipeDown 回调
    onSwipeLeft: function () {},    //可选，swipeLeft 回调
    onSwipeRight: function () {}    //可选，swipeRight 回调
});
```

**禁用滑屏**
有时候需要锁定屏幕，不响应用户的滑动事件，需要点击某按钮或完成某些操作后再开启滑屏。
如下：第二屏在第一次上滑时锁住，第二次上滑时再滑动：

```html
<!--
data-lock-next： 禁止往后滑
data-lock-prev： 禁止往前滑
-->
<section class="screen screen2" data-lock-next="true"></section>
```
```javascript
var pageSlider = new WxMoment.PageSlider({
    pages: $('.screen'),
    onSwipeUp: function(){
        //所以回调接口的 this 均指向 pageSlider 实例
        if(this.index === 1){
            //做你想做的操作.....

            //最后取消禁止
            $(this.pages[this.index]).data('lock-next', false);
        }
    }
});
```
**记住最后浏览的页面索引**
当页面跳走返回时，希望能直接返回到上次跳走的页面，只需设置：`rememberLastVisited: true`，
即会保存当前页面索引到 `localstorage`，当返回时即可方便操作，如下：
```javascript
var pageSlider = new WxMoment.PageSlider({
    pages: $('.screen'),
    rememberLastVisited: true,
    oninit: function(){
        //返回时，需告诉我们此时为返回动作而不是刷新，可以通过 hash 告诉我们
        //PageSlider 所有回调接口 this 指向 PageSlider，方便进行操作
        if(返回为 true){
            this.moveTo(this.lastVisitedIndex, true);
        }
    }
});
```

### 微信分享

新版 Weixin JS SDK 的分享接口需要绑定域名，并通过 access_token 获取 jsapi_ticket，由于获取 jsapi_ticket 的 API 调用次数非常有限，频繁刷新 jsapi_ticket 会导致 API 调用受限，影响自身业务。

详情页最终会部署到腾讯服务器 `wximg.qq.com` 域名下，允许继续使用 WeixinJSBridge 调用分享接口。

**注意：此微信分享实现方式要求部署到腾讯服务器后方可正常使用**

在 HEAD 中加入以下代码

```html
<meta name="wxm:timeline_title" content="分享到朋友圈的标题">
<meta name="wxm:appmessage_title" content="分享给好友的标题">
<meta name="wxm:appmessage_desc" content="分享给好友的详情">
<!-- 分享缩略图，必须为绝对路径-->
<meta name="wxm:img_url" content="http://wximg.gtimg.com/tmt/_demo/wxmoment/img/thumb.jpg">
<!-- 分享链接-->
<meta name="wxm:link" content="http://wximg.gtimg.com/tmt/_demo/wxmoment/share.html">
```

在 JS 中初始化分享组件

```javascript
var share = new WxMoment.Share();
```

一些特定需求下，需要动态修改分享的内容，更多参数请查看 example

```javascript
share.set('appmessage', 'title', "使用 set 函数重新设置标题");
```

### 腾讯视频

在腾讯视频上传视频后，可以获取到这样的视频地址 `http://v.qq.com/boke/page/i/0/o/b001672wkoe.html`，其中 `b001672wkoe` 即视频的 vid。

代码

```html
<!--初始化 WxMoment.Video 后，默认注入视频相关 DOM 到 ID 为 WxmomentVideo 的元素中，视频宽高在此设置-->
<div id="WxMomentVideo" style="width: 320px; height: 320px;"></div>
```

```javascript
var video = new WxMoment.Video({
    //请联系接口人确认视频清晰度已调至高清版本
    //如果要定制“播放按钮”的样式，请使用 CSS 覆盖 .tvp_overlay_play 和 .tvp_button_play 即可

    vid: "a0016gys8ct", //视频 vid 取自视频地址：http://v.qq.com/page/a/t/t/a0016gys8ct.html
    pic: "http://wximg.gtimg.com/tmt/_demo/wxmoment/img/video-thumb.jpg", //设置视频默认缩略图
    oninited: function () {
        //播放器在视频载入完毕触发
    },
    onplaying: function () {
        //播放器真正开始播放视频第一帧画面时
    },
    onpause: function () {
        //播放器触发暂停时，目前只针对HTML5播放器有效
    },
    onresume: function () {
        //暂停后继续播放时触发
    },
    onallended: function () {
        //播放器播放完毕时
    },
    onfullscreen: function (isfull) {
        //onfullscreen(isfull) 播放器触发全屏/非全屏时，参数isfull表示当前是否是全屏
    }
});

//可以通过以下方式控制播放/暂停
//video.getPlayer().play();
//video.getPlayer().pause();

//以下可以拉起iOS全屏播放
//video.getPlayer().enterFullScreen();
```