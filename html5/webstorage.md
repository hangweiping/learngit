WebStorage是HTML5中本地存储的解决方案之一，在HTML5的WebStorage概念引入之前除去IE User Data、Flash Cookie、Google Gears等看名字就不靠谱的解决方案，浏览器兼容的本地存储方案只有使用cookie。有同学可能会问，既然有了cookie本地存储，为什么还要引入WebStorage的概念？

### Cookie肿么了

cookie的缺陷是非常明显的

\1. 数据大小：作为存储容器，cookie的大小限制在4KB左右这是非常坑爹的，尤其对于现在复杂的业务逻辑需求，4KB的容量除了存储一些配置字段还简单单值信息，对于绝大部分开发者来说真的不知指望什么了。

\2. 安全性问题：由于在HTTP请求中的cookie是明文传递的（HTTPS不是），带来的安全性问题还是很大的。

\3. 网络负担：我们知道cookie会被附加在每个HTTP请求中，在HttpRequest 和HttpResponse的header中都是要被传输的，所以无形中增加了一些不必要的流量损失。

### WebStorage

WebStorage是HTML新增的本地存储解决方案之一，但并不是为了取代cookie而制定的标准，cookie作为HTTP协议的一部分用来处理客户端和服务器通信是不可或缺的，session正是依赖于实现的客户端状态保持。WebStorage的意图在于解决本来不应该cookie做，却不得不用cookie的本地存储。

WebStorage提供两种类型的API：localStorage和sessionStorage，两者的区别看名字就有大概了解，localStorage在本地永久性存储数据，除非显式将其删除或清空，sessionStorage存储的数据只在会话期间有效，关闭浏览器则自动删除。两个对象都有共同的API

```
interface Storage {
  readonly attribute unsigned long length;
  DOMString? key(unsigned long index);
  getter DOMString getItem(DOMString key);
  setter creator void setItem(DOMString key, DOMString value);
  deleter void removeItem(DOMString key);
  void clear();
};
```

- length:唯一的属性，只读，用来获取storage内的键值对数量。
- key：根据index获取storage的键名
- getItem：根据key获取storage内的对应value
- setItem：为storage内添加键值对
- removeItem：根据键名，删除键值对
- clear：清空storage对象

### 使用

在实现了WebStorage的浏览器中，页面有两个全局的对象localStorage和sessionStorage

