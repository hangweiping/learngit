## JS的6种实现继承的方式及Object.create

> **一般只使用<u>第三种</u>**

### ①原型链式继承

```javascript
 1 function Person(name){
 2     this.name=name;
 3 }
 4 
 5 Person.prototype.getName=function(){
 6     return this.name;
 7 }
 8 
 9 function Male(age){
10     this.age=age;
11 }
12 
13 Male.prototype=new Person("John");
14 
15 Male.prototype.getAge=function(){
16     return this.age;
17 }
18 
19 var maleA=new Male(12);
20 console.log(maleA.getAge());//12
21 console.log(maleA.getName());//John
22 
23 var maleB=new Male(25);
24 console.log(maleB.getAge());//25
25 console.log(maleB.getName());//John
```

原型链式继承的实质是重写构造函数的原型对象，代之以一个所要继承的类型的实例。该例子中Male类型继承了Person类型。这种继承方式，所有的子类型共享父类型的属性和方法，因为原型指向的是同一个父类型实例。所以，当任一个子类型实例改变了继承来的原型属性的值，那么其他实例的原型属性的值也将被改变。为了解决这个问题，出现了借用构造函数式继承。

### ②借用构造函数式继承

```javascript
 1 function Person(name){
 2     this.name=name;
 3 }
 4 
 5 Person.prototype.getName=function(){
 6     return this.name;
 7 }
 8 
 9 function Male(name,age){
10     Person.call(this,name);
11     this.age=age;
12 }
13 
14 Male.prototype.getAge=function(){
15     return this.age;
16 }
17 
18 var maleA=new Male("A",12);
19 console.log(maleA.getAge());//12
20 console.log(maleA.name);//A
21 console.log(typeof maleA.getName);//undefined
22 
23 var maleB=new Male("B",25);
24 console.log(maleB.getAge());//25
25 console.log(maleB.name);//B
26 console.log(typeof maleB.getName);//undefined
```

借用构造函数式继承的实质是在构造函数内调用父类型的构造函数，那么子类型将继承父类型构造函数内所添加的属性和方法。借用构造函数式继承的优点是可以在继承父类型的时候向父类型的构造函数传递参数，缺点是不能继承定义在父类型原型上的方法和属性。为了解决借用构造函数式继承的这个缺点，出现了组合式继承。

### ③组合式继承

```javascript
 1 function Person(name){
 2     this.name=name;
 3 }
 4 
 5 Person.prototype.getName=function(){
 6     return this.name;
 7 }
 8 
 9 function Male(name,age){
10     Person.call(this,name);
11     this.age=age;
12 }
13 
14 Male.prototype=new Person();
15 
16 Male.prototype.getAge=function(){
17     return this.age;
18 }
19 
20 var maleA=new Male("A",12);
21 console.log(maleA.getAge());//12
22 console.log(maleA.name);//A
23 console.log(maleA.getName());//A
24 
25 var maleB=new Male("B",25);
26 console.log(maleB.getAge());//25
27 console.log(maleB.name);//B
28 console.log(maleB.getName());//B
```

组合式继承是将原型链式继承和构造函数式继承组合在一起使用，从而发挥两者的长处。其背后的思路是使用原型链来实现对原型属性和方法的继承，而通过借用构造函数实现对实例属性的继承。这样，既通过在原型上定义方法实现了函数复用，又能保证每个实例都有它自己的属性。

### ④原型式继承

```javascript
 1 function object(o){
 2     function F(){}
 3     F.prototype=o;
 4     return new F();
 5 }
 6 
 7 var person={};
 8 person.name='John';
 9 person.getName=function(){
10     return this.name;
11 }
12 
13 var male=object(person);
14 male.age=22;
15 male.getAge=function(){
16     return this.age;
17 }
18 
19 console.log(male.name);//John
20 console.log(male.getName())//John
21 console.log(male.age);//22
22 console.log(male.getAge())//22
```

原型式继承是基于现有对象然后借助原型创建新的对象，新对象拥有现有对象的所有属性和方法，同时还不用创建自定义类型，其实质上是对现有对象的浅复制，新对象与现有对象共享现有对象的属性和方法。ECMASctipt5通过新增Object.create()方法规范了原型式继承。这个方法接受两个参数，一个用作新对象原型的对象和（可选的）一个为新对象定义额外属性的对象。上面的例子可以改写为：

