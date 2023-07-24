# 1. More Concise

## 1.1  `nullptr`

​	使用`nullptr`，不会有重载函数调用模糊问题，`nullptr`不属于广义整型，也不是普通意义上的指针，它实际类型是`std::nullptr_t`，它能够隐式的转换成所有的原始指针类型，故可**视其为一个可以指向所有类型的指针**。

## 1.2 `override`

​	覆写父类的虚函数时候，有些IDE可能会给出斜体等的提示，表示此函数覆写自父类。若是在文本编辑器中，写错了，参数类型不对或个数不对，但编译没问题，运行时候却不被调用，`override`可以辅助检查是否继承了想要虚继承的函数。

​	关键字`override`指明此种覆写关系，若关系不成立，则会报错的形式提示给用户。

## 1.3 `final`

​	关键字`final`有两个用途：

- 它阻止了从类继承；
- 阻止了一个虚函数的覆写。

用来阻止类的无限扩展。

```c++
#include <iostream>
using namespace std;
class A //final
{
public:
    virtual void func() {}
};
class B : public A
{
public:
    virtual void func() override final {}
};
int main()
{
    return 0;
}

```

## 1.4 `default`

​	C++的类有四类特殊成员函数，分别是：默认构造函数、析构函数、拷贝构造函数以及拷贝赋值运算符。

​	这些成员函数负责创建、初始化、销毁，或者拷贝类对象。

​	如果程序员没有显示地为一个类定义某个特殊成员函数，而又需要用到该特殊成员函数时，使用`default`编译器会隐式的为此类生成一个默认的特殊成员函数。

```c++
#include <iostream>

using namespace std;

class A
{
public:
    A() = default;
//    ~A(){}
//    A(const A &){}
//    A& operator=(const A &){}
    A(int a)
    {

    }
};

int main()
{
    A a;
    return 0;
}
```

## 1.5 `delete`

​	显示的禁用某个函数。

```c++
#include <iostream>

using namespace std;

class Singletion
{
public:
    static Singletion * getInstance()
    {
        if(_ins == nullptr)
            _ins = new Singletion;
        return _ins;
    }
    ~Singletion()= delete;
    Singletion(const Singletion &)= delete;
private:
    Singletion()= default;
    static Singletion * _ins;
};

Singletion * Singletion::_ins = nullptr;

int main()
{
    Singletion * ps = Singletion::getInstance();
    
    return 0;
}
```

## 1.6 委托构造

```c++
#include <iostream>

using namespace std;

struct A {
    bool a_;
    char b_;
    int c_;
    float d_;
    double e_;
    A(bool a, char b, int c, float d, double e)
        : a_(a), b_(b), c_(c), d_(d), e_(e) {}
    //construct reuse
    A (int c): A(true, 'b', c, 1.1, 1000.1) {}
    A (double e): A (false, 'a', 0, 0.1, e) {}
};
int main(){
    A o1(10);
    cout <<"a:"<<o1.a_
         <<", b:" <<o1.b_
         <<", c: " <<o1.c_
         <<", d: "<< o1.d_
         <<", e: "<< o1.e_
         <<endl;
    A o2(5.5);
    cout <<"a:"<<o2.a_
         <<", b:" <<o2.b_
         <<", c: " <<o2.c_
         <<", d: "<< o2.d_
         <<", e: "<< o2.e_
         <<endl;
    return 0;
}
```

## 1.7 原生字符串

​	C++提供了`R"()"`，原生字符串，即字符串中无转义，亦无需反转义。但是注意（）中`)"`会导致提前结束。

```c++
#include <iostream>

using namespace std;

#define path "C:\Program Files (x86)\Tencen\QzoneMusic\QQMusicAddin"
#define path2 "C:\\Program Files (x86)\\Tencen\\QzoneMusic\\QQMusicAddin"
#define path3 R"(C:\Program Files (x86)\Tencen\QzoneMusic\QQMusicAddin)"

int main(){
    cout<<path<<endl;
    cout<<path2<<endl;
    cout<<path3<<endl;
    return 0;
}
```

## 1.8 区间迭代

```c++
#include <iostream>

using namespace std;

int main(){
    int arr[10] = {1,2,3,4,5,6,7,8,9,10};

    for(int i=0; i<sizeof(arr)/sizeof(*arr); i++)
        cout<<arr[i]<<" ";
    cout<<endl;
    for(auto & i:arr)
        cout<<i<<" ";
    cout<<endl;

//    char *p = "china";
//    for(auto & ch:p)    //error!
//        cout<<ch<<endl;
    string str = "china";
    for(auto &ch:str)
        cout<<ch<<endl;
    return 0;
}
```

