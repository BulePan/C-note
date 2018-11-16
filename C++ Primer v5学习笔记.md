# C++ Primer 第5版学习笔记

## 变量和基本类型
+ **初始化列表**：在C++11中，用花括号来初始化变量的形式。如：
```
int arg = 10;
int arg = {10}; //列表初始化
int arg(10);
int arg{10}; //列表初始化
```
使用这种形式初始化内置类型的一个重要**特性**：如果我们使用列表初始化且初始化值存在丢失信息的风险，则编译器将报错。如：
```
long double ld = 3.1415926536;
int a{ld}, b = {ld}; //错误：转换未执行，因为存在丢失信息的危险
int c(ld), d = ld;   //正确:转换执行,且确实丢失了精度
```

+ **引用**:C++11引入**右值引用**,但"引用"常指**左值引用**, 为对象起了一个别名,==引用并非对象, 它只是为一个已存在的对象所起的另外一个名字,因此**声明引用时必须初始化**==.如:
```
int ival = 1024;
int &refVal = ival; //refVal指向ival(是ival的另一个名字)
int &refVal2;       //错误:引用必须被初始化
```
+ **引用与指针的区别**:
    1. 引用是另一个对象的别名,而指针是一个变量,因此引用不再用存储空间,而指针需要存储空间;
    2. 引用在声明时必须初始化,而指针定义时可以不初始化(但后续对指针操作前必须赋值),即引用初始化后不能在修改,但指针可以再指向其他对象;
    3. 不存在多级引用,但存在多级指针;
    4. 引用的对象必须是一块合法的空间(如不能对NULL引用),但指针可以指向NULL作为特殊标识.
    5. 对引用执行加减操作实际上是对引用的对象加减,但对指针进行加减,是前后移动指针.
    6. 引用不是对象,因此不能定义指向引用的指针,但指针是对象,所以存在指针的引用.

+ **对常量的引用**:可以把引用绑定到const对象上。对常量的引用不能被用作修改它所绑定的对象。
```
const int ci = 1024;
const int &r1 = ci; //正确：引用及其对应的对象都是常量
r1 = 42;            //错误：r1是对常量的引用，不能修改
int &r2 = ci;       //错误：试图让一个非常量引用绑定一个常量对象
                    //假设以上初始化合法，则就可以通过r2修改它引用对象的指，这肯定不正确。
```
对常量的引用可简称为**常量引用**(只是简称)，严格来说，并不存在常量引用。因为引用本身不是一个对象，所以我们没办法让引用本身恒定不变。

```
int i = 42;
const int &r1 = i;      //正确,允许将对const的引用绑定到一个非const对象上
const int &r1 = 42;     //正确：r2是一个常量引用
const int &r3 = r1 * 2; //正确
int &r4 = r1 * 2;       //错误：r4是一个普通的引用
int &r5 = i;            //r5绑定对象i
r5 = 0;                 //正确，r5不是对const的引用，i的值被修改为0
r1 = 0;                 //错误，r1是一个常量引用，不能从r1修改i的值，但是可以从其他地方修改i的值
```

+ **指向常量的指针**：不能用于改变其所指对象的指。要想存放常量对象的地址，只能使用指向常量的指针。
```
const double pi = 3.14;   //pi是一个常量，它的指不能改变
double *ptr = &pi;        //错误：ptr是一个普通指针
const double *cptr = &pi; //正确：cptr可以指向一个double常量
*cptr = 42;               //错误：不能给*cptr赋值
```
==和常量引用一样，指向常量的指针也没有规定其所指的对象必须是一个常量。所谓指向常量的指针仅仅要求不能通过该指针改变对象的值，而没有规定那个对象的值不能通过其他途径改变。==

+ **const指针**：把指针本身定为常量，也称**常量指针**，它必须初始化，而且一旦初始化完成，则它的值(指针变量本身的值(是一个地址),而不是指针指向的对象的值)就不能在改变。
```
int errNumb = 0;
int *const curErr = &errNumb; //curErr将一直指向errNumb
const double pi = 3.14;
const double *const pip = &pi; //pip是一个指向常量对象的常量指针

*curErr = 1; //正确：curErr指向的对象不是常量可以修改，只是curErr不能再指向其他对象
*pip = 2.72; //错误：pip指向的对象是一个常量，不能修改，同时pip也不能指向其他对象
```
指针本身是一个常量并不意味着不能通过指针修改其所指对象的值，能否这样做完全依赖与所指对象的类型。

+ **顶层const**
指针是一个对象，它又可以指向另外一个对象，因此指针本身是不是常量以及指针所指对象是不是常量是两个独立的问题。**顶层const**表示指针本身是一个常量，而**底层const**表示指针所指对象是一个常量。顶层const对任何数据类型都适用，底层const只与指针、引用等符合类型的基本类型部分有关。
```
int i = 0;
int *const p1 = &i;      //p1的值不能改变，所以是一个顶层const
const int ci = 42;       //ci的值不能改变，所以是一个顶层const
const int *p2 = &ci;     //p2指向的对象不能改变，所以是一个底层const
const int *const p3 = p2;//*号右边的是顶层const，左边的是底层const
const int &r = ci;       //用于声明引用的const都是底层const**==
```
==在执行拷贝操作时，顶层const不受影响。==
```
i = ci;  //拷贝ci的值，ci是一个顶层const，对此不受影响
p2 = p3; //p2和p3指向的对象类型相同，p3顶层const的部分不受影响。
```
==在执行拷贝时，拷入和拷出的对象必须具有相同的底层const资格，或者两个对象的数据类型必须能够转换，一般，非常量可以转换成常量。==
```
int *p = p3; //错误：p3包含底层const，而p没有
p2 = p3; //正确：p2和p3都是底层const
p2 = &i; //正确：非const可以转换成const
int &r = ci; //错误：普通引用不能绑定到常量上
const int &r2 = i; //正确：对常量的引用可以绑定到一个普通对象上
```
+ **constexpr变量**
C++11规定允许将变量声明为constexpr类型以便编译器来验证变量的值是否是一个常量表达式。==声明为constexpr的变量一定是一个常量，而且必须常量表达式初始化。==
一个constexpr指针的初始值必须是nullptr或者0，或者存储于某个固定地址中的对象(全局变量或者静态变量)。constexpr限定符只对指针本身有效，与指针所指对象无关。
```
const int *p = nullptr;     //p是一个指向整型常量的指针，是一个底层const
constexpr int *q = nullptr; //q是一个指向整数的常量指针，是一个顶层const
```

