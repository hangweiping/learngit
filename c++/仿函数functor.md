> **仿函数(functor)**，又称函数对象，一种具有函数特质的对象。仿函数主要用于STL中的算法中，虽然函数指针虽然也可以作为算法的参数，但是函数指针不能满足STL对抽象性的要求，也不能满足软件积木的要求--函数指针无法和STL其他组件搭配，产生更灵活变化。仿函数是智能型函数就好比智能指针的行为像指针，其就可看作是一个指针。但是智能指针是定义的一个类对象，所以在具备指针功能的同时也有其他的能力。仿函数的能力也可以超越operator（）。**因为仿函数可以拥有成员函数和成员变量**，这意味着仿函数拥有状态。另一个好处是可以在执行期初始化它们。同时，STL还为仿函数提供了强大的配接器，也就是适配器Adapters，这些适配器本身也属于仿函数，调用方式同样类似于函数。更进一步，这些适配器可以和仿函数、函数、适配器再进行绑定、组合、适配，完成更加复杂的功能。

**<u>语法糖越甜，编译调试查错越苦！</u>**

**STL 不支持没有参数或两个以上参数的仿函数**, C++11标准对这部分调整比较大

**仿函数三大妙处：**

1. 仿函数比一般函数更灵巧，因为它可以永远状态，事实上对于仿函数，你可以同事拥有两个状态不同的实体，一般函数则力未能逮;
2. 每个仿函数都有其类型，因此你可以将仿函数的类型当作template参数来传递，从而指定某种行为模式。此外还有一个好处：容器类型也会因为仿函数的不同而不同;
3. 执行速度上，仿函数通常比普通函数更快.

## 仿函数的定义

在STL的六大组件中，仿函数可说是体积最小、观念最简单、实现最容易的一个，但小兵也能立大功——他扮演一种“策略”角色，可以让STL算法具有更加灵活的“演出”。

在STL的历史上，仿函数(functor)是早期的命名，C++标准规格定下来后采用了新的名称——函数对象(function object)。就实际意义而言，函数对象的称谓更加贴切：一种具有函数特质的对象。函数对象对调用者而言可以向函数调用一样地被调用，而对被调用者而言则是以对象所定义的函数调用操作符(function call operator)。

在C++中，函数调用操作符是指左右小括弧 `()` ，该操作符是可以重载的。许多 STL 算法都提供了两个版本，一个用于一般情况（例如排序时使用 `operator<` 以递增方式排列），一个用于特殊情况（例如排序时按照使用者自定义的大小关系进行排序）。这有点类似于C语言中的函数指针，但函数指针无法满足STL对抽象性的要求，也不能和STL其他组件（如配接器adaptor）搭配，产生更灵活的变化，关于这一点下一节将详细介绍。

函数对象可以看成是一种“操作”，排序时两两相邻元素满足使这种操作为真，当然我们可以设计一个函数，然后传递函数指针，但是我们需要抽象接口，函数指针不能与适配器搭配使用。所以这时候我们需要一个行为类似函数的对象。

## 函数对象分类

标准 C++ 库根据 operator() 参数个数为 0 个、1 个、 2 个将函数对象加以分类。主要有以下 5 种类型: 
发生器:    一种没有参数且返回一个任意类型值的函数对象，例如随机数发生器。 
一元函数:    一种只有一个任意类型的参数，且返回一个可能不同类型值的函数对象。 
二元函数:    一种有两个任意类型的参数，且返回一个可能不同类型值的函数对象。 
一元判定函数:    返回 bool 型的一元函数。 
二元判定函数:    返回 bool 型的二元函数。 
可以看出，STL 中函数对象最多仅适用于两个参数，但这已经足够完成相当强大的功能，使用 STL 的函数对象时，需要头文件<function>。  
C++98标准 与 C++11标准下对于函数对象的使用有较大改变，下面将介绍一下两者的区别与使用，这里我推荐使用 C++11标准下的函数对象。