​	C++为for提供了for range的用法。对于STL的遍历带来了极大的书写便利。

​	（range for）语句遍历给定序列中的每个元素并对序列中的每个值执行某种操作，其语法形式如下

```c++
for (declaration : expression)
    statement</code>
```

​	expression部分是一个对象，必须是一个序列，比方说用花括号括起来的初始值列表、数组或者vector或string等类型的对象。**这些类型的共同特点是拥有能返回迭代器的begin和and成员**。

​	declaration部分负责定义一个变量，该变量将被用于访问序列中的基础元素。每次迭代，declaration部分的变量会被初始化为expression部分的下一个元素值。**确保类型相容最简单的办法是使用auto类型说明符**。

## 1.9 初始化列表

常规初始化

```c++
#include <iostream>
#include <vector>
#include <list>
#include <map>
using namespace std;

int main(){
    int arr[] = {1,2,3,4,5,6,7,8,9,10};
//    vector<int> vi;
//    vector<int> vi(10);
//    vector<int> vi(10,101);
    vector<int> vi(arr,arr+10);

    for(auto & i:vi)
        cout<<i<<endl;

//    list<int> li;
//    list<int> li(10);
//    list<int> li(10,101);
    list<int> li(arr,arr+10);

    map<int,string> mis;
    mis[0] = "first";
    mis[1] = "second";
    mis[2] = "third";
    map<int,string> mis2(mis.begin(),mis.end());
    mis.insert(pair<int,string>(3,"fourth"));
    mis.insert(map<int,string>::value_type(3,"fourth"));

    for(auto & pair:mis)
        cout<<pair.first<<":"<<pair.second<<endl;
    return 0;
}
```

统一的初始化风格

```c++
#include <iostream>
#include <vector>
#include <list>
#include <map>
using namespace std;

int main(){
//    int i(10);
//    int ii({10});
//    int iii{10};
    int i = 10;
    int arr[] = {1,2,3,4,5,6,7,8,9,10};
    vector<int> vi = {1,2,3,4,5,6,7,8,9,10};
    list<int> li = {1,2,3,4,5,6,7,8,9,10};
    map<int,string> mis = {
        {0,"first"},
        {1,"second"},
        {2,"third"},
        {3,"fourth"}
    };

    for(auto & pair:mis)
        cout<<pair.first<<":"<<pair.second<<endl;
    return 0;
}
```

​	在C++11中提供了新的模板类型`initializer_list`，它的对象只能用大括号{}初始化，其内的元素均是const的。常用于构造器和普通函数传参。

```c++
#include <iostream>
#include <initializer_list>
using namespace std;

int sum(const initializer_list<int> &il)
{
    int s = 0;
    for(auto & i:il)
        s += i;
    return s;
}
int average(const initializer_list<int> &il)
{
    int s = 0;
    for(auto &i :il)
        s+=i;
    return s/il.size();
}

int main(){
    int s = sum({1,2,3,4});
    int av = average({1,2,3,4});
    cout<<"sum "<<s<<" av "<<av<<endl;
    return 0;
}
```

```c++
#include <iostream>
#include <vector>
#include <initializer_list>
using namespace std;

template<class T>
class MyArr
{
public:
    MyArr() = default;
    MyArr(const initializer_list<T> & il)
    {
        for(auto & i:il)
            vt.push_back(i);
    }
protected:
    vector<T> vt;
};

int main(){
    MyArr<int> ma;
    MyArr<int> ma2 = {1,2,3,4};
    return 0;
}
```

## 1.10 自动类型推导 `auto`

​	`auto`在C++中只是一个占位符，能够实现类型的自我推导，并不代表一个实际的类型声明。

​	`auto`声明的变量，<font color=red>必须马上初始化</font>，来让编译器推断出它的实际类型，并在编译时将auto占位符替换为真正的类型。

​	<font color=cornflowerblue>注意：auto不能用于函数参数（仅能C++14可以通过）</font>

​	**推导不出修饰**  如const。

```c++
const int m = 100; //m=10;
auto n = m; n = 10;
```

## 1.11 类型推导 `decltype`

