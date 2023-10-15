# C++在VS的常见错误解决

C开头的错误是发生在编译阶段，而LNK开头发生的错误在链接阶段。



## error C2572：重定义默认参数

注意形参的初始值只要有一个设定，它后面也必须是有初始值的。

如果.h文件和.cpp文件的函数形参都有初始值，那么一般是保留函数声明的初始值（.h文件），将.cpp文件的形参初始值删除掉。



## error LINK ：无法解析的外部符号

1. __report_rangecheckfailure 是用来检查堆栈缓存溢出的，如果编译的时候打开GSQ
   (project property->Configuration properties -> c/c++ -> Code generation -> Buffer security check)选项时(GS编译选项解析)，编译器将为你加入检测函数堆栈缓存谥出错误额外代码。具体实现在wdow下的运行时库文件msvcrt.d中。而这些检查堆栈缓存益出的代码中在msvcrt11.d及以后版本中都会调用到report rangecheckfailure函数，所以当你用vs2010及以前版本进行开发，并使用了由vs2012及以后编译器编译的库文件时，在链接的时候就会报这样的错误。

2. 确定是自己编译的动态链接库，多半是动态链接库引入不正确。

   选中项目名称右键 -> 属性 -> 链接器 -> 常规 -> 附加库目录 -> lib文件所在路径

   选中项目名称右键 -> 属性 -> 链接器 -> 输入 -> 附加依赖性 -> lib文件名称

3. j检查.h里的声明和.cpp里文件是否一一对应。

4. 如果没用lib库，include头文件，并且.cpp文件里也实现了头文件所定义的函数，还是出现了无法解析的外部符号。

5. 检查一下`#ifndef`和`#define`之间的代码块是否匹配该头文件。

   原因可能是没把他们添加进项目中，右键->添加现有项目->把对应文件添加进来。
   
   【如果一个没有被调用的函数发生链接错误，可以给这个函数加个static，那么它就只在这个cpp文件中存在。或者把它改为inline的，即编译器将函数的代码插入到调用该函数的地方，而不是通过函数调用的方式执行。）



# link : fatal error lnk1168: 无法打开 进行写入

进程已经打开，把进程关掉或者重启电脑可解决。



# 无法打开包括文件：  xxx

检查一下#include的文件地址，如果地址较长，需要在项目右键属性，C/C++常规->附加包含目录把路径加进去。

例如：





## 表达式必须包含类类型

注意指针调用函数是用`->`而不是`.`



## 类型转换

### 向下转型

`dynamic_cast<>()`

例如：要把Constraint类型指针转化为它的派生类ViewSurfaceSpring指针

```C++
ViewSurfaceSpring* vss = dynamic_cast<ViewSurfaceSpring*>(_viewConstraint)
```



### const指针赋给非const指针

1. 隐式类型转换 `const_cast<>()`

```C++
const int* constPtr = new int(42);
int* nonConstPtr = const_cast<int*>(constPtr);
```

2. 强制类型转换 `reinterpret_cast<>()`

```C++
const int* constPtr = new int(42);
int* nonConstPtr = reinterpret_cast<int*>(constPtr);
```



