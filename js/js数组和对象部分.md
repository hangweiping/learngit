## 对象部分

### Object类型

`Object` 是一个无序的集合，可以存放任意类型对象，所有其他对象都继承自这个对象。

`Object` 是一个无序的集合，可以存放任意类型对象，所有其他对象都继承自这个对象。
创建`Object`类型有两种，一种是使用`new`运算符，一种是字面量表示法。

**1.使用new运算符创建Object**

```
var obj = new Object();//注意大写，也可以直接写成Object()
```

注意，通过`new Object()` 的写法生成新对象，与字面量的写法 `obj = {}` 是等价的。

**2. 使用字面量方式创建：**

```
var obj = {
    name : 'trigkit4',
    age : 21
};//分号最好加上
```

在使用字面量声明`Object`对象时，不会调用`Object()`构造函数（FF除外）

### Object.prototype对象

所有构造函数都有一个`prototype`属性，指向一个原型对象。

```
Object.prototype.print = function(){ console.log(this)};

var obj = new Object();

obj.print() // Object

```

实例`obj`直接继承了`Object.prototype`的属性和方法

```
1.对象只是一种特殊的数据。对象拥有属性和方法。 JavaScript 是面向对象的语言，但 JavaScript 不使用类。 JavaScript 基于 [prototype][1]，而不是基于类的。

2.属性：是隶属于某个特定对象的变量。方法：是只有某个特定对象才能调用的函数。

3.js对象是属性和方法的集合。一个方法就是一个函数，是对象的成员。属性是一个值或一组值（以数组或对象的形式），是对象的成员。

4.js对象是基于构造器函数的，使用构造器函数创建一个新对象时，就可以说是实例化了一个新对象。属性是构造器函数内部的变量。

```

使用构造器函数实例化的对象：

```
cat = new Animal();

```

`Javascript`是一种基于对象（object-based）的语言，你遇到的所有东西几乎都是对象。但是，它又不是一种真正的面向对象编程（OOP）语言，因为它的语法中没有class（类）。

```
<script type="text/javascript">
    //对象是名/值对的集合
        var browser = {        //对象是由花括号括起来的
            name:"Firefox",
            kernel:"Gecko"
        };
</script>

```

------

```
 //通过点号（.）或“[]”来访问对象的属性
    browser.name         //"Firefox"
    browser["kernel"]    //"Gecko"
```

对象（objct）是属性（property）的集合，每个属性都由“名/值对”构成，js同样定义了一个特殊的对象——数组，它是带有编号的值的有序集合。