```javascript
 1 var person={};
 2 person.name='John';
 3 person.getName=function(){
 4     return this.name;
 5 }
 6 
 7 var male=Object.create(person,{
 8     age:{
 9         value:22
10     }
11 });
12 
13 male.getAge=function(){
14     return this.age;
15 }
16 console.log(male.name);//John
17 console.log(male.getName())//John
18 console.log(male.age);//22
19 console.log(male.getAge());//22
```

### ⑤寄生式继承

```javascript
 1 function Male(original,age){
 2     var clone=Object.create(original);
 3     clone.age=age;
 4     clone.getAge=function(){
 5         return this.age;
 6     }
 7     
 8     return clone;
 9 }
10 
11 var person={};
12 person.name="John";
13 person.getName=function(){
14     return this.name;
15 }
16 
17 var male=Male(person,22);
18 
19 console.log(male.name);//John
20 console.log(male.getName())//John
21 console.log(male.age);//22
22 console.log(male.getAge());//22
```

寄生式继承与寄生构造函数和工厂模式类似，即创建一个仅用于封装继承过程的函数，该函数在内部以某种方式来增强对象，最后再像真是它做了所有工作一样的返回对象。这种继承方式新对象与现有对象共享现有对象的所有属性和方法。改变先有对象的属性的值，新对象对应属性的值也将改变。原型式继承和寄生式继承，都不能实现函数的复用。为了解决这一问题，出现了寄生组合式继承。

### ⑥寄生组合式继承

```javascript
 1 function inheritPrototype(subType,superType){
 2     var prototype=Object.create(superType.prototype);
 3     prototype.constructor=subType;
 4     subType.prototype=prototype;
 5 }
 6 
 7 function Person(name){
 8     this.name=name;
 9 }
10 
11 Person.prototype.getName=function(){
12     return this.name;
13 }
14 
15 function Male(name,age){
16     Person.call(this,name);
17     this.age=age;
18 }
19 
20 inheritPrototype(Male,Person);
21 
22 Male.prototype.getAge=function(){
23     return this.age;
24 }
25 
26 var maleA=new Male("A",22)
27 
28 console.log(maleA.name);//A
29 console.log(maleA.getName())//A
30 console.log(maleA.age);//22
31 console.log(maleA.getAge());//22
```

寄生组合式继承，即通过借用构造函数来继承属性，通过原型链的混成形式来继承方法，其背后的基本思路是：不必为了指定子类型的原型而调用父类型的构造函数，我们所需要的无非就是父类型的原型的一个副本而已。本质上，就是使用寄生式继承来继承父类型的原型，然后再将结果指定给子类型的原型。



## 原型模式 : Object.create 与 prototype

### 原型模式说明

说明：使用原型实例来 拷贝 创建新的可定制的对象；新建的对象，不需要知道原对象创建的具体过程；

过程：Prototype => new ProtoExam => clone to new Object;

使用相关代码:

```javascript
function Prototype() {
    this.name = '';
    this.age = '';
    this.sex = '';
}

Prototype.prototype.userInfo = function() {
    return '个人信息, 姓名: '+this.name+', 年龄: '+this.age+', 性别:'+this.sex+'<br />';
}
```

现在需要两个或以上的个人信息内容:

```javascript
var proto = new Prototype();
var person1 = Object.create(proto);
person1.name = '小明';
person1.sex = '男';
person1.age = 35;
person1.userInfo(); 
//
var person2 = Object.create(proto);
person2.name = '小华';
person2.sex = '女';
person2.age = 33;
person2.userInfo();
```

输出返回:

```shell
个人信息, 姓名: 小明, 年龄: 35, 性别: 男
个人信息, 姓名: 小华, 年龄: 33, 性别: 女
```

原型模式，一般用于 抽象结构复杂，但内容组成差不多，抽象内容可定制，新创建只需在原创建对象上稍微修改即可达到需求的情况；

### Object.create 使用说明

1>. 定义: 创建一个可指定原型对象的并可以包含可选自定义属性的对象；

2> Object.create(proto [, properties]);  可选，用于配置新对象的属性;

```
1. proto: 要创建新对象的 原型，必须，可为 null; 这个 proto 要是已经创建的[new过]，或 对象.prototype 才有价值;
2. properties: 可选，结构为:
{
     propField: {
       value: 'val'|{}|function(){},
       writable: true|false,
       enumerable: true|false,
       configurable: true|false,
       get:function(){return 10},
       set:function(value){}
     }
}
自定的属性有以下的四种本置属性：
value: 自定义属性值;
writable: 该项值是否可编辑，默认为 false, 当为 true 时，obj.prodField 可赋值；否则只读;
enumerable: 可枚举; 
confirurable: 可配置;
还可以包含 set, get 访问器方法；
其中，[set, get] 与 value 和 writable 不能同时出现；
```