![image](http://img0.tuicool.com/vaeArq.png!web)

以localStorage为例，看一段简单的操作代码

```javascript
var ls=localStorage;
            console.log(ls.length);//0
            ls.setItem('name','Byron');
            ls.setItem('age','24');
            console.log(ls.length);//2
            
            //遍历localStorage
            for(var i=0;i<ls.length;i++){
                /*
                    age : 24 
                    name : Byron 
                */
                var key=ls.key(i);
                console.log(key+' : '+ls.getItem(key));
            }
            
            ls.removeItem('age');
            
            
            for(var i=0;i<ls.length;i++){
                /*
                    name : Byron 
                */
                var key=ls.key(i);
                console.log(key+' : '+ls.getItem(key));
            }
            ls.clear();//0
            console.log(ls.length);
```

### 事件

同时HTML5规定了一个storage事件，在WebStorage发生变化的时候触发，可以用此监视不同页面对storage的修改

```javascript
interface StorageEvent : Event {
  readonly attribute DOMString key;
  readonly attribute DOMString? oldValue;
  readonly attribute DOMString? newValue;
  readonly attribute DOMString url;
  readonly attribute Storage? storageArea;
};
```

- key:键值对的键
- oldValue：修改之前的value
- newValue：修改之后的value
- url：触发改动的页面url
- StorageArea：发生改变的Storage

*在index.php中定义*

```html
<a href="test.php" target="_blank">Test</a>
```

```javascript
window.addEventListener('storage',function(e){
      console.log(e.key+' is changed form '+e.oldValue+' to '+e.newValue+' by      '+e.url );
      console.log(e.storageArea ==localStorage);
},false);
            
localStorage.setItem('userName','Byron');
```

*test.php*

```javascript
localStorage.setItem('userName','Casper');
```

在index.php页面点击链接访问test.php时可以看到index.php的控制台输出log

### 为什么比cookie好

1. 从容量上讲WebStorage一般浏览器提供5M的存储空间，用来存储视频、图片神马的不够，但对于绝大部分操作足矣.
2. 安全性上WebStorage并不作为HTTP header发送的浏览器，所以相对安全
3. 从流量上讲，因为WebStorage不传送到服务器，所以不必要的流量可以节省，这样对于高频次访问或者针对手机移动设备的网页还是很不错的。
4. 这并不意味着WebStorage可以取代cookie，而是有了WebStorage后cookie能只做它应该做的事情了——作为客户端与服务器交互的通道，保持客户端状态。所以仅仅作为本地存储解决方案WebStorage是优于cookie的。

### 注意点

1.浏览器兼容性，这个几乎是所有HTML5新特性中最容易实施的了，因为IE8+的浏览器都支持，在IE7、IE6中可以使用IE User Data实现。

![2011052411384081](http://img2.tuicool.com/myaA73.jpg!web) 

2.由于localStorage和sessionStorage都是对象，所以我饿每年也可以通过”.key”或”[key]”的方式获取、修改键值对，但不推荐这么做

```javascript
localStorage.userName='Frank';
console.log(localStorage['userName']);
```

3.虽然localStorage存储在本地，但不同的浏览器存储存储数据是独立的，所以在Chrome上存储的localStorage在FireFox上是获取不到的。

4.localStorage和sessionStorage只能存储字符串类型，对于复杂的对象可以使用ECMAScript提供的JSON对象的stringify和parse来处理，低版本IE可以使用 [json2.js](https://github.com/douglascrockford/JSON-js/blob/master/json2.js)

5.现在很多浏览器都提供了“匿名访问”，“安全模式”，“隐身窗口”等等功能。在这种情况下，浏览器都是会重新建立一个新的localStorage，所以这些模式下的页面是没法访问在正常模式下的页面中的数据。

6.除了控制台，Chrome还为本地存储提供了非常直观的显示方式，调试的时候很方便

![image](http://img0.tuicool.com/RVzQFb.png!web)



### 引入实例

今天给大家分享一个留言实时保存的例子。

js代码如下：

```javascript
 1 function upInfo() {
 2     var lStorage = window.localStorage;
 3     var show = window.document.getElementById("show");
 4     if (window.localStorage.myBoard) {
 5         show.value = window.localStorage.myBoard;
 6     } else {
 7         var info = "还没有留言";
 8         show.value = "还没有留言";
 9     }
10 }
11 function addInfo() {
12     var info = window.document.getElementById("t1");
13     var lStorage = window.localStorage;
14     if (lStorage.myBoard) {
15         var date = new Date();
16         lStorage.myBoard += t1.value + "\n发表时间：" + date.toLocaleString() + "\n";
17     } else {
18         var date = new Date();
19         lStorage.myBoard = t1.value + "\n发表时间：" + date.toLocaleString() + "\n";
20     }
21     upInfo();
22 }
23 function cleanInfo() {
24     window.localStorage.removeItem("myBoard");
25     upInfo();
26 }
27 upInfo();
```

html如下：

```html
 1 <body> 
 2   <div> 
 3    <h2>简单的web存储留言板</h2> 
 4    <textarea id="t1"></textarea> 
 5    <br /> 
 6    <input type="button" onclick="addInfo()" value="留言" /> 
 7    <input type="button" onclick="cleanInfo()" value="清除留言" /> 
 8    <br /> 
 9    <hr /> 
10    <label id="shows"></label> 
11    <textarea id="show" readonly="readonly"></textarea> 
12   </div> 
13  </body>
```

运行效果，如下：

![img](http://img0.tuicool.com/QbQNVv2.png!web)