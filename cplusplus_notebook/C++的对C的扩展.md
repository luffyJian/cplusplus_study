# 1.函数重载

## 1.1 重载规则

1. 函数名相同；
2. 参数个数不同，参数的类型不同，参数的顺序不同；
3. 返回值类型不能作为重载的标准。

## 1.2 匹配规则

1. 严格匹配，找到则调用。
2. 通过隐式转换寻求一个匹配，找到则调用。

## 1.3 重载实现机制

​    C++利用Name Mangling（命名倾轧）技术，改变函数名，来区分参数不同的同名函数。

## 1.4 extern “C”

​    Name Mangling依据函数声明来进行倾轧。若声明被倾轧，则调用为倾轧版本，若声明为非倾轧版本，则调用也为非倾轧版本。

​    C++默认所有函数倾轧。若有特殊函数不参与倾轧，则需要extern "C"来进行声明。

- mystring.h

```cpp
extern "C" int myString(char *str);
```

- mystring.cpp

```cpp
extern "C"{
int myString(char *str)
{
    int len = 0;
    while(*str++)
        len++;
    return len;
}
}
```

​    C++完全兼容C语言，那么就面临着，完全兼容C的类库。由.c文件的生成的库文件中函数名，并没有发生name mangling行为，而我们在包含.c文件所对应的.h文件时，.h文件要发生name mangling行为，因而会在链接的时候发生错误。

​    C++为了避免上述错误的发生，重载了关键字extern。只需要在避免name mangling的函数前，加extern "C"，如果有多个，则extern "C"{}将函数的声明放入{}即可。

## 1.5 默认参数

​    通常情况下，函数在调用时，形参从实参那里取得值。C++给出了可以不用从实参取值的方法，给形参设置默认值，称为默认参数。

​    **默认规则：**

1. 默认的顺序，是从右往左，不能跳跃；
2. 函数声明和定义在一起时，默认参数在定义（声明）处。声明在前，定义在后，默认参数只能在声明处；
3. 默认值可以是常量，全局变量，或是一个函数；
4. 实参个数+默认参数个数>=形参个数。

​    同时使用默认参数和重载可能两者会产生规则冲突（ambiguous），当两者要实现同样的功能时，可以考虑优先选用默认参数。

# 2. 引用

## 2.1 定义

​    变量名，本身是一段内存的引用，即别名。此处引入的引用，是为已有变量起一个别名。

​    声明如下：

```cpp
int a;
int &ra = a;
```

## 2.2 规则

1. 引用，是一种关系型声明，而非定义。不能独立存在，必须初始化，且与原类型保持一致，且不分配内存；
2. 声明关系，一经声明，不可变更；
3. 可对引用，再次引用，多次引用的结果，是某一个变量具有多个别名，多个别名间是平行关系。
4. 辨别引用与其他，&符号前有数据类型时，是引用。

## 2.2 应用

​    C++很少使用独立变量的引用，如果使用某一个变量，就直接使用它的原名，没有必要使用它的别名。

​    引用的真正目的，就是取代指针传参。C++引入引用后，可以用引用解决的问题，避免使用指针来解决。

​    引用，从宏观上可以理解为，扩展了变量的作用域，传参后，就像在本地解决问题一样。避免了传n级指针，解决n-1级指针的问题，即平级内解决问题。

## 2.3 指针的引用-有，引用的指针-无

​    引用的本质是指针，C++对裸露的内存地址（指针）做了一次包装。又取得了指针的优良特性。所以再对引用取地址，建立引用的指针没有意义。

```cpp
#include <iostream>
using namespace std;

void Swap(char* &p, char* &q)
{
    char *t = p;
    p = q;
    q = t;
}

int main()
{
    char *m = "China";
    char *n = "America";

    cout<<"m->"<<m<<endl;
    cout<<"n->"<<n<<endl;

    Swap(m,n);

    cout<<"m->"<<m<<endl;
    cout<<"n->"<<n<<endl;

    return 0;
}
```

## 2.4 指针的指针-有，引用的引用-无

## 2.5 指针 数组-有，引用 数组-无

​    数组名，本身是首元素的地址，若首元素是引用的话，数组名就成为了引用的指针，与之前所提的不符，即不存在引用的指针。

## 2.6 数组的引用

​    数组名，本质是一个常量指针，对于指针是可以取引用的，所以数组的引用是存在的。

```cpp
int array[5] = {1,2,3,4,5};
cout<<"sizeof(array) = "<<sizeof(array)<<endl;

int * const & pr = array;
cout<<"sizeof(pr) = "<<sizeof(pr)<<endl;

int (&ra)[5] = array;
cout<<"sizeof(ra) = "<<sizeof(ra)<<endl;


sizeof(array) = 20
sizeof(pr) = 4
sizeof(ra) = 20
```