`js`还定义了一个特殊的对象——[函数](http://segmentfault.com/blog/trigkit4/1190000000660786)，函数是具有与他相关联的可执行代码的对象，通过调用函数来执行代码，并返回运算结果。

JS中没有[类\][3](undefined)，但是它取了一个新的名字叫“`原型对象`”，因此"类==原型对象"，详情见：[JavaScript类的写法（一）](http://segmentfault.com/blog/trigkit4/1190000000725051)

### 二：类(原型对象)和对象(实例)的区别与联系

```
1.类(原型对象)是抽象，是概念的，代表一类事物。
2.对象是具体的，实际的，代表一个具体的事物。
3.类(原型对象)是对象实例的模板，对象实例是类的一个个体。

```

一个常见的误解是数字的字面值（literal）不是对象。这是因为 JavaScript 解析器的一个错误，它试图将点操作符解析为浮点数字面值的一部分。

有很多变通方法可以让数字的字面值看起来像对象。

```
2..toString(); // 第二个点号可以正常解析
2 .toString(); // 注意点号前面的空格
(2).toString(); // 2先被计算

```

### 删除属性

删除属性的唯一方法是使用 delete 操作符；设置属性为 `undefined` 或者 `null` 并不能真正的删除属性，而仅仅是移除了属性和值的关联。

### JavaScript面向对象三大特征

```
封装：不考虑内部实现，只考虑功能使用
继承：从已有对象上，继承出新的对象
多态：所谓多态，就是指一个引用在不同情况下的多种状态，

```

### 1.封装

封装就是要把属于同一类事物的共性(包括属性与行为)归到一个类中,以方便使用.比如人这个东东,可用下面的方式封装:

人{

```
年龄(属性一)
身高(属性二)
性别(属性三)

做事(行为之一)
走路(行为之二)
说话(行为之三)
```

}

**封装的好处：**

```
封装保护了内部数据的完整性；
封装使对象的重构更轻松；
弱化模块间的耦合，提高对象的可重用性；
有助于避免命名空间冲突；

```

**看下面一个例子：**

```
 <script type="text/javascript">  
            var boy = {}; //创建一个空对象
                    boy.name = "小明";//按照原型对象的属性赋值
                    boy.age = 12;
                    
            var girl = {};
                    girl.name = "小红";
                    girl.age = 10;
    </script>

```

这就是最简单的封装了，把两个属性封装在一个对象里面。但是，这样的写法有两个缺点，一是如果多生成几个实例，写起来就非常麻烦；二是实例与原型之间，没有任何办法，可以看出有什么联系。

### 构造函数模式

为了解决从原型对象生成实例的问题，Javascript提供了一个构造函数（`Constructor`）模式。

所谓"`构造函数`"，其实就是一个普通函数，但是内部使用了`this`变量。对构造函数使用new运算符，就能生成实例，并且`this`变量会绑定在实例对象上。

比如`boy`和`girl`的[原型对象](http://segmentfault.com/blog/trigkit4/1190000000662547)现在就可以这样写：

```
<script type="text/javascript">  
        function Person(name,age){
                this.name = name;
                this.age = age;
        }
</script>
```

我们现在就可以生成实例对象了。

```
<script type="text/javascript">  
        var boy = new Person("小明",12)；
        var girl = new Person("小红",10);
        
        alert(boy.name);  //小明
        alert(boy.age);   //12
</script>
```

这时`boy`和`girl`会自动含有一个`constructor`属性，指向它们的构造函数。

```
alert(boy.constructor == Person); //true

alert(girl.constructor); //输出整串构造函数代码，自己试试吧
```

`Prototype`模式 `Javascript`规定，每一个构造函数都有一个`prototype`属性，指向另一个对象。这个对象的所有属性和方法，都会被构造函数的实例继承。

这意味着，我们可以把那些不变的属性和方法，直接定义在`prototype`对象上。

```
<script type="text/javascript">
function Person(name,age){
        this.name = name;
        this.age = age;
}

Person.protype.type = "人类";

Person.protype.eat = function(){
        alert("吃米饭");
}
</script>

```

然后，生成实例:

```
<script type="text/javascript">
var boy = new Person("小明","12");
var girl = new Person("小红","10");

alert(boy.type);//人类
boy.eat();//吃饭
</script>

```

这时所有实例的`type`属性和`eat()`方法，其实都是同一个内存地址，指向`prototype`对象，因此就提高了运行效率。

```
alert(boy.eat == girl.eat); //true
```

**原型属性**是一个内置属性，它指定了对象所扩展的构造器函数。

**原型属性**是一个内置属性，它指定了对象所扩展的构造器函数。
下面的代码为`Animal`构造器函数添加一个新的属性`size`，这个新属性是`cat`对象的原型属性。通过使用原型属性，所有扩展`Animal`构造器函数的对象就可以访问`size`属性

```
cat = new Animal("feline","meow", "walk/run");
cat.prototype.size = "fat"; 
```

在这种情况下，所有的`Animal`对象的`size`属性都是“fat”。原型默认为`Object`的新实例, 由于仍是对象, 故可以给该对象添加新的属性。就好像`style`是`javascript`的一个对象一样，也可以往`style`后继续添加属性。

```
  <script type="text/javascript">
        /*定义一个Person类*/
        function Person(_name,_age,_salary){
            //Person类的公开属性，类的公开属性的定义方式是：”this.属性名“
            this.Name=_name;
            //Person类的私有属性，类的私有属性的定义方式是：”var 属性名“
            var Age=_age;
            var Salary=_salary;
    
            //定义Person类的公开方法(特权方法)，类的公开方法的定义方式
是：”this.functionName=function(){.....}“
            this.Show=function(){
   alert("Age="+Age+"\t"+"Salary="+Salary);//在公开方法里面访问类的私有属性是允许的
            }
</script>

```

对象在查找某个属性的时候，会首先遍历自身的属性，如果没有则会继续查找`[[Prototype]]`引用的对象，如果再没有则继续查找`[[Prototype]].[[Prototype]]`引用的对象，依次类推，直到`[[Prototype]].….[[Prototype]]`为`undefined`（`Object`的`[[Prototype]]`就是`undefined`）

简单说就是通过对象的`[[Prototype]]`保存对另一个对象的引用，通过这个引用往上进行属性的查找，这就是原型链。

### null 对象

`js`给变量赋`null`值的作用在于：

`js`给变量赋`null`值的作用在于：
赋值一个空指针，容易让人理解这个变量是准备用来存放对象的。也方便调错

### 全局的window对象

`JavaScript`中的任何一个全局函数或变量都是`window`的属性。

`JavaScript`中的任何一个全局函数或变量都是`window`的属性。
`self`对象与`window`对象完全相同，`self`通常用于确认就是在当前的窗体内。

　window的主对象主要有如下几个：

```
JavaScript document 对象
JavaScript frames 对象
JavaScript history 对象
JavaScript location 对象
JavaScript navigator 对象
JavaScript screen 对象

```

### 几个常用方法

```
valueof()方法：返回指定对象的原始值
split() 方法将字符串分割为字符串数组，并返回此数组。
indexOf() 方法可返回某个指定的字符串值在字符串中首次出现的位置。    
substring() 方法用于提取字符串中介于两个指定下标之间的字符。
substr() 方法从字符串中提取从 startPos位置开始的指定数目的字符串。    
join()方法用于把数组中的所有元素放入一个字符串。
arrayObject.join(分隔符)
reverse() 方法用于颠倒数组中元素的顺序。    
slice() 方法可从已有的数组中返回选定的元素。

```

## 对象字面量

对象字面量是用于创建包含大量属性的过程，如下所示：

```
<script type="text/javascript">
    var company = {
        name : "Microsoft",
        ages : 39,
        employees : 99000,
        CEO : "Nadella"
    };     
</script>

```

这里需要注意的是属性和属性值以冒号(`:`)隔开；多个属性用逗号(`,`)隔开。对象字面量亦可以定义方法，只需在这个对象的属性上写上`function`就行，这是一个匿名函数，调用它只需要写上他的`方法名()`即可。

```
<script type="text/javascript">
var dog = {
   name:"husky",
   age:2,
   run:function(){
              return "123";
}
}
alert(dog.run());//如果输入dog.run,那么会弹出它后面的function部分的代码
</script>

```

## 基本值类型包装器

`js`有五种基本的值类型：`number、string、Boolean、null和undefined`。除了`null和undefined`外，其他三个都具有所谓的基本包装对象。可以使用内置构造函数`Number()`、`String()`和`Boolean()`创建包装对象。

```
var num = new Number(10);
console.log(typeof num);//object

```

## Object()方法

```
Object() // 返回一个空对象
Object(undefined) // 返回一个空对象
Object(null) // 返回一个空对象

Object(1) // 等同于 new Number(1)
Object('foo') // 等同于 new String('foo')
Object(true) // 等同于 new Boolean(true)

Object([]) // 返回原数组
Object({}) // 返回原对象
Object(function(){}) // 返回原函数

```

## 数组部分

### 1.Array 对象

`Array` 对象：提供对创建任何数据类型的数组的支持。

```
arrayObj = new Array()
arrayObj = new Array([size])
arrayObj = new Array([element0[, element1[, ...[, elementN]]]])

```

定义：`var arr = [2,3,45,6]; var arr = new Array(2,4,5,7)`

定义：`var arr = [2,3,45,6]; var arr = new Array(2,4,5,7)`
两者是定义没有任何差别，`[]`的性能高，因为代码短。

使用数组和对象字面量：var aTest = `[]`；创建数组时，使用数组字面量是个好选择；类似的，对象字面量也可用于节省空间。以下两行是相等的，但是使用对象字面量的更加简短：

```
 var oTest = new Object;  //尽量不用
 var oTest = { };    //最好的选择，或者var 0Test = [ ];

```

遍历为了达到遍历数组的最佳性能，推荐使用经典的 `for` 循环。

```
var list = [1, 2, 3, 4, 5, ...... 100000000];
for(var i = 0, l = list.length; i < l; i++) {
    console.log(list[i]);
}

```

上面代码有一个处理，就是通过 `l = list.length` 来缓存数组的长度。

### Array 构造函数

由于 `Array` 的构造函数在如何处理参数时有点模棱两可，因此总是推荐使用数组的字面语法 - `[]` - 来创建数组。

因此下面的代码将会使人很迷惑：

```
new Array(3, 4, 5); // 结果: [3, 4, 5] 
new Array(3) // 结果: []，此数组长度为 3
```

应该尽量避免使用数组构造函数创建新数组。推荐使用数组的字面语法。它们更加短小和简洁，因此增加了代码的可读性。

## Array数组的属性

**Array**数组的3个属性：`length属性、prototype属性、constructor属性`

### 1.length属性

Length属性表示数组的长度，即其中元素的个数。因为数组的索引总是由0开始，所以一个数组的上下限分别是：0和length-1。和其他大多数语言不同的是，JavaScript数组的length属性是可变的，这一点需要特别注意。

### 2.prototype属性

返回对象类型原型的引用。`prototype`属性是`object`共有的。

对于`Array`数组对象，以以下例子说明`prototype`属性的用途。

对于`Array`数组对象，以以下例子说明`prototype`属性的用途。
给数组对象添加返回数组中最大元素值的方法。要完成这一点，声明一个函数，将它加入`Array.prototype`，并使用它。

```
function array_max()  
{  
var i,max=this[0];  
for(i=1;i<this.length;i++)  
{  
if(max<this[i])  
max=this[i];  
}  
return max;  
}  
 
Array.prototype.max=array_max;  
var x=new Array(1,2,3,4,5,6);  
var y=x.max();  
  
```

*该代码执行后，y保存数组x中的最大值，或说6。*

### 3.constructor属性

表示创建对象的函数。说明：`constructor`属性是所有具有`prototype`的对象的成员。它们包括除`Global`和`Math`对象以外的所有`JScript`固有对象。`constructor`属性保存了对构造特定对象实例的函数的引用。

**例如：**

```
x = new String("Hi");  
if(x.constructor==String) //进行处理（条件为真）。  
//或  
function MyFunc{  
//函数体。  
}  
 
y=new MyFunc;  
if(y.constructor==MyFunc)//进行处理（条件为真）。

```

**对于数组来说：**

```
y = new Array(); 

```

## Array 对象方法

| 方法               | 描述                              |
| ---------------- | ------------------------------- |
| concat()         | 连接两个或更多的数组，并返回结果。               |
| join()           | 把数组的所有元素放入一个字符串。元素通过指定的分隔符进行分隔。 |
| pop()            | 删除并返回数组的最后一个元素                  |
| push()           | 向数组的末尾添加一个或更多元素，并返回新的长度。        |
| reverse()        | 颠倒数组中元素的顺序。                     |
| shift()          | 删除并返回数组的第一个元素                   |
| slice()          | 从某个已有的数组返回选定的元素                 |
| sort()           | 对数组的元素进行排序                      |
| splice()         | 删除元素，并向数组添加新元素。                 |
| toSource()       | 返回该对象的源代码。                      |
| toString()       | 把数组转换为字符串，并返回结果。                |
| toLocaleString() | 把数组转换为本地数组，并返回结果。               |
| unshift()        | 向数组的开头添加一个或更多元素，并返回新的长度。        |
| valueOf()        | 返回数组对象的原始值                      |

### sort()方法

语法

```
arrayObject.sort(sortby)

```

`sortby`可选。规定排序顺序。必须是函数。

```
var arr = [11,2,28,4,5,1];
console.log(arr.sort());//return  [1, 11, 2, 28, 4, 5]
```

为毛这里的11、28没有按照顺序来排列呢？这是因为不带参数的`sort`是按照字符编码的顺序进行排序的。

为毛这里的11、28没有按照顺序来排列呢？这是因为不带参数的`sort`是按照字符编码的顺序进行排序的。
那么，如果要让数组元素按照从小到大排序呢？看下面代码：

```
var arr = [11,2,28,4,5,1];
    console.log(arr.sort(function(a,b){
        return a-b;//return  [1, 2, 4, 5, 11, 28]
    })); 

```

如果想按照其他标准进行排序，就需要提供比较函数，该函数要比较两个值，然后返回一个用于说明这两个值的相对顺序的数字。比较函数应该具有两个参数 a 和 b，其返回值如下：

```
若 a 小于 b，在排序后的数组中 a 应该出现在 b 之前，则返回一个小于 0 的值。
若 a 等于 b，则返回 0。
若 a 大于 b，则返回一个大于 0 的值。
```

