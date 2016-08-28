## 介绍

koa是一个相对于express来说，更小，更健壮，更富表现力的Web框架。koa通过组合不同的generator来避免繁琐的回调函数调用。koa的核心库没有绑定任何的中间件，仅仅提供了一个轻量优雅的函数库，使得编写Web应用变得得心应手。

## 使用

在项目目录路径下运行命令 `npm install --save-dev koa` 就可以在本地安装koa模块。

安装完成后，写一个hello world来验证是否生效。

## hello world

hello world代码十分简单。

```
const koa = require('koa');
const app = koa();

app.use(function *() {
  this.body = 'hello world';
});

app.listen(3000);
```

上面8行代码就实现了koa的hello world。第4行调用的 `app.use()` 传入了一个generator方法，就是koa中间件的基本实现。koa应用的实现就是由一个一个的中间件来实现。每一个中间件都是一个generator方法，通过yield语句，将一个一个中间件逻辑级联起来。举个例子：

```
const koa = require('koa');
const app = koa();

// x-response-time
app.use(function *(next) {
  console.log('line 1');
  const start = new Date;
  yield next;
  console.log('line 5');
  const ms = new Date - start;
  this.set('X-Response-Time', `${ms}ms`);
});

// log time
app.use(function *(next) {
  console.log('line 2');
  const start = new Date;
  yield next;
  console.log('line4');
  const ms = new Date - start;
  console.info('%s %s : %s ms', this.method, this.url, ms);
});

// response
app.use(function *(next) {
  console.log('line3');
  this.body = 'hello world';
});

app.listen(3000);
```

运行上面的代码后，当有一个请求被触发时，控制台的log就会打印如下日志(请忽略上面代码中的info日志)：

```
line 1
line 2
line 3
line 4
line 5
```

可以看出，koa中间件的运行，被yield分割成了两段来运行，运行的时间顺序如下图：

koa框架本身的功能十分简单，koa应用的功能都是通过中间件来实现的，下面我们来介绍常用的几个koa中间件。

## koa-static

koa-static是管理静态文件请求的中间件。比如要请求html，JS，图片的静态文件时，就可以使用koa-static来实现。

举个例子，比如项目根目录下得static目录用于存放静态文件，那么如下代码就可以实现该目录的静态文件请求

```
const path = require('path');
const staticServer = require('koa-static');

app.use(staticServer(path.join(__dirname, 'static')));
```

## koa-router

koa-router是一个路由中间件，用法如下：

```
const router = require('koa-router')();

// 监听url请求
router.get('/list', function *() {
  // ...
});
router.post('/user/register', function *() {
  // ...
});
```

## koa-safe-jsonp

JSONP格式返回的中间件，用法：

```
const jsonp = require('koa-safe-jsonp');
const router = require('koa-router');
const koa = require('koa');

const app = koa();
jsonp(app);

router.get('./list', function *() {
  const list = [];
  this.json = list;
});
```

## koa-session

session管理的中间件，用法：

```
const session = require('koa-session');

app.use(session(app));

router.post('./user/login', function *() {
  this.session.user = user;
});
```

## koa-onerror

koa-onerror用于格式化异常情况的页面输出。用法：

```
const onerror = require('koa-onerror');
const koa = require('koa');

const app = koa();
onerror(app);
```

## 中间件实现

上面列出的常用中间件都是十分常用的中间件，但是在业务开发过程中，我们会根据业务场景实现一些专门的中间件，那么如何开发一个中间件呢？

现在我们来讲下如何实现koa的中间件。比如我们要实现一个统计请求响应实现的功能，就可以开发一个统计请求响应时间的中间件来使用。

基本从上面的例子中，可以看出，中间件都是通过 `app.use()` 来注册的，而 `app.use()` 方法的参数是一个generator方法。所以, koa中间件需要返回一个generator方法，所以实现代码如下：