## 2.7 常引用

​    const修饰的变量，有着变量的形式，常量的作用，常用于取代#define定义的宏常量。

​    const对象，只能声明为const引用，使其语义保持一致性。non-const对象，既可以声明为const引用，也可以声明为non-const引用。声明为const引用，则不可以通过const引用修改数据。

```cpp
const int a = 10;
//int &ra = a; error!
const int &ra = a;

int b = 20;
int &rb = b;
const int &rbb = b;
```

​    **临时变量的引用**，临时变量，通常理解为不可以去地址的对象，如，常量，表达式，函数返回值，类型不同的变量

```cpp
#include <iostream>
using namespace std;

int foo()
{
    int a = 100;
    return a;
}

int main()
{
    //常量
    const int &cc = 55;
    cout<<cc<<endl;
    //表达式
    int a = 3; int b = 5;
    const int &ret = a+b;
    cout<<ret<<endl;
    //函数返回值
    const int &ra = foo();
    cout<<ra<<endl;
    //类型不同的变量
    double d = 100.12;
    const int &rd = d;
    cout<<rd<<endl;
    return 0;
}
```

​    本质上const引用，引用了一个不可改变的临时变量。const int tmp = data; const int &rd = tmp；此时，我们修改data的值，临时变量的值也并没有发生改变。

​    const type &与type & const这两个修饰方式，哪个是正确的，背后有什么设计的用以？

​    可以参考const修饰指针的用法，int * const p，表示p不可以更改指向，而引用，本省一经声明，即不可更改指向，故不存在这种修饰方法。

​    在任何可以使用const的地方，尽量使用const。可以使你的程序更健壮。

​    引用的本质是const类型的指针，即type * const p。其功能类似指针，看起来又不是指针，是对指针的封装。

# 3.堆内存操作

## 3.1 申请空间

```cpp
int *p = new int(10); //初始化为10
int **pp = new int *;

double *d = new double[10]{1,2,3,5}; //申请十个空间，顺便初始化

int(*a)[5] = new int[3][5];
```

## 3.2 释放空间

```cpp
delete p;     //对于申请单变量空间
delete [] pp; //对于一维或多维数组空间
```

## 3.3 注意事项

1. new/delete 是关键字，效率高于malloc和free。
2. 配对使用，避免内存泄漏和多重释放。
3. 避免，交叉使用。比如malloc申请的空间去delete，new出的空间被free。

# 4.内联函数

​    C语言中有宏函数的概念。宏函数的特点是内嵌到调用代码中去，避免了函数调用的开销。但是由于宏函数的处理发生在预处理阶段，缺失了语法检测和又可能带来的语意差错。

|      | 宏函数                                                       | 普通函数                         |
| ---- | ------------------------------------------------------------ | -------------------------------- |
| 优点 | 内嵌代码，避免压栈与出栈的开销。                             | 高度抽象，避免重复开发，类型检查 |
| 缺点 | 代码替换，易使生成代码体积变大，易产生逻辑错误，无类型检查。 | 压栈与出栈，带来开销             |

​    内联函数能够让函数，既有宏函数的快速也有普通函数的优点，内联函数的生成，只需要在普通函数的前面加inline关键字即可。

```cpp
inline int sqr(int x)
{
    return x*x;
}
```

| 优点     | 避免调用时的额外开销（入栈与出栈操作）                       |
| -------- | ------------------------------------------------------------ |
| 代价     | 由于内联函数的函数体在代码段中会出现多个“副本”，因此会增加代码段的空间。 |
| 本质     | 以牺牲代码段空间为代价，提高程序的运行时间的效率             |
| 适用场景 | 函数体很“小”，且被“频繁”调用。                               |

# 5.类型强转

​    使用C风格的强制转换可以把想要的任何东西转换成合乎心意的类型，为什么还需要一个新的C++类型的强制转换呢？

​    新类型的强制转换可以提供更好的控制强制转换过程，允许控制各种不同类型的强制转换。C++提供了四种转化，static_cast，reinterpret_cast，dynamic_cast，const_cast以满足不同需求，C++风格的强制转换好处是，它们能更清晰的表明它们要干什么。

​    C语言转换风格，在C++中依然适用。

|     C style      |         C++ style          |
| :--------------: | :------------------------: |
| (type)expression | xxx_cast<type>(expression) |

## 5.1 static_cast

| 语法格式 | static_cast<type>(expression)                                |
| :------: | ------------------------------------------------------------ |
| 适用场景 | 在一个方向上可以做隐式转换，在另一个方向上就可以做静态转换。双向可隐式的自然不需要强制转换。 |

