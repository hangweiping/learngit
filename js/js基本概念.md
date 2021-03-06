## 基本概念

`javascript`是一门解释型的语言，浏览器充当解释器。`js`执行时，在同一个作用域内是先解释再执行。解释的时候会编译`function`和`var`这两个关键词定义的变量，编译完成后从上往下执行并向变量赋值。

### 区分大小写

`ECMASCript`中的一切（包括变量，函数名和操作符）都区分大小写。

## 1. 变量

变量在第一次用到时就设置于内存中，便于后来在脚本中引用。使用变量之前先进行声明。可以使用 `var` 关键字来进行变量声明。

`var count, amount, level; // 用单个 var 关键字声明的多个声明。`

### 变量命名

变量名包括全局变量，局部变量，类变量，函数参数等等，他们都属于这一类。

变量命名都以类型前缀+有意义的单词组成，用驼峰式命名法增加变量和函式的可读性。例如：`sUserName，nCount。`

**前缀规范：**

**前缀规范：**
每个局部变量都需要有一个类型前缀，按照类型可以分为：

```
s：表示字符串。例如：sName，sHtml；
n：表示数字。例如：nPage，nTotal；
b：表示逻辑。例如：bChecked，bHasLogin；
a：表示数组。例如：aList，aGroup；
r：表示正则表达式。例如：rDomain，rEmail；
f：表示函数。例如：fGetHtml，fInit；
o：表示以上未涉及到的其他对象，例如：oButton，oDate；
g：表示全局变量，例如：gUserName，gLoginTime；

```

`JScript` 是一种区分大小写的语言。创建合法的变量名称应遵循如下规则：

> 注意第一个字符不能是数字。
>
> ```
> 后面可以跟任意字母或数字以及下划线，但不能是空格
> 变量名称一定不能是 保留字。
> ```

`javascript`是一种弱类型语言，`JavaScript` 会忽略多余的空格。您可以向脚本添加空格，来提高其可读性。

`var`是`javascript`的保留字，表明接下来是变量说明，变量名是用户自定义的标识符，变量之间用逗号分开。

如果声明了一个变量但没有对其赋值，该变量存在，其值为`Jscript` 值 `undefined`。

### 强制类型转换

在 `Jscript` 中，可以对不同类型的值执行运算，不必担心 `JScript` 解释器产生异常。相反，`JScript` 解释器自动将数据类型之一改变（强制转换）为另一种数据类型，然后执行运算。例如：

```
运算                    结果

数值与字符串相加    将数值强制转换为字符串。
布尔值与字符串相加    将布尔值强制转换为字符串。
数值与布尔值相加    将布尔值强制转换为数值。

```

要想显式地将字符串转换为整数，使用 `parseInt` 方法。要想显式地将字符串转换为数字，使用 `parseFloat` 方法。

`JavaScript` 变量的生存期：当您在函数内声明了一个变量后，就只能在该函数中访问该变量。当退出该函数后，这个变量会被撤销。这种变量称为本地变量。您可以在不同的函数中使用名称相同的本地变量，这是因为只有声明过变量的函数能够识别其中的每个变量。

`JavaScript` 变量的生存期：当您在函数内声明了一个变量后，就只能在该函数中访问该变量。当退出该函数后，这个变量会被撤销。这种变量称为本地变量。您可以在不同的函数中使用名称相同的本地变量，这是因为只有声明过变量的函数能够识别其中的每个变量。
如果您在函数之外声明了一个变量，则页面上的所有函数都可以访问该变量。这些变量的生存期从声明它们之后开始，在页面关闭时结束。

## 2.js的数据类型

jscript 有三种->主要数据类型、两种->复合数据类型和两种->特殊数据类型。

### 主要（基本）数据类型

```
字符串
数值
布尔

```

### 复合（引用）数据类型

```
对象
数组

```

### 特殊数据类型

```
 Nul
`Undefined`
```

------

字符串数据类型：字符串数据类型用来表示 `JScript` 中的文本。在`js`中，虽然双引号（""）和单引号('')均可表示字符串，而且它们几乎没有任何区别。但只使用`双引号`("")来表示字符串被认为是最佳的。

一个字符串值是排在一起的一串零或零以上的 `Unicode` 字符（字母、数字和标点符号）。

### 什么是Unicode？

`Unicode`为每个字符都提供了唯一的数值，不管是什么平台、什么程序或什么语言。开发`unicode`是为了给处理世界上存在的所有字符提供统一的编码。

### 数值数据类型

我们需要明白一点，`JScript` 内部将所有的数值表示为浮点值，因此，在 `Jscript` 中整数和浮点值没有差别。

### Boolean数据类型

布尔（逻辑）只能有两个值：`true` 或 `false`。

------

## js数组和对象