​	`auto`类型，作为占位符，必须初始化，即auto所代表的类型，必须定义变量。

​	若，仅希望得到类型，而不产生变量，则需要`decltype`

```c++
decltype(expression)
```

expression代表被推导的表达式。

```C++
#include <iostream>
using namespace std;

void func()
{
    cout<<"hello world"<<endl;
}

typedef void (*FUNC)();

int main(){
    int i = 10;
    decltype(i) ii;
    cout<<sizeof(i)<<":"<<sizeof(ii)<<endl;

    decltype("china") ds = "abcde"; //少于5个字符会报错  ---> const char[5]
    cout<<sizeof(ds)<<endl;

    decltype(string("china")) s;
    cout<<sizeof(string)<<":"<<sizeof(s)<<endl;

    char *p = "china";
    decltype(p) ps;
    cout<<sizeof(p)<<":"<<sizeof(ps)<<endl;

    FUNC fu;
    decltype(fu) f = func;
    f();
    return 0;
}
```

​	上例中，关于函数的类型推导，不可以直接使用函数名字，可以使用函数的类型生成的对象推导。

​	`decltype(expr)`所推导出来的类型，完全与expr类型一致。同auto一样，在编译期间完成，并不会真正计算表达式的值，即上面的推导并不会导致函数打印。

​	**decltype不可以对类型推导**

------

`decltype + typedef`

```c++
#include <iostream>
#include <map>
using namespace std;

typedef decltype(map<int,string>::value_type(1,"china")) mypair;

int main(){
    map<int,string> mis;
    mis.insert(map<int,string>::value_type(1,"china"));
    mis.insert(mypair(2,"america"));
    for(auto & item:mis)
        cout<<item.first<<":"<<item.second<<endl;
    return 0;
}

```

------

`decltype + auto`

`->decltype()`放在函数的结尾，称为尾推导。返回值类型用`auto`来配合使用，是一种绝佳组合。

```c++
#include <iostream>
using namespace std;

template<class T1, class T2>
auto sum(T1 a, T2 b)->decltype(a+b)
{
    return a+b;
}

int main(){
    int i = 100;
    double d = 100.1;
    auto ret = sum(i,d);
    cout<<ret<<endl;
    return 0;
}
```

## 1.12 仿函数

​	重载了`operator()`的类的对象，在使用中，语法类似于函数，故称其为仿函数。此种用法优于常见的函数回调。

​	相对于函数，仿函数，可以拥有初始状态，一般通过class定义的私有成员，并在声明对象的时候，进行初始化。

```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

class Compare{
public:
    Compare(bool f = true):flag(f){}
    int operator()(int x, int y)
    {
        if(flag)
            return x>y;
        else
            return x<y;
    }
private:
    bool flag;
};

int main(){
    vector<int> vi = {1,3,5,7,9,2,4,6,8,10};
    sort(vi.begin(),vi.end(),Compare());
    for(auto &i:vi)
        cout<<i<<endl;
    sort(vi.begin(),vi.end(),Compare(false));
    for(auto &i:vi)
        cout<<i<<endl;
    return 0;
}
```

## 1.13 Lambda

​	匿名函数（无函数名，只有函数体），就地书写，调用，即Lambda存在的意义，常用于取代作回调用的简短函数指针与仿函数。

### 1.13.1 格式

```C++
[capturelist](parameterlist) mutable -> return type{function body}
```

- `capturelist`：捕获列表，总是出现在Lambda函数开始处。事实上[]是Lambda的引用符。即编译器根据引用符来判断接下来的代码是否是Lambda函数。
- `parameterlist`：参数列表。与普通函数的参数列表一致，若不需要传递参数，可以连同()一起省略。
- `mutable`：默认情况下，Lambda函数总是一个const函数，mutable可以取消其常量性，所谓常量性是指不可修改capturelist中的东西。在使用该修饰符时，参数列表不可省略（即使参数为空）。
- `->return type`：返回类型，不需要返回值的时候可以连同->一起省略。此外在返回类型明确的情况下，也可以省略该部分，编译器可以自行推导。
- `function body`：函数体，内容与普通函数一致，不过除了可以使用参数之外，还可以使用所有捕获的变量。

```c++
#include <iostream>
using namespace std;

int main()
{
    auto foo = []{return 1+2;};
    cout<<foo()<<endl;
    cout<<[]{return 1+2;}()<<endl;
    return 0;
}
```