## 系统函数对象:

STL 提供了部分内建函数对象，分为算数类、关系运算类和逻辑运算类，以及证同、选择、映射。

具体如下。 

STL 标准函数对象表: 

![](http://img1.tuicool.com/6bm6vaF.png!web)

前三个比较好理解，证同、选择、映射分别是：

**证同：** 函数名为 `identity` ，是一个一元函数（只有一个参数），传进去什么参数，就返回什么。好像有点傻逼，这个函数用于指定红黑树所需的 `KeyOfValue` 来实现 set，因为 set 的值既是 key 也是 value，identity(key)，就可以得到 value 了。这是为了 set 和 map 的统一，他们的底层机制都是一样的。

**选择：** 有两个函数分别是 `select1st` 和 `select2nd` ，两个函数都接收 **一个** pair，前者返回 pair 的第一个元素，第二个函数返回第二个元素，这两个函数都用来指定红黑树的 `KeyOfValue` 来实现 map。

**映射：** 也有两个函数 `project1st` 和 project2nd`，都接收两个参数，第一个函数返回第一个参数，第二个函数返回第二个参数。跟上面的选择函数的区别在于，选择是一元函数，只接收一个 pair 作为唯一的参数，而映射接收两个参数。

这些内置仿函数不只是为了单独调用，而是配合 STL 其他组件（算法和容器），变化出多种多样的版本。比如 `sort()` 函数，用 `greater` 就是从小到大排序，用 `less` 就是从大到小排序，而函数实现只有一个。

## 自定义仿函数

只要写一个 class 或 class template，然后重载 `()` 就完成一个简单的仿函数，但是，这样的函数跟 STL 无法兼容。为了能跟 STL 兼容，我们的仿函数还需要完成两件事情： 

1. 继承 binary_function 或者 unary_function。 
2. 重载 `()` 接收一个或者两个参数。

标准库提供了两种函数对象基类：

```cpp
 1 template <class _Arg, class _Result>
 2 struct unary_function {
 3   typedef _Arg argument_type;
 4   typedef _Result result_type;
 5 };
 6  
 7 template <class _Arg1, class _Arg2, class _Result>
 8 struct binary_function {
 9   typedef _Arg1 first_argument_type;
10   typedef _Arg2 second_argument_type;
11   typedef _Result result_type;
12 }; 
```

其中，类unary_function应用于一元操作符，它会接受两个模板参数，一个作为其返回值类型，另一个作为其参数类型；相应地，binary_function应用于二元操作符。

## 函数对象的适配器

![img](http://pic002.cnblogs.com/images/2012/384029/2012042112015557.png)

　　STL为支持标准函数对象的组合提供了标准函数对象的适配器。

　　这些适配器都有共同的特征：它们都依赖于函数对象基类unary_function, binary_function, 对这些适配器中的每一个都提供了一个协助函数，它以一个函数对象为参数，返回另一个合适的函数对象。也就是说，这种适配器是一种形式简单的高阶函数，它以一个函数作为参数并具此产生另一个新的函数[1]。

#### 约束器

bind1st和bind2nd可以绑定二元函数对象的某一个值。值得注意的是：bind1st和bind2nd不是函数对象，它们是普通的函数，它们的输入参数中，第一个参数是二元函数对象，第二个参数是要绑定的值（对于bind1st是绑定二元函数参数的第一个参数，对于bind2nd是绑定二元函数参数的第二个参数）。

下面是bind1st的完整实现代码：

```cpp
 1 template <class _Operation>
 2 class binder1st
 3   : public unary_function<typename _Operation::second_argument_type,
 4                           typename _Operation::result_type> {
 5 protected:
 6   _Operation op;
 7   typename _Operation::first_argument_type value;
 8 public:
 9   binder1st(const _Operation& __x,
10             const typename _Operation::first_argument_type& __y)
11       : op(__x), value(__y) {}
12   typename _Operation::result_type
13   operator()(const typename _Operation::second_argument_type& __x) const {
14     return op(value, __x);
15   }
16 };
17  
18 template <class _Operation, class _Tp>
19 inline binder1st<_Operation>
20 bind1st(const _Operation& __fn, const _Tp& __x)
21 {
22   typedef typename _Operation::first_argument_type _Arg1_type;
23   return binder1st<_Operation>(__fn, _Arg1_type(__x));
24 }
```

在我们最上面提供的替换大于5的元素的实现中，我们可以像下面的方式这样调用bind1st：

```
1 #include <functional>
2 ...
3 replace_if(vec.begin(), vec.end(), bind1st(less<int>(), 5), 8);
```

#### 成员函数适配器

![img](http://pic002.cnblogs.com/images/2012/384029/2012042112021981.png)

有时候，用户要给某个算法传递一个对象的成员函数，这个时候，我们需要用到成员函数的适配器mem_fun()和mem_fun_ref()。

其中，mem_fun()接受一个对象指针传递过来的成员函数，mem_fun_ref()接受一个对象引用传递过来的成员函数，它们都返回一个函数对象。

下面摘录一部分mem_fun()的实现代码：

```cpp
 1 //这个版本的mem_fun接受一个指针、无参、非void返回值、非const成员函数
 2 // _Tp::*f 表示指向类_Tp成员函数的指针
 3 template <class _Ret, class _Tp>
 4 inline mem_fun_t<_Ret,_Tp> mem_fun(_Ret (_Tp::*__f)())
 5   { return mem_fun_t<_Ret,_Tp>(__f); }
 6  
 7 template <class _Ret, class _Tp>
 8 class mem_fun_t : public unary_function<_Tp*,_Ret> {
 9 public:
10   explicit mem_fun_t(_Ret (_Tp::*__pf)()) : _M_f(__pf) {}
11   _Ret operator()(_Tp* __p) const { return (__p->*_M_f)(); }
12 private:
13   _Ret (_Tp::*_M_f)();
14 }; 
```

我们可以像下面这样使用mem_fun():

```
1 void draw_all(list<Shape*>& lsp){
2 For_each(lsp.begin(), lsp.end(), mem_fun(&Shape::draw));
3 }
```

mem_fun_t()和mem_fun_ref_t()函数族群共有16 = 2 ^ 4个函数，分别对应：

1）成员函数无参数 or 成员函数有一个参数

2）通过指针调用 or 通过引用调用

3）无返回值 or 有返回值

4）const成员函数 or non_const成员函数

#### 普通函数适配器

![img](http://pic002.cnblogs.com/images/2012/384029/2012042112031349.png)

与5.2类似，有时候用户需要给算法传递一个普通函数，这个时候，我们需要用到普通函数的适配器ptr_fun(),它有两个重载版本，一个有一个参数，另一个有两个参数。

适配器ptr_fun()返回一个函数对象

　　下面的代码是拥有一个参数的普通函数适配器的实现代码：

```cpp
 1 template <class _Arg, class _Result>
 2 class pointer_to_unary_function : public unary_function<_Arg, _Result> {
 3 protected:
 4   _Result (*_M_ptr)(_Arg);
 5 public:
 6   pointer_to_unary_function() {}
 7   explicit pointer_to_unary_function(_Result (*__x)(_Arg)) : _M_ptr(__x) {}
 8   _Result operator()(_Arg __x) const { return _M_ptr(__x); }
 9 };
10  
11 template <class _Arg, class _Result>
12 inline pointer_to_unary_function<_Arg, _Result>
13 ptr_fun(_Result (*__x)(_Arg))
14 {
15   return pointer_to_unary_function<_Arg, _Result>(__x);
16 }
```

　　我们可以看到，ptr_fun接受一个函数指针x，返回一个类pointer_to_unary_function的对象，该对象重载operator()操作符时调用x传递过来的函数。

 

　　在我们最上面提供的替换大于5的元素的实现中，我们可以像下面的方式这样调用bind1st：

```
#include <functional>
...
replace_if(vec.begin(), vec.end(), ptr_fun(bigthan5), 8);
```

#### 谓词否定迭代器　　

![img](http://pic002.cnblogs.com/images/2012/384029/2012042112035140.png) 

顾名思义，这个否定迭代器会接受任何一个返回值为bool类型的模板参数，这个模板参数可以是上面提到的任何返回值为bool的函数，也可以是用户自定义的返回值为bool的函数，或者返回值为bool的类成员函数。

对这个bool取反之后返回一个函数对象。

否定迭代器由not1和not2组成，它们分别有一个或两个模板参数。

下面是not2的实现代码：

```cpp
 1 template <class _Predicate>
 2 class binary_negate
 3   : public binary_function<typename _Predicate::first_argument_type,
 4                            typename _Predicate::second_argument_type,
 5                            bool> {
 6 protected:
 7   _Predicate _M_pred;
 8 public:
 9   explicit binary_negate(const _Predicate& __x) : _M_pred(__x) {}
10   bool operator()(const typename _Predicate::first_argument_type& __x,
11                   const typename _Predicate::second_argument_type& __y) const
12   {
13     return !_M_pred(__x, __y);
14   }
15 };
16  
17 template <class _Predicate>
18 inline binary_negate<_Predicate>
19 not2(const _Predicate& __pred)
20 {
21   return binary_negate<_Predicate>(__pred);
22 } 
```

在我们最上面提供的替换元素的实现中，我们可以像下面的方式这样调用bind1st：

```cpp
1 #include <functional>
2 ...
3 replace_if(vec.begin(), vec.end(), not1(ptr_fun(bigthan5)), 8);
```

上面的代码将所有小于等于5的元素替换为8。

#### 可适配(Adaptable)的关键

STL算法非常灵活的一个关键因素之一在于STL仿函数的可配接性(adaptability)，即函数可以被配接器修饰，彼此相积木一样地串接。为了拥有配接能力，每一个仿函数必须定义自己的相应型别(associate types)，就像迭代器如果要融入整个STL大家庭，也必须依照规定定义自己的5个相应型别一样。这样做是为了让配接器能够获得函数的一些特性。相应型别都只是一些 typedef，所有必要操作在编译期就就全部完成了，对程序的执行效率没有任何影响，不带来任何额外负担。

仿函数相应型别主要用来表示函数的参数型别和返回值型别，为了方便， `stl_function.h` 中定义了两个基类，分别是 `unary_function` 和 `binary_function`，分别表示一元函数和二元函数，其中都是一些型别的定义，仿函数只需要继承其中一个类，就可以拥有配接能力了

要使用STL内建的仿函数，必须包含<functional>头文件。

从实现来说，函数对象就是一个“行为类似函数”的对象，为了能够“行为类似函数”，其类别定义中必须自定义function call运算子(operator())。仿函数是为了STL能够实现更好的抽象性。仿函数主要用于STL中，当需要传入一个函数时使用。虽然通常来说我们可以直接传入一个函数指针，但是函数指针的抽象性不够强，同时函数指针也无法跟STL中其他组件搭配使用，所以仿函数还是有比较大的意义的。在STL中有很多仿函数，我们暂时不介绍它们。下面再看看仿函数配接器。

配接器是一种设计模式，它在原有类型的基础上扩展成为另外一个接口，使原本因为接口不兼容而不能合作的类型可以一起工作。仿函数的配接操作包括bind（将二元仿函数的第一或者第二个参数固定为某个值），negate（逻辑否定，将某个一元或者二元仿函数的返回值取反），compose(组合调用，用一个函数的返回值作为另外一个的参数执行调用)以及对一般函数（辅助函数是ptr_fun）或者成员函数(辅助函数是mem_fun, mem_fun_ref)的修饰，使之成为一个仿函数。需要特别注意的是，配接器的参数是仿函数，返回值跟仿函数的返回值一致，极大可能不是仿函数，所以配接器不能组合使用：adaptor1（adaptor2）。为了配合配接器工作，每个配接器都有一个辅助函数，用于生成配接器对象。比如bind1st是binder1st的辅助函数。平时我们基本都是用辅助函数的，所以，不要以为辅助函数是配接器。

## 函数适配器

顾名思义，函数适配器能够将函数(函数对象)做一定的转换，编程另一个功能大致的函数(函数对象)。就像生活中的电源适配器一样，将三口插座通过电源适配器变成两口插座方便使用。比如你想要计算数组 int a[] 中小于 4 的个数，那么你会用到 less<int>() 函数，但是如何体现要比较的 4 呢？这时候用函数适配器就能很好的解决。另外，STL 中绝大多数算法归根结底是调用功能类中重载的 operator() 运算符来实现的，然而功能类中还有很多普通的成员函数，STL 本身不能直接调用，需要通过函数适配器进行转换之后才能调用。 

顾名思义，函数适配器能够将函数(函数对象)做一定的转换，编程另一个功能大致的函数(函数对象)。就像生活中的电源适配器一样，将三口插座通过电源适配器变成两口插座方便使用。比如你想要计算数组 int a[] 中小于 4 的个数，那么你会用到 less<int>() 函数，但是如何体现要比较的 4 呢？这时候用函数适配器就能很好的解决。另外，STL 中绝大多数算法归根结底是调用功能类中重载的 operator() 运算符来实现的，然而功能类中还有很多普通的成员函数，STL 本身不能直接调用，需要通过函数适配器进行转换之后才能调用。 
C++98 函数适配器分类表:

![](http://img1.tuicool.com/QRRNZvr.png!web)

C++11 函数适配器表:

![](http://img2.tuicool.com/u6v6jiQ.png!web)

我们知道，在C++中，可调用实体主要包括函数，函数指针，函数引用，可以隐式转换为函数指定的对象，或者实现了opetator()的对象（即函数对象)。C++11中，新增加了一个std::function对象，std::function对象是对C++中现有的可调用实体的一种类型安全的包裹(我们知道像函数指针这类可调用实体，是类型不安全的)。 

## C++11的新玩法

有人可能会说，我已经有了自己实现的判断函数了，但是直接用又不行，有啥解决办法吗？ 

有人可能会说，我已经有了自己实现的判断函数了，但是直接用又不行，有啥解决办法吗？ 
其实是有的！（我也是最近才发现的）

C++11的标准中，提供了一套函数，能将一个普通的、不符合使用方要求的函数，转变成一个符合参数列表要求的 `functor` ，这实在是太酷了！

比如用户自己实现的 `int test(const std::string& str_vec, const size_t threshold)` 函数，如果能将第二个参数进行 **绑定** ，不就符合 `count_if`的要求了吗？

新标准的C++就提供了这样一个函数—— `bind` 。

通过 `std::bind` 以及 `std::placeholders` ，就可以实现转化，样例代码如下：

```cpp
  bool less_than_func(const std::string& str, const size_t threshold)
  {
      return str.length() < threshold;
  }

  //提供 _1 占位符
  using namespace std::placeholders;							  
  //绑定less_than_func第二个参数为5, 转化为functor
  auto less_than_functor = std::bind(less_than_func, _1, 5); 
  std::cout << std::count_if(str_vec.begin(), str_vec.end(), less_than_functor) << std::endl;
```

## 仿函数(functor)的应用

1）C语言使用**函数指针**和**回调函数**来实现仿函数，例如一个用来排序的函数可以这样使用仿函数

```c
#include <stdio.h>
#include <stdlib.h>
//int sort_function( const void *a, const void *b);
int sort_function( const void *a, const void *b)
{   
	return *(int*)a-*(int*)b;
}

int main()
{

   int list[5] = { 54, 21, 11, 67, 22 };
   qsort((void *)list, 5, sizeof(list[0]), sort_function);
    //起始地址，个数，元素大小，回调函数
   int  x;
   for (x = 0; x < 5; x++)
		  printf("%i\n", list[x]);

   return 0;
}
```

如果不使用仿函数，则需要在每个使用qsort的地方，都重写一遍sort_function的内容。

这里也就是解释了一些关于仿函数和回调函数的联系，C语言当中的仿函数使用是通过回调函数实现的。

再举一个仿函数的例子

```cpp
#include<vector>
#include<iostream>
#include<algorithm>

class Employee
{
     public:
           int DoSomething()
           {
               std::cout<<"do something"<<std::endl;
               return 0;
           }
};


int GiveDoSomething(Employee& e)
{
    return e.DoSomething();
}


int main()
{
    Employee *employee = new Employee();

    std::vector<Employee> emps;
    emps.push_back(*employee);

    std::for_each(emps.begin(),emps.end(),&GiveDoSomething);

    return 0;
}
```

如果不使用仿函数，则需要在for_each当中直接调用，每个需要使用Employee类中DoSomething方法的地方都要调用，这样做就简化了编程。

当然这里不使用仿函数也是可以的。

```cpp
#include<vector>
#include<iostream>
#include<algorithm>

class Employee
{
     public:
           int DoSomething()
           {
               std::cout<<"do something"<<std::endl;
               return 0;
           }
};


int main()
{
    Employee *employee = new Employee();

    std::vector<Employee*> emps;
    emps.push_back(employee);

    std::for_each(emps.begin(),emps.end(),std::mem_fun(&Employee::DoSomething));

    return 0;
}
//在vector当中保存的是对象指针的时候可以使用mem_fun函数
```

思想上其实也是回调思想。

 2）在C++里，我们通过在一个**类中重载括号运算符的方法使用一个函数对象**而不是一个普通函数。

```cpp
#include <iostream>
#include <algorithm>

using namespace std;
template<typename T>
class display
{
public:
	void operator()(const T &x)
	{
		cout<<x<<" ";
	}
};


int main()
{
	int ia[]={1,2,3,4,5};
	for_each(ia,ia+5,display<int>());

	return 0;
}
```

普通的其实创建了一个临时无名的对象，之后调用其重载函数()。

其实创建了一个临时无名的对象，之后调用其重载函数()。
但是STL中的仿函数不是这个样子的，为了使仿函数拥有被函数配接其（function adapter）修饰，彼此像积木一样地串接。仿函数应该定义自己相应的类别（associative types）。就像迭代器一样，为了融入STL，必须定义自己的5个相应类别。这样做的目的是为了让配接器能够取出，获得反函数的某些信息。相应类别都是一下typedef定义的预处理，在编译时就完成了替代，对程序执行效率没有影响，也不带来任何额外的负担。**仿函数相应的类别主要用来表现函数参数类型和返回值类型。**



**接下来看看三个等价的例子：**

```cpp
#include<iostream>
#include<list>
#include<algorithm>
#include<set>
#include<string>
#include<vector>
using namespace std;
class Person{
public:
  Person(){}
  Person(string name,int age){
    this->name=name;
    this->age=age;
  }
  void show(){
    cout<<name<<" "<<age<<endl;
  }
  bool operator()(Person &p1,Person &p2){
    return p1.name<p2.name||(!(p1.name>p2.name))&&(p1.age<p2.age);
  
  }
private:
  string name;
  int age;
};

int main(){
  vector<Person>v;
  Person p1("zhang",22);
  Person p2("li",20);
  Person p3("tan",23);
  Person p4("lai",22);
  Person p5("huang",22);
  Person p6("huang",21);
  Person p7("zhang",23);
  Person p8("zhang",17);

  v.push_back(p1);
  v.push_back(p2);
  v.push_back(p3);
  v.push_back(p4);
  v.push_back(p5);
  v.push_back(p6);
  v.push_back(p7);
  v.push_back(p8);

  for(vector<Person>::iterator itera=v.begin();itera!=v.end();++itera){
    (*itera).show();
  }
  cout<<"*****************************"<<endl;
  sort(v.begin(),v.end(),Person());
  for(vector<Person>::iterator itera=v.begin();itera!=v.end();++itera){
    (*itera).show();
  }
  system("pause");
  return 0;
}
```

```cpp
#include<iostream>
#include<list>
#include<algorithm>
#include<set>
#include<string>
#include<vector>
using namespace std;
class Person{
public:
  Person(){}
  Person(string name,int age){
    this->name=name;
    this->age=age;
  }
  void show(){
    cout<<name<<" "<<age<<endl;
  }
  
public:
  string name;
  int age;
};
bool bijiao(Person &p1,Person &p2){
  return p1.name<p2.name||(!(p1.name>p2.name))&&(p1.age<p2.age);
  
}
int main(){
  vector<Person>v;
  Person p1("zhang",22);
  Person p2("li",20);
  Person p3("tan",23);
  Person p4("lai",22);
  Person p5("huang",22);
  Person p6("huang",21);
  Person p7("zhang",23);
  Person p8("zhang",17);

  v.push_back(p1);
  v.push_back(p2);
  v.push_back(p3);
  v.push_back(p4);
  v.push_back(p5);
  v.push_back(p6);
  v.push_back(p7);
  v.push_back(p8);

  for(vector<Person>::iterator itera=v.begin();itera!=v.end();++itera){
    (*itera).show();
  }
  cout<<"*****************************"<<endl;
  sort(v.begin(),v.end(),bijiao);
  for(vector<Person>::iterator itera=v.begin();itera!=v.end();++itera){
    (*itera).show();
  }
  system("pause");
  return 0;
}
```

```cpp
#include<iostream>
#include<list>
#include<algorithm>
#include<set>
#include<string>
#include<vector>
using namespace std;
class Person{
public:
  Person(){}
  Person(string name,int age){
    this->name=name;
    this->age=age;
  }
  void show(){
    cout<<name<<" "<<age<<endl;
  }
  
public:
  string name;
  int age;
};
class BiJiao{
public:
  bool operator()(const Person &p1,const Person &p2){
    return p1.name<p2.name||(!(p1.name>p2.name))&&(p1.age<p2.age);
  
  }
};
int main(){
  set<Person,BiJiao>v;
  Person p1("zhang",22);
  Person p2("li",20);
  Person p3("tan",23);
  Person p4("lai",22);
  Person p5("huang",22);
  Person p6("huang",21);
  Person p7("zhang",23);
  Person p8("zhang",17);

  v.insert(p1);
  v.insert(p2);
  v.insert(p3);
  v.insert(p4);
  v.insert(p5);
  v.insert(p6);
  v.insert(p7);
  v.insert(p8);

  for(set<Person,BiJiao>::iterator itera=v.begin();itera!=v.end();++itera){
    (*itera).show();
  }
  cout<<"*****************************"<<endl;
  
  for(set<Person,BiJiao>::iterator itera=v.begin();itera!=v.end();++itera){
    (*itera).show();
  }
  system("pause");
  return 0;
}
```

**三个结果都一样：**

![img](http://img2.tuicool.com/R32yAb.jpg!web)

## **总结**

函数对象在STL的算法部分占有很重要的作用，STL中基本上所有的算法都像__pred(*__first)或__binary_op(*__first1, *__first2)这样对迭代器指向的元素进行操作，这就需要用户正确地传递__pred和__binary_op给算法。本节中介绍的函数对象不论在封装性还是性能上都完全胜任这个任务。

对函数对象的几种类型有明确的理解，并辅之以相关的练习，定会掌握它。

**参考书目**

[1] 《C++程序设计语言》 Bjarne Stroustrip

[2] SGI STL-3.3源代码