## 概述

`HTML5` 提供了很多新的功能，主要有：

```
新的 HTML 元素，例如 section, nav, header, footer, article 等
用于绘画的 Canvas 元素
用于多媒体播放的 video 和 audio 元素
用于定位的 Geolocation API
本地存储以及离线应用
Web Workers、Web WebSocket API

```

移动前端开发可分为：

1. 手机网页开发。这部分跟web前端开发差别不大，使用的技术都是`html+css+js`。区别为手机浏览器是`webkit`的天下，pc端是IE的天下。
2. `app`前端开发。使用的技术也是`html+css+js`，但它需要基于`PhoneGap`，`React Native`等开发平台调用手机核心功能接口（包括地理定位，加速器，联系人，声音和振动等）模拟`native app`，这部分跟web前端开发完全不同。最终代码发布要分别编译成各系统平台的`app`。

## canvas

`canvas`部分请参考：[http://segmentfault.com/a/1190000000661407](http://segmentfault.com/a/1190000000661407)

## drag and drop(拖拽)

### 拖拽元素事件 : 事件对象为被拖拽元素

```
dragstart :  拖拽前触发 
drag :拖拽前、拖拽结束之间，连续触发
dragend  : 拖拽结束触发

```

### 目标元素事件 : 事件对象为目标元素

```
dragenter :  进入目标元素触发，相当于mouseover
dragover : 进入目标、离开目标之间，连续触发
dragleave :  离开目标元素触发，相当于mouseout
drop  :  在目标元素上释放鼠标触发

```

### 事件的执行顺序 ：drop不触发的时候

```
dragstart  >  drag >  dragenter >  dragover >  dragleave > dragend 

```

### 事件的执行顺序 ：drop触发的时候(dragover的时候阻止默认事件)

```
dragstart  >  drag >  dragenter >  dragover >  drop > dragend

```

### 解决火狐下的问题

火狐浏览器下必须设置`dataTransfer`对象的`setData`方法才可以拖拽除图片外的其他标签。

### dataTransfer属性和方法

```
    属性              描述
    dropEffect      设置或获取拖曳操作的类型和要显示的光标类型。
    effectAllowed    设置光标样式(none, copy, copyLink, copyMove, link, linkMove, move, all 和 uninitialized)

    setDragImage    三个参数：指定的元素，坐标X，坐标Y
    files           获取外部拖拽的文件，返回一个filesList列表。filesList下有个type属性，返回文件的类型


    方法            描述
    clearData   通过 dataTransfer 或 clipboardData 对象从剪贴板删除一种或多种数据格式。

    setData() : 设置数据 key和value(必须是字符串)

    getData() : 获取数据，根据key值，获取对应的value

```

只有加阻止默认事件才可以触发`drop`

```
 oDiv.ondragover = function(ev){  //只有加阻止默认事件才可以触发drop
        ev.preventDefault();
    };   

```

`drag and drop`的浏览器支持情况：

`drag and drop`的浏览器支持情况：
![img](https://segmentfault.com/img/bVlwZe)

### HTML5实现拖拽上传预览图片：

```
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
    <title>无标题文档</title>
    <style type="text/css">
        #div1{
            margin: 0 auto;
            width:200px;
            height:200px;
            background:#ccc;
            color: white;}
        #div1 p{
            position: relative;
            top: 90px;
            left: 50px;
        }
        li{ width:200px; height:200px; margin:5px; float:left; list-style:none;}
        li img{ width:500px;height: auto}
    </style>
    <script type="text/javascript">

        window.onload = function(){

            var oUl = document.getElementById('ul1');
            var oDiv = document.getElementById('div1');

            oDiv.ondragenter = function(){
                this.innerHTML = '可以释放';
            };

            oDiv.ondragover = function(ev){
                ev.preventDefault();
            };

            oDiv.ondragleave = function(){
                this.innerHTML = '请拖拽到此区域';
            };

            oDiv.ondrop = function(ev){

                ev.preventDefault();

                var fs = ev.dataTransfer.files;

                for(var i=0;i<fs.length;i++){

                    var fr = new FileReader();

                    if( fs[i].type.indexOf('image')!=-1 ){

                        fr.readAsDataURL( fs[i] );

                        fr.onload = function(){
                            var oLi = document.createElement('li');
                            var oImg = document.createElement('img');
                            oImg.src = this.result;
                            oLi.appendChild( oImg );
                            oUl.appendChild( oLi );
                        };
                    }
                    else{
                        alert('亲,请拖拽图片格式');
                    }

                }

            };

        };
    </script>
</head>
<body>
<div id="div1"><p>请拖拽到此区域</p></div>
<ul id="ul1"></ul>
</body>

```

### FileReader(读取文件信息)

| 属性名        | 描述                         |
| ---------- | -------------------------- |
| error      | 在读取文件时发生的错误. 只读.           |
| readyState | 表明FileReader对象的当前状态.       |
| result     | 读取到的文件内容。这个属性只在读取操作完成之后才有效 |

| 方法名                            | 描述                                |
| ------------------------------ | --------------------------------- |
| abort                          | 中止该读取操作.在返回时,readyState属性的值为DONE. |
| readAsArrayBuffer              | 将File对象F读取为一个 ArrayBuffer 对象。     |
| readAsBinaryString             | 将File对象F读取为一个二进制字符串。              |
| readAsDataURL                  | 将File对象F读取为编码过的数据URL。             |
| readAsText(File f, [encoding]) | 读取 File对象F并赋予一个字符串。               |

创建一个`FileReader`对象：

```
var reader = new FileReader();

```

## Web Worker和缓存

`Web Worker`的基本原理就是在当前`js`的主线程中，使用`Worker`类加载一个`js`文件来开辟一个新的线程，起到互不阻塞执行的效果，并且提供主线程和新线程之间数据交换的接口：`postMessage，onmessage`。

主机 `worker` 和 `worker` 脚本可以通过 `postMessage` 发送消息并使用 `onmessage` 事件侦听响应。消息的内容作为事件的数据属性进行发送。

```
var worker = new Worker('worker.js');
worker.onmessage = function(e) {
  alert(e.data);
};

```

### worker主线程:

```
1.通过 worker = new Worker( url ) 加载一个JS文件来创建一个worker，同时返回一个worker实例。

2.通过worker.postMessage( data ) 方法来向worker发送数据。

3.绑定worker.onmessage方法来接收worker发送过来的数据。

4.可以使用 worker.terminate() 来终止一个worker的执行。

```

### worker新线程：

```
1.通过postMessage( data ) 方法来向主线程发送数据。

2.绑定onmessage方法来接收主线程发送过来的数据。

```

### 例子：计数功能

首先创建一个`index.html`文件

```
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <script type="text/javascript" src="index.js"></script>
</head>
<body>
<div id="numDiv">0</div>
<button type="button" id="start">start</button>
<button type="button" id="stop">stop</button>
</body>
</html>

```

其次，创建一个`count.js`文件：

```
var countNum = 0;
function count(){
    postMessage(countNum);
    countNum++;
    setTimeout(count,1000);
}

count();

```

再创建一个`index.js`文件

```
var numDiv;
var work = null;
window.onload = function () {
    numDiv = document.getElementById('numDiv');

    document.getElementById('start').onclick = startWorker;
    document.getElementById('stop').onclick = function () {
        if(work){
            work.terminate();//停止，释放掉资源
            work = null;//work重新初始化
        }
    };

};
function startWorker(){
    if(work){
        return;
    }
    work = new Worker('count.js');
    work.onmessage = function (e) {
        numDiv.innerHTML = e.data;
    };
}

```

![img](https://segmentfault.com/img/bVlw3w)

## Web SQL

三个核心方法：

```
1、openDatabase：这个方法使用现有数据库或创建新数据库创建数据库对象。

2、transaction：这个方法允许我们根据情况控制事务提交或回滚。

3、executeSql：这个方法用于执行真实的SQL查询。 

```

浏览器的本地数据库占用资源少，处理速度快。

浏览器的本地数据库占用资源少，处理速度快。
`openDatabase`方法打开一个已经存在的数据库，如果数据库不存在，它还可以创建数据库，创建并打开数据库的语法如下：

```
var db = openDatabase('mydb', '1.0', 'Test DB', 2 * 1024 * 1024);

```

参数为：数据库名（mydb）、版本号（1.0）、描述（Test DB）和数据库大小（`2*1024*1024`）以及创建回调函数

## WebSockets

`TCP`是因特网的基础传输协议，而`WebSocket`是`Web`应用程序的传输协议，它提供了双向的，按序到达的数据流。`WebSocket`连接的是`URL`，而非因特网上的主机和端口。

```
 // 创建一个Socket实例
var socket = new WebSocket('ws://localhost:8080');

// 打开Socket
socket.onopen = function(ev) {

    // 发送一个初始化消息
    socket.send('hello HTML5');

    // 监听消息
    socket.onmessage = function(ev) {
        console.log('Client received a message',ev);
    };

    // 监听Socket的关闭
    socket.onclose = function(ev) {
        console.log('Client notified socket has closed',ev);
    };

    // 关闭Socket....
    socket.close()
};

```

同`http`协议使用`http://`开头一样，`WebSocket`协议的`URL`使用`ws://`开头，而安全的`WebSocket`协议使用`wss://`开头。

`HTTP`协议通常承载于`TCP`协议之上，有时也承载于`TLS`或`SSL`协议层之上，这个时候，就成了我们常说的`HTTPS`。

`HTTP`协议通常承载于`TCP`协议之上，有时也承载于`TLS`或`SSL`协议层之上，这个时候，就成了我们常说的`HTTPS`。
![img](https://segmentfault.com/img/bVlxvo)

`HTTP`协议通常承载于`TCP`协议之上，有时也承载于`TLS`或`SSL`协议层之上，这个时候，就成了我们常说的`HTTPS`。
![img](https://segmentfault.com/img/bVlxvo)
默认`HTTP`的端口号为`80`，`HTTPS`的端口号为`443`。

## getUserMedia API

`navigator.getUserMedia`可以提示用户需要权限去使用像摄像头或麦克风之类的媒体设备.

`navigator.getUserMedia`可以提示用户需要权限去使用像摄像头或麦克风之类的媒体设备.
下面是一个获取用户摄像头并提供拍照功能的例子：

```
<body>
<video id="video" width="640" height="480" autoplay></video>
<button id="snap">Snap Photo</button>
<canvas id="canvas" width="640" height="480"></canvas>
<script type="text/javascript">

    // 添加事件监听器
    window.addEventListener("DOMContentLoaded", function() {
        // 获取元素，创建设置等等
        var canvas = document.getElementById("canvas"),
                context = canvas.getContext("2d"),
                video = document.getElementById("video"),
                videoObj = { "video": true },
                errBack = function(error) {
                    console.log("Video capture error: ", error.code);
                };

        // 添加video 监听器
        if(navigator.getUserMedia) { // 标准
            navigator.getUserMedia(videoObj, function(stream) {
                video.src = stream;
                video.play();
            }, errBack);
        } else if(navigator.webkitGetUserMedia) { // WebKit 前缀
            navigator.webkitGetUserMedia(videoObj, function(stream){
                video.src = window.webkitURL.createObjectURL(stream);
                video.play();
            }, errBack);
        }
        else if(navigator.mozGetUserMedia) { // Firefox 前缀
            navigator.mozGetUserMedia(videoObj, function(stream){
                video.src = window.URL.createObjectURL(stream);
                video.play();
            }, errBack);
        }

        document.getElementById("snap").addEventListener("click", function() {
            context.drawImage(video, 0, 0, 640, 480);
        });

    }, false);

</script>
</body>

```

下面是浏览器兼容性的写法：

```
<script type = 'text/javascript'>
    navigator.getMedia = ( navigator.getUserMedia ||
        navigator.webkitGetUserMedia ||
        navigator.mozGetUserMedia ||
        navigator.msGetUserMedia);
</script>

```

### 浏览器对象

![img](https://segmentfault.com/img/bVlxQS)

### audio和video

下图是`audio`和`video`的属性及其浏览器的兼容性：

下图是`audio`和`video`的属性及其浏览器的兼容性：
![img](https://segmentfault.com/img/bVlxye)

## Fullscreen API

### 进入全屏模式

```
// 找到正确的方法  
function launchFullScreen(element) {  
  if(element.requestFullScreen) {  
    element.requestFullScreen();  
  } else if(element.mozRequestFullScreen) {  
    element.mozRequestFullScreen();  
  } else if(element.webkitRequestFullScreen) {  
    element.webkitRequestFullScreen();  
  }  
}  

// 启动全屏模式  
launchFullScreen(document.documentElement); // 整个页面  
launchFullScreen(document.getElementById("videoElement")); // 单独元素 

```

### 退出全屏模式

```
function cancelFullscreen() {  
  if(document.cancelFullScreen) {  
    document.cancelFullScreen();  
  } else if(document.mozCancelFullScreen) {  
    document.mozCancelFullScreen();  
  } else if(document.webkitCancelFullScreen) {  
    document.webkitCancelFullScreen();  
  }  
}  

// 取消全屏  
cancelFullscreen();   

```

### 全屏属性和事件

```
document.fullScreenElement：当前全屏显示的元素。
document.fullScreenEnabled：判断浏览器是否支持全屏。
fullscreenchange事件：全屏状态改变事件。

```

## History API

HTML5 更新了两个API，无刷新更新地址 `history.pushState`方法和`history.replaceState` 方法

## Geolocation API

`Geolocation API` 是通过`window.navigator.geolocation` 获得对地理定位的访问的。该对象有如下三个方法：

```
getCurrentPosition()
watchPosition()
clearWatch()

```

![img](https://segmentfault.com/img/bVlxP8)

## 表单元素

`HTML5` 新增了很多表单元素让开发者构建更优秀的 `Web` 应用程序。以下是HTML5新增的表单元素

```
datalist
datetime
output
keygen 
date 
month 
week
time
color
number 
range 
email 
url

```

![img](https://segmentfault.com/img/bVlxMF)

## html5移动端优化

`PC`端的优化对于移动端同样适用，`Android`同时支持4个并发请求，`iOS 5`后可支持6个，所以，应尽量减少`http`请求数

![img](https://segmentfault.com/img/bVlxNE)

```
1. PC优化手段在Mobile侧同样适用
2. 在Mobile侧我们提出三秒种渲染完成首屏指标
3. 基于第二点，首屏加载3秒完成或使用Loading
4. 基于联通3G网络平均338KB/s(2.71Mb/s)，所以首屏资源不应超过1014KB
5. Mobile侧因手机配置原因，除加载外渲染速度也是优化重点
6. 基于第五点，要合理处理代码减少渲染损耗
7. 基于第二、第五点，所有影响首屏加载和渲染的代码应在处理逻辑中后置
8. 加载完成后用户交互使用时也需注意性能

```

![img](https://segmentfault.com/img/bVlxNL)

### 不滥用Float

```
Float在渲染时计算量比较大，尽量减少使用

```

### 不滥用Web字体

```
Web字体需要下载，解析，重绘当前页面，尽量减少使用

```

### 不声明过多的Font-size

```
过多的Font-size引发CSS树的效率

```

### 减少重绘和回流

```
a) 避免不必要的Dom操作
b) 尽量改变Class而不是Style，使用classList代替className
c) 避免使用document.write
d) 减少drawImage

```

`Viewport`可以加速页面的渲染，请使用以下代码

```
< meta name=”viewport” content=”width=device-width, initial-scale=1″>

```

### 动画优化

```
a) 尽量使用CSS3动画
b) 合理使用requestAnimationFrame动画代替setTimeout
c) 适当使用Canvas动画 5个元素以内使用css动画，5个以上使用Canvas动画（iOS8可使用webGL）

```

### GPU加速

```
CSS中以下属性（CSS3 transitions、CSS3 3D transforms、Opacity、Canvas、WebGL、Video）来触发GPU渲染，请合理使用
PS：过渡使用会引发手机过耗电增加

```

> 资料参考：
>
> 资料参考：
> [如何做到一秒渲染一个移动页面](https://github.com/cssmagic/blog/issues/20)
>
> 资料参考：
> [如何做到一秒渲染一个移动页面](https://github.com/cssmagic/blog/issues/20)
> HTML5与CSS3语法提供浏览器兼容性测试及使用建议：[http://html5please.com/](http://html5please.com/)

## meta相关总结

`HTML5`页面窗口自动调整到设备宽度，并禁止用户缩放页面

```
<meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no" />

```

忽略将页面中的数字识别为电话号码

```
<meta name="format-detection" content="telephone=no" />

```

忽略`Android`平台中对邮箱地址的识别

```
<meta name="format-detection" content="email=no" />

```

当网站添加到主屏幕快速启动方式，可隐藏地址栏，仅针对`ios`的`safari`

```
    <meta name="apple-mobile-web-app-capable" content="yes" />
    <!-- ios7.0版本以后，safari上已看不到效果 -->

```

将网站添加到主屏幕快速启动方式，仅针对`ios`的`safari`顶端状态条的样式

```
<meta name="apple-mobile-web-app-status-bar-style" content="black" />
<!-- 可选default、black、black-translucent -->

```

## 移动端touch事件(区分webkit 和 winphone)

当用户手指放在移动设备在屏幕上滑动会触发的touch事件

以下支持`webkit`：

```
touchstart——当手指触碰屏幕时候发生。不管当前有多少只手指
touchmove——当手指在屏幕上滑动时连续触发。通常我们再滑屏页面，会调用event的preventDefault()可以阻止默认情况的发生：阻止页面滚动
touchend——当手指离开屏幕时触发
touchcancel——系统停止跟踪触摸时候会触发。例如在触摸过程中突然页面alert()一个提示框，此时会触发该事件，这个事件比较少用

```

以下支持`windows phone 8`：

```
MSPointerDown——当手指触碰屏幕时候发生。不管当前有多少只手指
MSPointerMove——当手指在屏幕上滑动时连续触发。通常我们再滑屏页面，会调用css的html{-ms-touch-action: none;}可以阻止默认情况的发生：阻止页面滚动
MSPointerUp——当手指离开屏幕时触发

```

## 手机拍照和上传图片

``的`accept` 属性

```
<!-- 选择照片 -->
<input type=file accept="image/*">
<!-- 选择视频 -->
<input type=file accept="video/*">

```

使用总结：

```
ios 有拍照、录像、选取本地图片功能
部分android只有选取本地图片功能
winphone不支持
input控件默认外观丑陋

```

## 移动端手势总结

### 主要用户操作

![img](https://segmentfault.com/img/bVlxPD)

### 手势的具体操作

![img](https://segmentfault.com/img/bVlxPx)

### 触摸屏手势

![img](https://segmentfault.com/img/bVlxPA)

### 手势的具体操作

![img](https://segmentfault.com/img/bVlxPC)

to be continued。。。