详情看我这篇文章->[javascript学习总结— —数组和对象部分](http://segmentfault.com/blog/trigkit4/1190000000653028)

**Null 数据类型：**可以通过给一个变量赋 null 值来清除变量的内容。

`Jscript`中 `typeof` 运算符将报告 `null` 值为 `Object` 类型，而非类型 `null`。

`null`用来表示尚未存在的对象，常用来表示函数企图返回一个不存在的对象。

`Undefined` 数据类型：

如下情况将返回 `undefined` 值：

```
对象属性不存在，
声明了变量但从未赋值。

```

### null和undefined的区别

```
alert(typeof undefined); //output "undefined"  
alert(typeof null); //output "object"  
alert(null == undefined); //output "true" 

```

`ECMAScript`认为`undefined`是从`null`派生出来的，所以把它们定义为相等的。

```
alert(null === undefined); //output "false"  
alert(typeof null == typeof undefined); //output "false"  
```

`null`与`undefined`的类型是不一样的，所以输出"`false`"。而===代表绝对等于，在这里`null === undefined`输出`false`

另外，这里介绍一种比较重要的数据类型——引用数据类型

### 引用数据类型

`javascript`引用数据类型是保存在堆内存中的对象，`JavaScript`不允许直接访问堆内存空间中的位置和操作堆内存空间，只能通过操作对象在栈内存中的引用地址。所以引用类型的数据，在栈内存中保存的实际上是对象在堆内存中的引用地址。通过这个引用地址可以快速查找到保存在堆内存中的对象。

下面我们来演示这个引用数据类型赋值过程

![](http://segmentfault.com/img/bVcTZw)

自然，给`obj2`添加`name`属性，实际上是给堆内存中的对象添加了`name`属性，`obj2`和`obj1`在栈内存中保存的只是堆内存对象的引用地址，虽然也是拷贝了一份，但指向的对象却是同一个。故而改变`obj2`引起了`obj1`的改变。

**基本类型值**指的是那些保存在栈内存中的简单数据段，即这种值完全保存在内存中的一个位置。
而**引用类型值**则是指那些保存在堆内存中的对象，即变量中保存的实际上只是一个指针，这个指针指向内存中的另一个位置，该位置保存对象。
简而言之，**堆内存**存放引用值，**栈内存**存放固定类型值。
![img](http://segmentfault.com/img/bVcTZz)

在 `ECMAScript` 中，变量可以存在两种类型的值，即原始值和引用值。

原始值存储在栈（`stack`）中的简单数据段，也就是说，它们的值直接存储在变量访问的位置。引用值存储在堆（`heap`）中的对象，也就是说，存储在变量处的值是一个指针（`point`），指向存储对象的内存处。

```
<script type="text/javascript”>
var box = new Object();  //创建一个引用类型
var box = "lee";   //基本类型值是字符串
box.age = 23;    //基本类型值添加属性很怪异，因为只有对象才可以添加属性。
alert(box.age);  //不是引用类型，无法输出；
</script>
```

## 3.JScript 的运算符

优先级：指运算符的运算顺序，通俗的说就是先计算哪一部分。

优先级：指运算符的运算顺序，通俗的说就是先计算哪一部分。
结合性：同一优先级运算符的计算顺序，通俗的说就是从哪个方向算起，是左到右还是右到左。

### 数据类型转换和基本包装类型

`String()` 转换为字符串类型

`String()` 转换为字符串类型
`Number()` 转换为数字类型

`String()` 转换为字符串类型
`Number()` 转换为数字类型
`Boolean()` 转换为布尔类型

`parseInt`：将字符串转换为整数。从字符串的开头开始解析，在第一个非整数的位置停止解析，并返回前面读到所有的整数。如果字符串不是以整数开头的，将返回NaN。如：parseInt（“150 hi”）返回的值是：150，parseInt("hi")返回的值是：NaN。

`parseInt`：将字符串转换为整数。从字符串的开头开始解析，在第一个非整数的位置停止解析，并返回前面读到所有的整数。如果字符串不是以整数开头的，将返回NaN。如：parseInt（“150 hi”）返回的值是：150，parseInt("hi")返回的值是：NaN。
`parseFloat`：将字符串转换为浮点数。 从字符串的开头开始解析，在第一个非整数的位置停止解析，并返回前面读到所有的整数。如果字符串不是以整数开头的，将返回NaN。如：`parseFloat("15.5 hi") 返回的值是：15.5，parseFloat("hi 15.5")返回的值是：NaN。`

```
eval：将字符串作为javascript表达式进行计算，并返回执行结果，如果没有结果则返回undefined。

```

### 基本包装类型

每当读取一个基本类型值的时候，后台就会创建一个对应的基本包装类型的对象，从而能调用一些方法来操作这些数据。基本包装类型包括`Boolean`、`Number`和`String`

```
var box = 'trigkit4'; //字面量
box.name = 'mike';   //无效属性
box.age = function () { //无效方法
    return 22;
};
 
//new运算符写法
var box = new String('trigkit4');//new 运算符
box.name = 'mike';   //有效属性
box.age = function () { //有效方法
    return 22;
};
```

`String`类型包含了三个属性和大量的可用内置方法

```
属性    描述
length :返回字符串的字符长度
Constructor : 返回创建String对象的函数
prototype : 通过添加属性和方法扩展字符串定义
```

为了便于操作基本类型值，ECMAScript还提供了三个特殊的引用类型：Boolean、Number和String。这些类型与其他引用类型相似，同时也具有与各自的基本包装类型相应的特殊行为。实际上，每当读取一个基本类型值时，后台就会创建一个对应的基本包装类型的对象，从而让我们能够调用一些方法来操作这些数据。

```
var s1 = "some text";
var s2 = s1.substring(2);
```

    这个例子中的变量s1包含一个字符串，字符串当然是基本类型值。而下一行调用了s1的substring()方法，并将返回的结果保存在s2中。我们知道，基本类型值不是对象，因此从逻辑上讲，它们不应该有方法（但是它们确实有方法）。其实，为了让我们实现这种直观的操作，后台已经自动完成了一系列的处理。当第二行代码访问s1时，访问过程处于一种读取模式，也就是从内存中读取这个字符串的值。而在读取模式中访问字符串时，后台都会自动完成下列处理：

    （1）创建String类型的一个实例。

    （2）在实例上调用指定方法。

    （3）销毁这个实例。

    可以使用以下代码表示：

```
var s1 = new String("some text");
var s2 = s1.substring(2);
s1 = null;
```

    经过此番处理，基本的字符串值就变得跟对象一样了。而且，上面三个步骤也适用于Boolean和Number类型对应的布尔值和数字值。

    引用类型与基本包装类型的主要区别就是对象的生命周期。使用new操作符创建的引用类型的实例，在执行流离开当前作用域之前都一直保存在内存中。而自动创建的基本包装类型的对象，只存在于这一行代码的执行期(瞬间)，然后立即销毁。这就意味着我们不能在运行时为属性添加属性和方法。

```
var s1 = "some text";
s1.color = "red";
alert(s1.color); //undefined
```

    当然，可以显示调用Boolean、Number和String来创建基本包装类型的对象，不过，不帮不建议这么做。对基本包装类型的实例调用typeof会返回"object",而且所有基本包装类型的对象都会被转化为布尔值true。。

```
var obj = new Object("some text");
alert(obj instanceof String) //true
```

    值得注意的是，使用new调用基本包装类型的构造函数，与直接调用同名的转型函数是不一样的。

```
var value = "25";
var number = Number(value);//转型函数
alert(typeof number) //number
 
var obj = new Number(var); //构造函数
alert(typeof obj) //object
```

## 4.js流程控制

对于js流程控制语句，这里只讲几个比较难懂的。其他不赘述。等下附上一张思维导图。

对于js流程控制语句，这里只讲几个比较难懂的。其他不赘述。等下附上一张思维导图。
1.for...in 语句对应于一个对象的每个，或一个数组的每个元素，执行一个或多个语句。

```
for (variable in [object | array])
statements 
```

参数：

`variable`：必选项。一个变量，它可以是 object 的任一属性或 array 的任一元素。

`variable`：必选项。一个变量，它可以是 object 的任一属性或 array 的任一元素。
`object`, `array`：可选项。要在其上遍历的对象或数组。

`variable`：必选项。一个变量，它可以是 object 的任一属性或 array 的任一元素。
`object`, `array`：可选项。要在其上遍历的对象或数组。
`statement`：可选项。相对于 object 的每个属性或 array 的每个元素，都要被执行的一个或多个语句。可以是复合语句。

虽然条件控制语句（如if语句）只在执行多条语句的情况下才要求使用代码块（左花括号"{"开头，右花括号"}"结尾）,但最佳实践是始终使用代码块。

```
if(args)
    alert(args);//容易出错
    
if(args){
    alert(args);//推荐使用
}
```

## 5.js函数

函数是由事件驱动的或者当它被调用时执行的可重复使用的代码块。

`Jscript` 支持两种函数：一类是语言内部的函数，另一类是自己创建的。

`Jscript` 支持两种函数：一类是语言内部的函数，另一类是自己创建的。
`JavaScript` 函数允许没有参数（但包含参数的小括号不能省略），也可以向函数传递参数供函数使用。

更多关于函数的知识请访问我的另一篇文章：[javascript学习大总结（四）function函数部分](http://blog.segmentfault.com/trigkit4/1190000000660786)

### 对象的组成

```
方法——函数：过程、动态的
属性——变量：状态、静态的
```