```c++
#include <iostream>
using namespace std;

int main()
{
    auto f = [](int x = 1, int y = 2){return x+y;};
    cout<<f(4,9)<<endl;
    cout<<[](int x = 1, int y = 2){return x+y;}(5,6)<<endl;
    return 0;
}
```

```c++
#include <iostream>
using namespace std;

int main()
{
    auto f = [](int x = 1, int y = 2)->int{return x+y;};
    cout<<f(4,9)<<endl;
    cout<<[](int x = 1, int y = 2)->int{return x+y;}(5,6)<<endl;
    return 0;
}

```

### 1.13.2 闭包[]与mutable

​	闭包（closure）是一个函数和它所引用的非本地变量的上下文环境的集合。从定义我们可以知道，闭包可以访问在它定义范围之外的变量。其最重要的应用就是回调函数。

​	Lambda函数能够捕获Lambda函数外的具有自动存储时期的变量。函数体与这些变量的集合合起来叫闭包。

​	闭包的概念在Lambda中通过[]来体现。

**捕获规则**：

| 形式       | 语意                                                         |
| ---------- | ------------------------------------------------------------ |
| `[]`       | 不截取任何变量                                               |
| `[bar]`    | 仅对外部变量bar值传递在函数体中使用。                        |
| `[&bar]`   | 仅对外部变量bar引用传递在函数体中使用。                      |
| `[x,&y]]`  | 仅x按值传递，y按引用传递在函数体中使用。                     |
| `[&]`      | 截取外部作用域中所有变量，并作为引用传递在函数体中使用。     |
| `[=]`      | 截取外部作用域中所有变量，并按值传递在函数体中使用。         |
| `[=,&foo]` | 截取外部作用域中的所有变量，并按值传递在函数体中使用，但是对foo变量使用引用传递。 |
| `[&,=foo]` | 截取外部作用域中的所有变量，并作为引用传递在函数体中使用，但是对foo变量按值传递 |

​	上述过程中，涉及到值传递要发生拷贝行为，而引用传递则不会发生拷贝行为。捕获列表中不允许重复，如`[=,a]`

```c++
#include <iostream>
using namespace std;

auto adder(){
    auto sum = 0;
    return [=](int value)mutable{
        sum+=value;
        return sum;
    };
}

auto fibonacii(){
    auto a = 0, b = 1, t = 0;
    return [=]()mutable{
        t = a;
        a = b;
        b = t + b;
        return a;
    };
}
int main()
{
    auto f = adder();
    for(int i=0; i<101; i++)
        cout<<f(i)<<endl;

    auto f2 = fibonacii();
    for(int i=0; i<10; i++)
        cout<<f2()<<endl;
    return 0;
}
```

### 1.13.3 本质

​	Lambda即匿名对象，闭包的本质，初始化Lambda对象。

​	Lambda的本质是匿名的仿函数！

## 1.14 enum class

​	枚举体的声明和定义使用enum class或是enum struct，二者等价。使用enum class/enum struct不会与现存的enum关键词冲突。且具有更好的类型安全和类似封装的特性。

### 1.14.1 类内的枚举

```c++
#include <iostream>
using namespace std;

class Painter
{
public:
    enum Color{
        red, green, yellow
    };
public:
    Painter()=default;
    void dis(Color c)
    {
        cout<<c<<endl;
    }
};

int main()
{
    Painter p;
    p.dis(Painter::yellow);
    return 0;
}
```

### 1.14.2 待作用域的枚举

```c++
#include <iostream>
using namespace std;

enum class Color{
    red, green, yellow
};

class Painter
{
public:
    Painter()=default;
    void dis(Color c)
    {
        cout<<static_cast<int>(c)<<endl;
    }
};

int main()
{
    Painter p;
    p.dis(Color::yellow);
    return 0;
}
```

### 1.14.3 指定类型

​	默认的底层数据类型是`int`，用户可以通过：`type(冒号+类型)`来指定任何**整型**（除了wchar_t）作为底层数据类型。

```c++
#include <iostream>
using namespace std;

enum class color:int {red,green,yellow};
enum class colorX:char {red, green,yellow};

int main()
{
    //使用域作用符访问枚举体成员，强转后打印
    cout<<static_cast<int>(color::red)<<endl;
    cout<<static_cast<int>(colorX::red)<<endl;
    return 0;
}
```