- 创建原型对象类:

```
function ProtoClass(){
   this.a = 'ProtoClass';
   this.c = {};
   this.b = function() {
   }
}
```

创建原型方法:

```
ProtoClass.prototype.aMethod = function() {
     //this.a;
     //this.b();
　　 return this.a;
}
```

### 使用方法

- 以 ProtoClass.prototype 创建一个对象;

```
var obj1 = Object.create(ProtoClass.prototype, {
    foo:{value: 'obj1', writable: true}
})
```

obj1 就具有 ProtoClass 原型方法 aMethod 的方法;

```
obj1.aMethod();
//就会输出 undefined 方法可访问，ProtoClass 成员无法访问
```

但是这种方法执行不到 ProtoClass 下 a, b, c 的成员属性:

- 采用 实例化的 ProtoClass 做原型:

```
var proto = new ProtoClass();

var obj2 = Object.create(proto, {
    foo:{value:'obj2'}
});
```

这样创建的 obj2 就具有 ProtoClass 的所有的成员属性 a, b, c 以及 aMethod 原型方法; 并添加了一个 foo 只读 数据属性；

```
obj2.a; //ProtoClass
obj2.c: //[Object]
obj2.b(); //

obj2.aMethod(); //ProtoClass
obj2.foo; //obj2

```

- 子类继承:

```javascript
function SubClass() {
}
SubClass.prototype = Object.create(ProtoClass.prototype ,{
  foo:{value: 'subclass'}
});
SubClass.prototype.subMethod = function() {
   return this.a || this.foo;
}
```

这种方法可以继承 到 ProtoClass 的 aMethod 方法，执行;

```javascript
var func = new SubClass();
func.aMethod() ;//undefined,读不到 ProtoClass 的成员属性,a,b,c
func.subMethod();//subclass
```

要让 SubClass 能读取到 ProtoClass 的成员属性，SubClass 要改下:

```javascript
function SubClass()
{
    ProtoClass.call(this);
}

//其他代码;
```

这种方法就可以获取 ProtoClass 的成员属性及原型方法;:

```javascript
var func = new SubClass();
func.aMethod() ;//ProtoClass
func.subMethod();//ProtoClass
```

还有一种方法，就是使用 实例化的 ProtoClass 对象，做为 SubClass 的原型;

```javascript
var proto = new ProtoClass();

function SubClass() {
}

SubClass.prototype = Object.create(proto, {
    foo:{value: 'subclass'}
});
```

这样 SubClass 实例化后，就可以获取到 ProtoClass 所有的属性及原型方法，以及创建一个只读数据属性 foo;

```javascript
var func = new SubClass();
func.foo; //subclass
func.a; //ProtoClass
func.b(); //
func.c; //[Object]
func.aMethod(); //ProtoClass
```

- 另外的创建继承方法，跟 Object.create 使用 实例化的ProtoClass 做原型 效果一样:

```javascript
function SubClass() {
　　this.foo = 'subclass'; //不过这边可读写
}

SubClass.prototype = new ProtoClass();
```

### Object.create 相关说明

Object.create 用于创建一个新的对象，当为 Object 时 prototype 为 null, 作用与 new Object(); 或 {} 一致;

当为 function 时，作用与 new FunctionName 一样;

```javascript
//1 Object
var o = {}
//等同于
var o2 = Object.create({});
//两者 constructor 一样;
//-----------------------------------------
function func() {
  this.a = 'func';
}
func.prototype.method = function() {
  return this.a;
}
var newfunc = new func();
//等同于[效果一样]
var newfunc2 = Object.create(Object.prototype/*Function.prototype||function(){}*/, {
   a: {value:'func', writable:true},
   method: {value: function() {return this.a;} }
});
```

但是 newfunc 与 newfunc2 在创建它们的对象的函数引用是不一样的.

newfunc 为 function func() {...}，newfunc2 为 function Function { Native }

Object.create(proto[, propertiesField]):

proto 说明，该值为必须，可以为 null, 如果没设定，将会抛出异常;

proto 为非 null, 即为已 实例化的值，即已经 new 过的值；javaScript 中的 对象大多有 constructor 属性，这个属性说明 此对象是通过哪个函数实例化后的对象；

propertiesField 为可选项，设定新创建对象可能需要的成员属性或方法；