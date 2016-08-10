HTTP协议是一种无状态协议，服务器端本身不具有识别客户端的能力，必须借助外部机制，比如session和cookie，才能与特定客户端保持对话。这多多少少带来一些不便，尤其在服务器端与客户端需要持续交换数据的场合（比如网络聊天），更是如此。为了解决这个问题，HTML5提出了浏览器的[WebSocket API](http://dev.w3.org/html5/websockets/)。

> WebSocket的主要作用是，允许服务器端与客户端进行全双工（full-duplex）的通信。举例来说，HTTP协议有点像发电子邮件，发出后必须等待对方回信；WebSocket则是像打电话，服务器端和客户端可以同时向对方发送数据，它们之间存着一条持续打开的数据通道。

WebSocket协议完全可以取代Ajax方法，用来向服务器端发送文本和二进制数据，而且还没有“同域限制”。

WebSocket不使用HTTP协议，而是使用自己的协议。浏览器发出的WebSocket请求类似于下面的样子：

```
GET / HTTP/1.1
Connection: Upgrade
Upgrade: websocket
Host: example.com
Origin: null
Sec-WebSocket-Key: sN9cRrP/n9NdMgdcy2VJFQ==
Sec-WebSocket-Version: 13
```

上面的头信息显示，有一个HTTP头是Upgrade。HTTP1.1协议规定，Upgrade头信息表示将通信协议从HTTP/1.1转向该项所指定的协议。“Connection: Upgrade”就表示浏览器通知服务器，如果可以，就升级到webSocket协议。Origin用于验证浏览器域名是否在服务器许可的范围内。Sec-WebSocket-Key则是用于握手协议的密钥，是base64编码的16字节随机字符串。

服务器端的WebSocket回应则是

```
HTTP/1.1 101 Switching Protocols
Connection: Upgrade
Upgrade: websocket
Sec-WebSocket-Accept: fFBooB7FAkLlXgRSz0BT3v4hq5s=
Sec-WebSocket-Origin: null
Sec-WebSocket-Location: ws://example.com/
```

服务器端同样用“Connection: Upgrade”通知浏览器，需要改变协议。Sec-WebSocket-Accept是服务器在浏览器提供的Sec-WebSocket-Key字符串后面，添加“258EAFA5-E914-47DA-95CA-C5AB0DC85B11” 字符串，然后再取sha-1的hash值。浏览器将对这个值进行验证，以证明确实是目标服务器回应了webSocket请求。Sec-WebSocket-Location表示进行通信的WebSocket网址。

> 请注意，WebSocket协议用ws表示。此外，还有wss协议，表示加密的WebSocket协议，对应HTTPs协议。

完成握手以后，WebSocket协议就在TCP协议之上，开始传送数据。

WebSocket协议需要服务器支持，目前比较流行的实现是基于node.js的[socket.io](http://socket.io/)，更多的实现可参阅[Wikipedia](http://en.wikipedia.org/wiki/WebSocket#Server_side)。至于浏览器端，目前主流浏览器都支持WebSocket协议（包括IE 10+），仅有的例外是手机端的Opera Mini和Android Browser。

## 客户端

浏览器端对WebSocket协议的处理，无非就是三件事：

- 建立连接和断开连接
- 发送数据和接收数据
- 处理错误

### 建立连接和断开连接

首先，客户端要检查浏览器是否支持WebSocket，使用的方法是查看window对象是否具有WebSocket属性。

```
if(window.WebSocket != undefined) {
    // WebSocket代码
}
```

然后，开始与服务器建立连接（这里假定服务器就是本机的1740端口，需要使用ws协议）。

```
if(window.WebSocket != undefined) {
    var connection = new WebSocket('ws://localhost:1740');
}
```

建立连接以后的WebSocket实例对象（即上面代码中的connection），有一个readyState属性，表示目前的状态，可以取4个值：

- 0： 正在连接
- 1： 连接成功
- 2： 正在关闭
- 3： 连接关闭

握手协议成功以后，readyState就从0变为1，并触发open事件，这时就可以向服务器发送信息了。我们可以指定open事件的回调函数。

```
connection.onopen = wsOpen;

function wsOpen (event) {
    console.log('Connected to: ' + event.currentTarget.URL);
}
```

关闭WebSocket连接，会触发close事件。

```
connection.onclose = wsClose;

function wsClose () {
    console.log("Closed");
}

connection.close();
```

### 发送数据和接收数据

连接建立后，客户端通过send方法向服务器端发送数据。

```
connection.send(message);
```

除了发送字符串，也可以使用 Blob 或 ArrayBuffer 对象发送二进制数据。

```
// 使用ArrayBuffer发送canvas图像数据
var img = canvas_context.getImageData(0, 0, 400, 320);
var binary = new Uint8Array(img.data.length);
for (var i = 0; i < img.data.length; i++) {
    binary[i] = img.data[i];
}
connection.send(binary.buffer);

// 使用Blob发送文件
var file = document.querySelector('input[type="file"]').files[0];
connection.send(file);
```

客户端收到服务器发送的数据，会触发message事件。可以通过定义message事件的回调函数，来处理服务端返回的数据。

```
connection.onmessage = wsMessage;

function wsMessage (event) {
    console.log(event.data);
}
```

上面代码的回调函数wsMessage的参数为事件对象event，该对象的data属性包含了服务器返回的数据。

如果接收的是二进制数据，需要将连接对象的格式设为blob或arraybuffer。

```
connection.binaryType = 'arraybuffer';

connection.onmessage = function(e) {
  console.log(e.data.byteLength); // ArrayBuffer对象有byteLength属性
};
```

### 处理错误

如果出现错误，浏览器会触发WebSocket实例对象的error事件。

```
connection.onerror = wsError;

function wsError(event) {
    console.log("Error: " + event.data);
}
```

## 服务器端

服务器端需要单独部署处理WebSocket的代码。下面用node.js搭建一个服务器环境。

```
var http = require('http');
var server = http.createServer(function(request, response) {});
```

假设监听1740端口。

```
server.listen(1740, function() {
    console.log((new Date()) + ' Server is listening on port 1740');
});
```

接着启动WebSocket服务器。这需要加载websocket库，如果没有安装，可以先使用npm命令安装。

```
var WebSocketServer = require('websocket').server;
var wsServer = new WebSocketServer({
    httpServer: server
});
```

WebSocket服务器建立request事件的回调函数。

```
var connection;

wsServer.on('request', function(req){
    connection = req.accept('echo-protocol', req.origin);
});
```

上面代码的回调函数接受一个参数req，表示request请求对象。然后，在回调函数内部，建立WebSocket连接connection。接着，就要对connection的message事件指定回调函数。

```
wsServer.on('request', function(r){
    connection = req.accept('echo-protocol', req.origin);

    connection.on('message', function(message) {
        var msgString = message.utf8Data;
        connection.sendUTF(msgString);
    });
});
```

最后，监听用户的disconnect事件。

```
connection.on('close', function(reasonCode, description) {
    console.log(connection.remoteAddress + ' disconnected.');
});
```

使用[ws](https://github.com/einaros/ws)模块，部署一个简单的WebSocket服务器非常容易。

```
var WebSocketServer = require('ws').Server;
var wss = new WebSocketServer({ port: 8080 });

wss.on('connection', function connection(ws) {
  ws.on('message', function incoming(message) {
    console.log('received: %s', message);
  });

  ws.send('something');
});
```

## Socket.io简介

[Socket.io](http://socket.io/)是目前最流行的WebSocket实现，包括服务器和客户端两个部分。它不仅简化了接口，使得操作更容易，而且对于那些不支持WebSocket的浏览器，会自动降为Ajax连接，最大限度地保证了兼容性。它的目标是统一通信机制，使得所有浏览器和移动设备都可以进行实时通信。

第一步，在服务器端的项目根目录下，安装socket.io模块。

```
npm install socket.io
```

第二步，在根目录下建立app.js，并写入以下代码（假定使用了Express框架）。

```
var app = require('express')();
var server = require('http').createServer(app);
var io = require('socket.io').listen(server);

server.listen(80);

app.get('/', function (req, res) {
  res.sendfile(__dirname + '/index.html');
});
```

上面代码表示，先建立并运行HTTP服务器。Socket.io的运行建立在HTTP服务器之上。

第三步，将Socket.io插入客户端网页。

```
<script src="/socket.io/socket.io.js"></script>
```

然后，在客户端脚本中，建立WebSocket连接。

```
var socket = io.connect('http://localhost');
```

由于本例假定WebSocket主机与客户端是同一台机器，所以connect方法的参数是`http://localhost`。接着，指定news事件（即服务器端发送news）的回调函数。

```
socket.on('news', function (data){
   console.log(data);
});
```

最后，用emit方法向服务器端发送信号，触发服务器端的anotherNews事件。

```
socket.emit('anotherNews');
```

> 请注意，emit方法可以取代Ajax请求，而on方法指定的回调函数，也等同于Ajax的回调函数。

第四步，在服务器端的app.js，加入以下代码。

```
io.sockets.on('connection', function (socket) {
  socket.emit('news', { hello: 'world' });
  socket.on('anotherNews', function (data) {
    console.log(data);
  });
});
```

上面代码的io.sockets.on方法指定connection事件（WebSocket连接建立）的回调函数。在回调函数中，用emit方法向客户端发送数据，触发客户端的news事件。然后，再用on方法指定服务器端anotherNews事件的回调函数。

不管是服务器还是客户端，socket.io提供两个核心方法：emit方法用于发送消息，on方法用于监听对方发送的消息。

### WebSockets支持及时数据交换，并被所有现代浏览器支持。

没有其他技术能够像WebSocket一样提供真正的双向通信，许多web开发者仍然是依赖于ajax的长轮询来实现。（注：我认为长轮询是富于创造性和多功能性的，虽然这只是一个不太完美的解决办法（hack））对Websocket缺少热情，也许是因为多年前他的安全性的脆弱，抑或者是缺少浏览器的支持，不管怎样，这两个问题都已经被解决了。

**决定手头的工作是否需要使用WebSocket技术的方法很简单：**

- 你的应用提供多个用户相互交流吗？
- 你的应用是展示服务器端经常变动的数据吗？

如果你的回答是肯定的，那么请考虑使用WebSocket。如果你仍然不确定，并想要更多的灵感，这有一些杀手锏的案例。

**1.社交订阅**

对社交类的应用的一个裨益之处就是能够即时的知道你的朋友正在做什么。虽然听起来有点可怕，但是我们都喜欢这样做。你不会想要在数分钟之后才能知道一个家庭成员在馅饼制作大赛获胜或者一个朋友订婚的消息。你是在线的，所以你的订阅的更新应该是实时的。

**2.多玩家游戏**

网络正在迅速转变为游戏平台。在不使用插件（我指的是Flash）的情况下，网络开发者现在可以在浏览器中实现和体验高性能的游戏。无论你是在处理DOM元素、CSS动画，HTML5的canvas或者尝试使用WebGL，玩家之间的互动效率是至关重要的。我不想在我扣动扳机之后，我的对手却已经移动位置。

**3.协同编辑/编程**

我们生活在分布式开发团队的时代。平时使用一个文档的副本就满足工作需求了，但是你最终需要有一个方式来合并所有的编辑副本。版本控制系统，比如Git能够帮助处理某些文件，但是当Git发现一个它不能解决的冲突时,你仍然需要去跟踪人们的修改历史。通过一个协同解决方案，比如WebSocket，我们能够工作在同一个文档，从而省去所有的合并版本。这样会很容易看出谁在编辑什么或者你在和谁同时在修改文档的同一部分。

**4.点击流数据**

分析用户与你网站的互动是提升你的网站的关键。HTTP的开销让我们只能优先考虑和收集最重要的数据部分。然后，经过六个月的线下分析，我们意识到我们应该收集一个不同的判断标准——一个看起来不是那么重要但是现在却影响了一个关键的决定。与HTTP请求的开销方式相比，使用Websocket，你可以由客户端发送不受限制的数据。想要在除页面加载之外跟踪鼠标的移动？只需要通过WebSocket连接发送这些数据到服务器，并存储在你喜欢的NoSQL数据库中就可以了（MongoDB是适合记录这样的事件的）。现在你可以通过回放用户在页面的动作来清楚的知道发生了什么。

**5.股票基金报价**

金融界瞬息万变——几乎是每毫秒都在变化。我们人类的大脑不能持续以那样的速度处理那么多的数据，所以我们写了一些算法来帮我们处理这些事情。虽然你不一定是在处理高频的交易，但是，过时的信息也只能导致损失。当你有一个显示盘来跟踪你感兴趣的公司时，你肯定想要随时知道他们的价值，而不是10秒前的数据。使用WebSocket可以流式更新这些数据变化而不需要等待。

**6.体育实况更新**

现在我们开始讨论一个让人们激情澎湃的愚蠢的东西——体育。我不是运动爱好者，但是我知道运动迷们想要什么。当爱国者在打比赛的时候，我的妹夫将会沉浸于这场比赛中而不能自拔。那是一种疯狂痴迷的状态，完全发自内心的。我虽然不理解这个，但是我敬佩他们与运动之间的这种强烈的联系，所以，最后我能做的就是给他的体验中降低延迟。如果你在你的网站应用中包含了体育新闻，WebSocket能够助力你的用户获得实时的更新。

**7.多媒体聊天**视频会议并不能代替和真人相见，但当你不能在同一个屋子里见到你谈话的对象时，视频会议是个不错的选择。尽管视频会议私有化做的“不错”，但其使用还是很繁琐。我可是开放式网络的粉丝，所以用WebSockets getUserMedia API和HTML5音视频元素明显是个不错的选择。WebRTC的出现顺理成章的成为我刚才概括的组合体，它看起来很有希望，但其缺乏目前浏览器的支持，所以就取消了它成为候选人的资格。

**8.基于位置的应用**越来越多的开发者借用移动设备的GPS功能来实现他们[基于位置的网络应用](http://www.infoworld.com/d/application-development/use-mongodb-make-your-app-location-aware-229403)。如果你一直记录用户的位置(比如运行应用来记录运动轨迹)，你可以收集到更加细致化的数据。如果你想实时的更新网络数据仪表盘(可以说是一个监视运动员的教练)，HTTP协议显得有些笨拙。借用WebSocket TCP链接可以让数据飞起来。

**9.在线教育**上学花费越来越贵了，但互联网变得更快和更便宜。[在线教育](http://www.infoworld.com/d/application-development/200k-computer-science-degree-or-these-free-online-classes-218565)是学习的不错方式，尤其是你可以和老师以及其他同学一起交流。很自然，WebSockets是个不错的选择，可以多媒体聊天、文字聊天以及其它优势如与别人合作一起在公共数字黑板上画画...

[WebSocket 实战](http://www.ibm.com/developerworks/cn/java/j-lo-WebSocket/)简要介绍了 WebSocket 的由来，原理机制以及服务端/客户端实现，并以实际客户案例指导并讲解了如何使用 WebSocket 解决实时响应及服务端消息推送方面的问题。本文适用于熟悉 HTML 协议规范和 J2EE Web 编程的读者，旨在帮助读者快速熟悉 HTML5 WebSocket 的原理和开发应用。文中的服务端及客户端项目代码可供下载，修改后可用于用户基于 WebSocket 的 HTTP 长连接的实际生产环境中。