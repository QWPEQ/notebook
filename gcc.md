# gcc的使用

## 什么是gcc
* gcc编译器(GNU C Compiler)
* 现在我们所说的 gcc 是 GUN Compiler Collection的缩写，可以支持多种语言编译，比如 C,C++,Java, pascal 等
## gcc主要特征
1. 可移植编译器，支持多种硬件平台
2. 支持跨平台交叉编译（跨CPU）
3. 多种语言前端，用于解析不同语言
4. 按模块化设计，可以加入新语言和新CPU架构的支持
5. 自由软件
## gcc编译过程
1. 预处理（pre-processing）E
2. 编译（Compiling） S
3. 汇编（Assembling） c
4. 链接 （Linking）
![](index_files/36caf4c7-6088-4bd1-ae65-feb2eddfe405.jpg)
## gcc常用选项
|选项名|作用|
|------|-----|
|**-o**|**生成目标**|
|**-c**|**取消链接步骤，编译源码并最后生成目标文件**|
|-E|只运行C预编译器（头文件，宏等展开）|
|-S|生成汇编语言文件后停止编译（.s文件）|
|**-Wall**|**打开编译告警（所有） warning all**|
|-Idir|增加 include 目录   (这里是大写 i ) 头文件路径|
|-LDir|增加 lib 目录 （编译静态库和动态库）|
|llib|链接 lib 库   （这里是小写 L ）  相当于 C++  #pragma comment(lib, “xxx.lib”)|
|**-g**|**嵌入调试信息，方便gdb调试**|

1. gcc -E hello.c -o hello.i
1. gcc  -S hello.i –o hello.s
1. gcc –c hello.s –o hello.o       
1. gcc hello.s –o hello

* 一次性完成: gcc hello.c –o hello
* 编译生成可重定位目标文件:gcc –c hello.c  [-o hello.o ]


## -Wall选项目
* -Wall 选项 增加所有编译告警
* 举例： printf(“Four:%f\n”,4)  定义参数未使用等

## 多模块编译
### 一次性编译：
*　gcc -Wall fun.c main_fun.c –o  main_fun

### 独立编译：
* gcc –Wall –c main_fun.c –o main_fun.o
* gcc –Wall –c fun.c –o fun.o
* gcc –Wall main_fun.o fun.o –o main_fun

## 多模块独立编译
### 优点：
* 改变了某一个模块，只需要编译更改的模块
* ==》 通过makefile 来进行自动化编译

## gcc进阶
### 使用外部库
### 静态库，共享库
### 生成静态库
### 生成动态库

## 头文件与库文件
* 库文件：预先编译好的函数集合，可重用，比如说 ncurse库(处理屏幕显示)，dbm库(数据库访问)

## 头文件，库文件位置
1. /usr/include/ 
2. /usr/local/include/
3. /usr/lib/
4. /usr/local/lib/

## 使用外部库
```
#include <math.h>
#include <stdio.h>
int main()
{
    double x=pow(2.0,3.0);
    printf("Result:%f\n",x);
    return 0;
}
```

* gcc –Wall calc.c –o calc –lm     (libm 数学函数库， lib省略)

## 静态库与共享库(动态库)
### 静态库（.a）: 程序在编译链接时候把库的代码链接到可执行文件中。程序运行时候，不再需要静态库
* 生成的可执行文件大，每个可执行文件都会加载一份拷贝到内存
### 共享库（.so或.sa）: 程序运行时候才去链接共享库代码，多个程序共享使用
* 使用时候只需要加载一份到内存

## 共享库
* 共享库链接的可执行文件：包含了一个函数入口地址表
* 可执行文件运行时候，操作系统把外部函数从磁盘共享库中复制到内存（也就是动态链接）
* 操作系统采用虚拟内存机制，允许物理内存中一份共享库被所有需要的进程公用，节省内存和磁盘空间

## 生成静态库
### 静态库生成（libxxx.a）：
* gcc –c fun.c    生成 myslib.o
* ar rcs libfun.a  fun.o生成 libMyslib.a
  rcs: replace and create
### 静态库使用：
1. gcc –Wall main.c libfun.a –o main
2. gcc –Wall –L. main.c –o main –lfun
 -L.表示在当前目录搜索 libfun.a
### 库搜索路径
1. 编译使用选项 –L 指定的目录**(建议)**
2. 修改环境变量，搜索指定的目录    
设置环境变量  LIBRARY_PATH （library）
export LIBRARY_PATH=“_库目录_”
3. lib文件放入系统指定目录

## 生成共享库（libxxx.so）
* gcc –shared –fPIC fun.o –o libFun.so
1. shared：生成共享库格式
2. fPIC：  产生位置无关码，允许在任何地址加载 (否则只能从指定地址加载，无法控制)相对地址


* 使用：
1. gcc –Wall main.o –o main –L. -lFun
### 运行共享库

#### 配置方式
1. 拷贝到so文件到共享库目录 /usr/lib/
2. 更改 LD_LIBRARY_PATH
3. 配置 /etc/ld.so.conf; 并使用 ldconfig 命令进行更新

* ldd main  查看main调用的动态库

## 注意事项
1. 两个库同时存在，优先使用的是共享库
2. 使用静态库，编译好可执行文件后，删除静态库，可执行文件无影响，但是共享库不能删除。

