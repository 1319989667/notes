# 项目属性

![image-20230713202253508](C:\Users\lanwei\AppData\Roaming\Typora\typora-user-images\image-20230713202253508.png)

解决方案配置和平台：

1. 配置的平台的设置只针对它本身。如果设置release对debug是没有影响的。
2. 配置属性->项目默认值->配置类型可以设置生成的是.exe还是.dll或者.lib
3. C/C++->预处理器->预处理到文件 在编译的时候会输出合并好的文件
4. C/C++>输出文件->汇编程序输出 可以选择输出的汇编文件格式
5. 如果想让debug模式开启优化模式，在C/C++ ->优化->优化选择最大速度，然后再C/C++->代码生成->基本运行检查设置默认。
6. 在项目属性->常规的输出目录和中间目录设置好自己想要的路径。比如输出目录设置为`$(SolutionDir)bin\$(Platform)\Configuration\`。中间目录设置为`$(SolutionDir)bin\intermediates\$(Platform)\Configuration\`



编译器方面的设置在C / C++ 里。Debug和Release的区别在于，Debug的优化选项是被禁用的。

链接器的作用是把cpp文本编译后的obj文件连接在一起。





# debug须知

compile只会编译单个文件。但是生成是生成整个项目

编译正确，但有link错误，一般是编译器找不到定义。

**【可以找一些编译与链接的更深入的视频学习】**



## 编译器工作原理

重点：文件没有意义！（ 可以更改编译器对待不同文件格式的处理方式）

一般情况是  .cpp ➡ 翻译单元 ➡ obj文件【如果一个cpp文件include其他cpp，那它们会合并为同一个翻译单元，形成大的obj文件】



常见的预处理语句：`#include`、`if ifdef`、`pragma`

1. `#include`所做的事情就是去把所提到的文件内容全部阅读一遍并粘贴到当前的位置。

2. `#define`就是直接替换内容。`#define Integer int`编译器会将文件里出现的Integer换为int

3. `#pragma once`是头文件保护符，防止我们把单个头文件多次include到同一个翻译单元里。

   

## 看报错

error List 很坑人，它是抓取Output窗口的error字段，但一般会缺失信息，所以我们一般看output窗口，有错误就双击那一行。



## 声明与定义

声明➡这个符号、函数是存在的。

定义➡这个函数到底是什么。（函数体）



# 头文件

头文件放的是声明。防止我们把单个头文件多次include到同一个翻译单元里。有两种方法都行。

1. `#pragma once`    头文件保护符，防止我们把单个头文件多次include到同一个翻译单元里。【现在基本用这个，更简洁】

2.  `ifndef`  这是老的方法

```c++
#ifndef _LOG_H
#define _LOG_H

// 写头文件的内容
void InitLog(); 
void play();

#endif
```



对于`include`，如果在相对层级的目录可以找到就用`""`（其实这个万能），如果在设置的include的目录下找到就是`<>`

C的标准库有扩展名`.h`，而C++的标准库的扩展名没有`.h`