```
// middleware/timer.js
module.exports = function() {
  return function *(next) {
    const path = this.path;
    const start = new Date;
    yield next;
    const end = new Date;
    console.log(`${path} response time: ${ end - start }ms`);
  }
};

// server.js
const timer = require('./middleware/timer.js');
app.use(timer());
```

上面这段代码，就实现了统计请求响应时间的功能。功能十分简单， `middleware/timer.js` 中直接返回了一个generator方法，在里面分别统计请求开始时间和结束时间，并打印时间差。

我们可以将中间件设计的稍微复杂一点，比如我们可以控制过滤掉一部分请求不打印，打印格式也可以进行控制。这个时候我们需要传入一些参数来控制中间件的功能，代码如下：

```
// middleware/timer.js
module.exports = function timer(options) {
  return function *timer() {
    let start = new Date;
    const path = this.path;
    const method = this.request.method;
    if ( method !== options.filter.method ) start = 0;
    yield next;
    const end = new Date;
    if(start !== 0 && end - start > options.filter.min) {
      console.log(options.format.replace(/:url/g, path).replace(/:time/g, `${ end - start }ms`));
    }
  }
};

// server.js
const timer = require('./middleware/timer.js');
// GET 请求超过100ms时，打印日志，日志格式： url time
app.use(timer({
  format: ':url :time'
  filter: {
    min: 100
    method: 'GET'
  }
}));
```

## koa源码理解

最前面介绍的时候有说到，koa的核心库十分简单，没有绑定任何中间件。所以，在最后说一下koa的源码。

koa.js的源码可以去 [https://github.com/koajs/koa](https://github.com/koajs/koa) 获得。

koa.js的源码有4个文件，分别是 `lib/application.js` , `lib/context.js` ,`lib/request.js` , `lib/response.js` 。从名字中可以看出来， `context.js` ,`request.js` , `response.js` 分别是上下文环境对象，request对象，response对象，而 `application.js` 就是koa.js的核心代码。

在上面的例子中，都使用了两个接口， `use` 和 `listen` 。下面我们先介绍下这两个方法, 下面是 `application.js` 中这两个方法的源码：

```
// ...
app.listen = function(){
  debug('listen');
  var server = http.createServer(this.callback());
  return server.listen.apply(server, arguments);
};
// ...
app.use = function(fn){
  if (!this.experimental) {
    // es7 async functions are not allowed,
    // so we have to make sure that `fn` is a generator function
    assert(fn && 'GeneratorFunction' == fn.constructor.name, 'app.use() requires a generator function');
  }
  debug('use %s', fn._name || fn.name || '-');
  this.middleware.push(fn);
  return this;
};
// ...
```

可以看出来， `use` 的作用就是将传入的中间件generator方法放到 `this.middleware` 中。 `listen` 接口的作用其实就是启动了一个server，并将请求处理设置为 `this.callback()` 的返回方法。

然后我们来看下 `this.callback` 怎么写的：

```
// ...
app.callback = function(){
  if (this.experimental) {
    console.error('Experimental ES7 Async Function support is deprecated. Please look into Koa v2 as the middleware signature has changed.')
  }
  var fn = this.experimental
    ? compose_es7(this.middleware)
    : co.wrap(compose(this.middleware));
  var self = this;

  if (!this.listeners('error').length) this.on('error', this.onerror);

  return function(req, res){
    res.statusCode = 404;
    var ctx = self.createContext(req, res);
    onFinished(res, ctx.onerror);
    fn.call(ctx).then(function () {
      respond.call(ctx);
    }).catch(ctx.onerror);
  }
};
// ...
```

其中的核心代码是这两句：

```
// ...
co.wrap(compose(this.middleware));
// ...
return function(req, res){
  // ...
  fn.call(ctx).then(function () {
    respond.call(ctx);
  }).catch(ctx.onerror);
}
// ...
```

其中 `compose(this.middleware)` 的作用是将传入的中间件数组合并成层层调用的generator函数。 `co.wrap()` 方法的作用是将generator数转化成一个自执行的函数。最后的 `fn.call(ctx)` 就开始逐步执行中间件了。