```cpp
//双向隐式
int a = 3;
float b = 5.5;
    
a = static_cast<int>(b);
b = static_cast<float>(a);
    
int x = 10; int y = 3;
float z = static_cast<float>(x)/y;
cout<<z<<endl;
    
//单向隐式
void *p; int *q;
//q = p;  error!
p = q;
q = static_cast<int*>(p);
char *pp = static_cast<char*>(malloc(100));
```

## 5.2 reinterpret_cast

| 语法格式 | reinterpret_cast<type>(expression)                           |
| -------- | ------------------------------------------------------------ |
| 适用场景 | “通常为操作数的位模式提供较低层的重新解释”也就是说将数据以二进制存在形式的重新解释，在双方向上都不可以隐式类型转换的，则需要重解释类型转换。 |

```cpp
    int *p; float *q;
    //p = q;  error!
    //q = p;  error!
    //p = static_cast<int*>(q);  error!
    //q = static_cast<float*>(p); error!

    p = reinterpret_cast<int*>(q);
    q = reinterpret_cast<float*>(p);

    int data = 0x12345678;
    printf("%x\n",*reinterpret_cast<char*>(&data));
    printf("%x\n",*reinterpret_cast<short*>(&data));
    printf("%x\n",*reinterpret_cast<int*>(&data));

    int arr[5] = {1,2,3,4,5};
    printf("%x\n",*(int*)((int)arr+1));
```

## 5.3 const_cast

| 语法格式 | const_cast<type>(expression)                                 |
| -------- | ------------------------------------------------------------ |
| 适用场景 | 用来移除非const对象的引用或指针的常量性（cast away the constness）使用const_cast去除const对于引用和指针的限定，通常是为了函数能够接受参数或返回值。 			 			非const对象--->const引用或指针--->脱const--->修改非const对象 |
| 评价     | 被const引用后的，反作用                                      |
| 注意     | 目标类型只能是**指针或引用**                                 |

```cpp
#include <iostream>
using namespace std;

void foo(const int &a)
{
    const_cast<int&>(a) = 200;
}

int main()
{
    int a;
    const int &ra = a;
    a=100;
    cout<<a<<endl;
    const_cast<int&>(ra) = 300;
    cout<<ra<<endl;
    cout<<a<<endl;

    const int *p = &a;
    *const_cast<int*>(p) = 400;
    cout<<*p<<endl;
    cout<<a<<endl;

    foo(a);
    cout<<a<<endl;

    return 0;
}
```

const对象不可以去const化。原生数据是非const的，可以去除其引用的const的属性，若原生数据是const的，去除其引用的const属性，执行任何写入操作都是未定义的。

> const_cast is only safe if you are adding const to an originally non-const variable.
>
> Trying to remove the const status from an originally-const object, and then perform the write operation on it will result in undefined behavior.

## 5.4 dynamic_cast

​    用于多态中的父子类之间的强制转化。

# 6.命名空间

​    命名空间为了大型项目开发，而引入的一种避免命名冲突的一种机制。

## 6.1全局无名命名空间

​    C++在C语言的基础之上，C++首次以语法的形式，确立了全局无命名空间的存在和使用方法；

```cpp
#include <iostream>

using namespace std;

int var = 100;

void func()
{
    cout<<"func"<<endl;
}
int main()
{
    int var = 200;
    cout<<"main var = "<<var<<endl;
    cout<<"global var = "<<::var<<endl;

    func();
    ::func();

    return 0;
}
```

## 6.2 namespace定义

​    语法C++引入namespace的本质是对全局命名空间再次划分。确切地说，是对全局的函数和变量再次进行作用域打包。

​    命名空间的声明及namespace中可以包含的内容：

```cpp
namespace spacename{

    global variable;
    function;
    struct type
    other namespace
}
```

## 6.3 命名空间的使用

​    常见的命名空间有三种层次的语法，示例如下：假设有命名空间namespace sn{};

| 方法                        | 举例                              | 备注                                                         |
| :-------------------------- | --------------------------------- | ------------------------------------------------------------ |
| sn::member;                 | sn::a = 5; 			sn::foo(); | 直接使用命名空间成员，不会产生命名冲突                       |
| using sn::member;           | using sn::a;  using sn::foo();    | 命名空间成员，融入该语句所在的命名空间，会产生命名冲突       |
| using namespace sn;         | using namespace std;              | 将命名空间（即全体成员），融入该语句所在的命名空间。会产生命名冲突 |
| using namespace one::other; | using namespace one::other;       | 命名空间支持嵌套                                             |

## 6.4 同名空间

​    当项目组中有多个人共同开发的时候，每个人可能负责几个模块或是几个文件，但是要求是相同的命名空间，该如何实现？

​    同名命名空间可以合并，是协同开发的基础。