# 2. More Effective

## 2.1 assert/static_assert

### 2.1.1 assert

​	`assert`是运行期断言，它用来发现运行期间的错误，不能提前到编译期发现，也不具有强制性，也谈不上改善编译信息的可读性，既然是运行期检查，对性能当然是有影响的，所以经常在发行版本中，`assert`都会被关掉。

​	`assert`关键在于判断expression的逻辑真假，如果为false，就会在stderr上面打印一条包含“表达式，文件名，行号”的错误信息，然后调用abort结束整个程序。

```c++
#include <iostream>
#include <assert.h>
using namespace std;

char * myStrcpy(char * dest, const char * src)
{
    assert(dest);
    assert(src);
    char *d = dest;
    while(*dest++ = *src++);
    return d;
}

int main()
{
    char *dst = NULL, *src = NULL;
    myStrcpy(dst,src);
    return 0;
}
```

![image-20230717203704947](C:\Users\Luffy\AppData\Roaming\Typora\typora-user-images\image-20230717203704947.png)



### 2.1.2 static_assert

​	`static_assert`是编译期断言，因此叫作静态断言。其语法很简单：`static_assert(常量表达式,提示字符串)`。

​	如果第一个参数常量表达式的值为真（true或非零值），那么`static_assert`不做任何事情，就像它不存在一样，否则会产生一条编译错误，错误位置就是该语句所在行，错误提示为第二个参数提示字符串。

​	使用`static_assert`，我们可以在编译期间发现更多错误，用编译器来强制保证一些契约，并帮助我们改善编译信息的可读性，尤其是用于模板时。

​	`static_assert`可以用在全局作用域中，命名空间中，类作用域中，函数作用域中，几乎可以不受限制的使用。

​	编译器在遇到一个`static_assert`语句时，通常立即将其第一个参数作为常量表达式进行演算，但如果该常量表达式依赖于某些模板参数，则延迟到模板实例化时再进行演算，这就让检查模板成为了可能。

```c++
#include <iostream>
using namespace std;

static_assert(sizeof(void*)==8,"64-bit code generation is not supported.");

template<typename T, typename U>
int my_bit_copy(T &a, U &b)
{
    static_assert(sizeof(a)==sizeof(b),
                  "parameters must have same width");
}

int main()
{
    int a; float b;
    my_bit_copy(a,b);
    char c;
    my_bit_copy(a,c);
    return 0;
}
```

## 2.2. 右值引用

### 2.2.1 右值定义

​	C++11以前没有明确提出右值的概念。C++11中的左值，仍然等同于C++98；C++11中的右值，除了C++98中的右值以外，还增加了将亡值。

![image-20230721094420781](C:\Users\Luffy\AppData\Roaming\Typora\typora-user-images\image-20230721094420781.png)

### 2.2.2 右值引用的作用

​	引入右值引用，为了**解决临时对象带来的效率问题**。

​	栈对象是可以返回的，栈对象的引用却不可以返回。栈对象返回，若在没有优化的情况下-fno-elide-constructors，会产生临时对象。

![image-20230721094728767](C:\Users\Luffy\AppData\Roaming\Typora\typora-user-images\image-20230721094728767.png)

```C++
#include <iostream>

using namespace std;

class A
{
public:
    A(){cout<<"A() "<<this<<endl;}
    ~A(){cout<<"~A()"<<this<<endl;}
    A(const A &another){cout<<"A(const A &)"<<this<<"->"<<&another<<endl;}
    void dis()
    {
        cout<<"xxxxxxxxxxxoooooooooooo"<<endl;
    }
};

A getObjA()  //拷贝不可避免，临时对象一定会产生
{
    return A();
}

int main()
{
    A a = getObjA();
    return 0;
}

```

未优化的结果

![image-20230721101653571](C:\Users\Luffy\AppData\Roaming\Typora\typora-user-images\image-20230721101653571.png)

引入右值引用

```c++
int main()
{
    A && a = getObjA();  //引用的是临时对象，扩展了临时对象的作用域，延长了其生命周期
    return 0;
}
```

![image-20230721101416466](C:\Users\Luffy\AppData\Roaming\Typora\typora-user-images\image-20230721101416466.png)

​	在没有优化的情况下，要经历两次复制，优化以后，各个平台大不相同（有构造一次的，也有拷贝一次的）。对此，C++志在解决平台不一致的问题。右值引用的意义如下。