+ **类型别名**：某种类型的另一个名字。
定义类型别名的两种方式：typedef、using
  
```
typedef double wages;   //wages是double的别名
typedef wages base, *p; //base是double的别名，p是double * 的别名

using SI = Sales_item; //SI是Sales_item的别名
```
指针、常量和类型别名
```
typedef char *pstring;
const pstring ctr = 0; //cstr是指向char的常量指针，而不是指向常量字符的指针
const pstring *ps; //ps是一个指针，它的对象是指向char的常量指针
```
+ **auto类型说明符**
C++11引入auto类型说明符，让编译器通过初始值来推断变量类型。因此==**auto定义的变量必须初始化。**==
```
auto  item = val1 + val2; //item初始化为val1和val2相加的结果，item的类型根据val1和val2的类型而定。
auto i = 0, *p = &i; //正确：i是整数，p是整型指针
auto sz = 0, pi = 3.14; //错误：sz和pi的类型不一致。
```
**符合类型、常量和auto**
使用auto类型说明符时：
1. 当引用被用作初始值时，真正参与初始化的是引用对象的值，此时编译器引用对象的类型作为auto的类型；
2. auto一般会忽略顶层const，同时保留底层const，如果希望推断出的类型是一个顶层const，需要明确指出。
```
int i = 0, &r = i; 
auto a = r; //a是一个整数(r是i的别名，而i是一个整数)

const int ci = i, &cr = ci;
auto b = ci; //b是一个整数(ci的顶层const被忽略)
auto c = cr; //c是一个整数(cr是ci的别名)
auto d = &i; //d是一个整型指针
auto e = &ci; //e是一个指向整型常量的指针(对常量对象取地址是一种底层const)

const auto f = ci; //ci的推演类型是int，f是const int

auto &g = ci; //g是一个整型常量引用
auto &h = 42; //错误：不能为非常量引用绑定字面值
cosnt auto &j = 42; //正确：可以为常量引用绑定字面值
```

+ **decltype类型指示符**
当希望从表达式的类型推断出要定义变量的类型，但又不想用该表达式的值初始化变量，此时可以使用**decltype类型指示符**。它的**作用**是选择并返回操作数的数据类型。过程中，编译器只分析表达式并得到它的类型，而不实际计算表达式的值。

**decltype处理顶层const和引用的方式与auto不同**，它会返回该变量的类型，包括顶层const和引用。
```
const int ci = 0, &cj = ci;
decltype(ci) x = 0; //x的类型是const int
decltype(cj) y = x; //y的类型是const int &，y绑定到x
decltype(cj) z;     //错误：z是一个引用类型，必须初始化
```
**decltype与引用**
```
int i = 42, *p = &i, &r = i;
decltype(r+0) b; //正确：加法的结果是int，因此b是一个未初始化的int
decltype(*p) c; //错误：c是int&，必须初始化
```
因为r是一个引用，因此decltype(r)的结果是引用类型，如果想让结果类型是r所指的类型，可以把r作为表达式的一部分，表达式的结果将是一个具体的值而非引用。如果表达式的内容是解引用操作，则decltype将得到引用类型。
==decltype((variable)) (双括号)的结果永远是引用，而decltype(variable)的结果只有当variable本身就是引用时才是引用。==

## 强制类型转换
+ **static_cast** 
任何具有明确定义的类型转换，只要不包含底层const，都可以使用static_cast。常用的有：第一种将较大算数类型转换成较小数据类型，此时编译器不会提示丢失精度的警告；第二种将存放在void*中的指针转换回原来的类型；也可用于将晓得数据类型转换为大的数据类型如int转double。
+ **cosnt_cast**
它只能改变运算对象的底层const，对于将常量对象转换成非常量的行为一般称为**去掉const性质**，一旦去掉对象的const性质，程序中可以对该对象进行写操作，如果对象本身不是常量，写操作合法，但如果对象本身是常量，执行写操作将是未定义行为。const_cast 常用于有函数重载的上下文中。
```
const char* pc;
char *p = const_cast<char *>(pc);//正确：但是通过p写值是未定义行为

const char *cp;
char *q = static_cast<char*<(cp); //错误：static_cast不能去掉const性质
static_cast<string>(cp); //正确：支付穿字面值转换成string类型
const_cast<string>(cp); //错误：const_cast只能改变常量属性
```
+ **reinterpret_cast**
通常为运算对象的位模式提供较低层次上的重新解释。如：
```
int *p;
char *pc = reinterpret_cast<char *>(ip);
```
但必须记住pc所指的真实对象是int而非字符，如果把pc当做普通的字符指针使用就可能在运行时发生错误。reinterpret_cast的本质依赖机器。

+ **dynamic_cast**
支持运行时类型识别。