![image-20230721102247339](C:\Users\Luffy\AppData\Roaming\Typora\typora-user-images\image-20230721102247339.png)

### 2.2.3 `const T &` 万能常引用

```c++
int main()
{
    const A & a = getObjA();
    return 0;
}
```

![image-20230721102907340](C:\Users\Luffy\AppData\Roaming\Typora\typora-user-images\image-20230721102907340.png)

​	`const`可以实现常量引用，不同类型的引用（非基本数据类型也适用，但需要转化构造函数）。

​	其本质也是产生了临时对象，并且该临时对象是`const`的。

​	如果一个函数的参数是非const类型，此时传递临时对象过来，则会编译不过，要想编译通过需要加const修饰。



缺陷：此种举措，也可以在返回中避免产生临时对象的再次拷贝和销毁。但临时对象的性质是const的，也会给后续的使用带来不便。	

​			const函数重载也是在此种返值const类型的情形下诞生，const对象只能调用const成员函数。

​	const T & ---> &&

### 2.2.4 与左值引用的对比

- 都属于引用类型
- 都必须初始化。左值引用时具名变量的别名，右值引用是匿名变量的别名。
- 左值引用，用于传参，好处在于，扩展对象的作用域。则右值引用的作用就在于延长了临时变量的生命周期。
- 避免“先拷贝再废弃”带来的性能浪费。
- 对将亡值进行引用的类型；它存在的目的就是为了实现移动语义。



## 2.3 移动构造

### 2.3.1 拷贝语义

​	自此，四大默认-->六大默认。

​	移动构造，本质：特定意义/使用场景下的浅拷贝，解决效率问题。

​	对于类中，含有指针，即含有堆内存资源时，要自实现其拷贝构造和拷贝赋值。也就是深拷贝和深赋值，避免造重析构或内存泄漏问题。这已经成为一种共识。

### 2.3.2 移动语义

```c++
#include <iostream>
using namespace std;
class HasPtrMem
{
public:
    HasPtrMem():_d(new int(100)){
        cout<<"HasPtrMem()"<<this<<endl;
    }
    HasPtrMem(const HasPtrMem& another)
        :_d(new int(*another._d)){
        cout<<"HasPtrMem(const HasPtrMem& another)"<<
            this<<"->"<<&another<<endl;
    }
    HasPtrMem(HasPtrMem&& another)
    {
        cout<<"HasPtrMem(HasPtrMem&& another)"<<
            this<<"->"<<&another<<endl;
        _d = another._d;
        another._d = nullptr;
    }
    ~HasPtrMem(){
        if(_d != nullptr)
            delete _d;
        cout<<"~HasPtrMem()"<<this<<endl;
    }
    int * _d;
};
HasPtrMem getTemp()
{
    return HasPtrMem();
}
int main(int argc, char *argv[])
{
//    HasPtrMem a;
//    HasPtrMem b(a);
    // cout<<*a._d<<endl;
    // cout<<*b._d<<endl;
    HasPtrMem&& ret = getTemp();
    return 0;
}
```

![image-20230721112341315](C:\Users\Luffy\AppData\Roaming\Typora\typora-user-images\image-20230721112341315.png)

​	移动构造函数，最大的用途就是避免同一份内存数据的不必要的变成两份甚至多份、过程中的变量传递导致的内存复制，另外解除了栈变量对内存的引用。

### 2.3.3 关于默认

​	对于不含有资源的对象来说，自实现拷贝和移动语义并没有意义，对于这样的类型而言，移动就是拷贝，拷贝就是移动。

​	拷贝构造/赋值 和 移动构造/赋值，必须同时提供或是同时不提供。才能保证同时具有拷贝和移动语义。只声明一种的话，类只能实现一种语义。

​	![image-20230721112859548](C:\Users\Luffy\AppData\Roaming\Typora\typora-user-images\image-20230721112859548.png)

### 2.3.4 慎用移动

​	拷贝，无疑是安全的，而移动，无疑是高效的。但是这种高效前提你对资源的深刻的理解，否则可能会带来极大的安全隐患，所以慎用。

​	移动解决了，如下第一个拷贝的效率问题，引用，解决了，如下第二个拷贝的效率问题。

![image-20230721113719342](C:\Users\Luffy\AppData\Roaming\Typora\typora-user-images\image-20230721113719342.png)

## 2.4 六大默认

