# linux编程入门(七)-使用gdb调试程序

程序开发离不开调试，可以断点调试，也可以打log调试，linux下断点调试c,c++程序用gdb。 

断点调试虽然很爽，但是效率较低，浪费时间。好的程序有完备的log，任何有可能出错的地方，都有log记录，所以只要看log一眼就能知道哪里有问题。尤其是我们在做服务器开发的时候，线上是不可能让你打断点调试的。所以在程序里记上完备的log是良好的习惯，会为你节省大量的调试时间。

但是，断点调试是我等必备的职业技能之一，所以必须熟练掌握断点调试。  

下面我们开始学习gdb调试。  
先安装gdb，一路走来的同学应该已经会安装了，这里我就不啰嗦了。  

[toc]

## 运行gdb
装完以后,输入gdb运行一下,就是下面的效果
``` c
# 这里输入gdb
bash$ gdb
GNU gdb (Ubuntu 8.2-0ubuntu1) 8.2
Copyright (C) 2018 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word".
(gdb)   #这里等待输入命令
```

我们现在还没有程序可以调试,先退出gdb
``` c
# 接上一步,输入q,退出
(gdb) q
```

## 准备个程序,用于调试
共4个文件:  
main.cpp  
test_class.h  
test_class.cpp  
Makefile  

test_class.h的内容
``` c
#ifndef __test_class_h__
#define __test_class_h__

class TestClass{
public:
    TestClass();
    virtual ~TestClass();

public:
    int sum(int a, int b);
    int fib(int n);

    int fib2( int n );
};

#endif//__test_class_h__
```

test_class.cpp的内容
``` c
#include <stdio.h>

TestClass::TestClass(){
}
TestClass::~TestClass(){
}

int TestClass::sum(int a, int b){
    int c = a+b;
    return c;
}
int TestClass::fib(int n){
    if( n <= 0 ){
        return 0;
    }
    if( n == 1 ){
        return 1;
    }

    int a1 = fib(n-1);
    int a2 = fib(n-2);
    int c = a1 + a2;
    return c;
}

int TestClass::fib2( int n ){
    if( n <= 0 ){
        return 0;
    }
    if( n <= 2 ){
        return 1;
    }

    int tmp[] = { 0, 1, 1 };

    for( int i=2; i<n; ++i ){
        tmp[0] = tmp[1];
        tmp[1] = tmp[2];
        tmp[2] = tmp[0] + tmp[1];
    }

    return tmp[2];
}

```

main.cpp的内容
``` c
#include <stdio.h>
#include <string.h>
#include "test_class.h"

int main(int argc, char** argv){
    (void)argc;
    (void)argv;

    printf("hello dafei\n");

    TestClass o;
    int a = 1;
    int b = 2;
    int c = o.sum( a, b );

    char buff[256]; memset(buff, 0x00, sizeof(buff));
    snprintf(buff, sizeof(buff), "sum: %d+%d=%d\n", a, b, c);
    printf("%s", buff);

    for( int i=1; i<10; ++i ){
        int f = i;
        int fv = o.fib( f );

        int fv2 = o.fib2( f );

        memset(buff, 0x00, sizeof(buff));
        snprintf(buff, sizeof(buff), "fib: %d = %d  %d\n", f, fv, fv2 );
        printf("%s", buff);
    }



    return 0;
}

```

Makefile
``` c
LINK    = @echo linking $@ && g++
GCC     = @echo compiling $@ && g++
GC      = @echo compiling $@ && gcc
AR      = @echo generating static library $@ && ar crv
FLAGS   = -g -DDEBUG -W -Wall -fPIC
GCCFLAGS =
DEFINES =
HEADER  = -I./
LIBS    =
LINKFLAGS =

#LIBS    += -lrt
#LIBS    += -pthread

OBJECT := main.o \
          test_class.o

BIN_PATH = ./

TARGET = main

$(TARGET) : $(OBJECT)
    $(LINK) $(FLAGS) $(LINKFLAGS) -o $@ $^ $(LIBS)

.cpp.o:
    $(GCC) -c $(HEADER) $(FLAGS) $(GCCFLAGS) -fpermissive -o $@ $<

.c.o:
    $(GC) -c $(HEADER) $(FLAGS) -fpermissive -o $@ $<

install: $(TARGET)
    cp $(TARGET) $(BIN_PATH)

clean:
    rm -rf $(TARGET) *.o *.so *.a

```

编译程序
``` c
bash$ make
compiling main.o
compiling test_class.o
linking main
```

查看一下生成的程序
``` c
bash$ ls
main  main.cpp  main.o  Makefile  test_class.cpp  test_class.h  test_class.o
```

用gdb运行程序就可以下断点了,  
> !!!注意  
当运行gdb ./main的时候,只是用gdb加载了main程序,main本身还没开始运行。  

``` c
bash$ gdb ./main
GNU gdb (Ubuntu 8.2-0ubuntu1) 8.2
Copyright (C) 2018 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from ./main...done.
(gdb)
```

显示当前运行到的代码,因为现在程序还没开始运行,所以显示在main函数    
命令: list，可以简写为l
``` c
(gdb) l
1       #include <stdio.h>
2       #include <string.h>
3       #include "test_class.h"
4
5       int main(int argc, char** argv){
6               (void)argc;
7               (void)argv;
8
9               printf("hello dafei\n");
10
```

按回车会重复上一次的命令,也就是再执行一次l,效果是翻到下一页
``` c
(gdb)      #这里输入了回车,默认执行上一次的命令
11              TestClass o;
12              int a = 1;
13              int b = 2;
14              int c = o.sum( a, b );
15
16              char buff[256]; memset(buff, 0x00, sizeof(buff));
17              snprintf(buff, sizeof(buff), "sum: %d+%d=%d\n", a, b, c);
18              printf("%s", buff);
19
20              for( int i=1; i<10; ++i ){
(gdb)
```

显示当前文件的第20行代码可以用l 20
``` c
(gdb) l 20
15
16              char buff[256]; memset(buff, 0x00, sizeof(buff));
17              snprintf(buff, sizeof(buff), "sum: %d+%d=%d\n", a, b, c);
18              printf("%s", buff);
19
20              for( int i=1; i<10; ++i ){
21                      int f = i;
22                      int fv = o.fib( f );
23
24                      int fv2 = o.fib2( f );
```

显示TestClass::fib的代码
``` c
(gdb) l TestClass::fib
8
9       int TestClass::sum(int a, int b){
10              int c = a+b;
11              return c;
12      }
13      int TestClass::fib(int n){
14              if( n <= 0 ){
15                      return 0;
16              }
17              if( n == 1 ){
```

## 下断点
gdb里下断点很灵活,下面看几种下断点的方法:  
命令: break，可以简写为b

* 在main函数下个断点
``` c
(gdb) b main
Breakpoint 1 at 0x11ae: file main.cpp, line 5.
```

* 在当前文件的第20行下个断点,因为当前文件是main.cpp所以断点下在main.cpp的第20号
``` c
(gdb) b 20
Breakpoint 2 at 0x127d: file main.cpp, line 20.
```

* 指定断点下在类的成员函数
``` c
(gdb) b TestClass::sum
Breakpoint 3 at 0x141e: file test_class.cpp, line 10.
```

* 指定断点下到test_class.cpp的第29行
``` c
(gdb) b test_class.cpp:29
Breakpoint 5 at 0x14b4: file test_class.cpp, line 29.
```

## 显示所有断点 
命令: info break，可以简写为 i b  

下面显示了当前共有4个断点,每个断点有相应的编号,Enb表示是否启用,我们可以禁用某个断点 
``` c
(gdb) i b
Num     Type           Disp Enb Address            What
1       breakpoint     keep y   0x00000000000011ae in main(int, char**) at main.cpp:5
2       breakpoint     keep y   0x000000000000127d in main(int, char**) at main.cpp:20
3       breakpoint     keep y   0x000000000000141e in TestClass::sum(int, int) at test_class.cpp:10
4       breakpoint     keep y   0x00000000000014b4 in TestClass::fib2(int) at test_class.cpp:29
```

## 禁用断点
命令: disable，可以简写为dis  
下面禁用4号断点,如果不指定编号,会禁用所有断点
``` c
(gdb) dis 4
```

再显示一下4号断点信息,可以看到Enb变为n了,表示该断点被禁用了
``` c
(gdb) i b 4
Num     Type           Disp Enb Address            What
4       breakpoint     keep n   0x00000000000014b4 in TestClass::fib2(int) at test_class.cpp:29
```

## 启用断点
命令: enable，可以简写为en  
下面启用4号断点，如果不指定编号，会启用所有断点
``` c
(gdb) en 4
```

再显示一下4号断点，可以看到Enb变为y了
``` c
(gdb) i b 4
Num     Type           Disp Enb Address            What
4       breakpoint     keep y   0x00000000000014b4 in TestClass::fib2(int) at test_class.cpp:29
```

## 删除断点
命令: delete，可以简写为d  
下面删除4号断点，如果不指定编号，会删除所有断点
``` c
(gdb) d 4
```

显示一下当前所有断点，可以看到4号断点没了
``` c
(gdb) i b
Num     Type           Disp Enb Address            What
1       breakpoint     keep y   0x00000000000011ae in main(int, char**) at main.cpp:5
2       breakpoint     keep y   0x000000000000127d in main(int, char**) at main.cpp:20
3       breakpoint     keep y   0x000000000000141e in TestClass::sum(int, int) at test_class.cpp:10
```

## 运行程序
命令: run 参数列表，可以简写为r
``` c
(gdb) r
Starting program: /home/dafei/test_gdb/main

# 因为我们在运行前已经打好断点,所以main程序刚跑起来就被断在main函数了
# 下面可以看到函数参数的值为argc=1, argv=0x7fffffffe1f8
Breakpoint 1, main (argc=1, argv=0x7fffffffe1f8) at main.cpp:5
5       int main(int argc, char** argv){
(gdb)
```

运行时带上参数abc
``` c
(gdb) r abc
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /home/dafei/test_gdb/main abc

# 下面可以看到argc为2,表示有一个参数,第一个为程序本身
Breakpoint 1, main (argc=2, argv=0x7fffffffe1d8) at main.cpp:5
5       int main(int argc, char** argv){

# 输出argv的值
(gdb) p argv[0]
$15 = 0x7fffffffe49c "/home/dafei/test_gdb/main"
(gdb) p argv[1]
$16 = 0x7fffffffe4d2 "abc"
```

## 显示源代码位置和编程语言
命令: info source 可以简写为i source  
该命令还会打印出编译选项
``` c
(gdb) i source
Current source file is main.cpp
Compilation directory is /home/dafei/test_gdb
Located in /home/dafei/test_gdb/main.cpp
Contains 34 lines.
Source language is c++.
Producer is GNU C++14 8.2.0 -mtune=generic -march=x86-64 -g -fPIC -fpermissive -fstack-protector-strong.
Compiled with DWARF 2 debugging format.
Does not include preprocessor macro info.
```

## 打印变量
命令: print，可以简写为p  

变量显示格式:
* x  按十六进制格式显示变量
* d  按十进制格式显示变量 
* u  按十六进制格式显示无符号整型
* o  按八进制格式显示变量
* t  按二进制格式显示变量
* a  按十六进制格式显示变量
* c  按字符格式显示变量
* f  按浮点数格式显示变量

 查看argc的值
``` c
(gdb) p argc
$2 = 1
```

查看变量的16进制值
``` c
(gdb) p/x argc
$6 = 0x1
```

查看argv的值
``` c
(gdb) p argv
$3 = (char **) 0x7fffffffe1f8
```

看*argv的值
``` c
(gdb) p *argv
$4 = 0x7fffffffe4a0 "/home/dafei/test_gdb/main"
```

查看argv[0]的值
``` c
(gdb) p argv[0]
$5 = 0x7fffffffe4a0 "/home/dafei/test_gdb/main"
(gdb)
```

## 下一步,跳过函数(与VS F10作用相同)
命令: next，可以简写为n
``` c
(gdb) n
# 程序计数器向前走了一步,下一步该执行第9行了
9               printf("hello dafei\n");
```
按回车重复上一步操作,也就是再执行一次n
``` c
(gdb)
# 第9行执行后,输出了一行字符串,并且程序计数器指向第11行
hello dafei
11              TestClass o;
```

再按次回车向前走一步
``` c
(gdb) 
12              int a = 1;
```

打印一下TestClass o的值
``` c
(gdb) p o
$10 = {_vptr.TestClass = 0x555555557d58 <vtable for TestClass+16>}
```

## 进函数(与VS F11作用相同)
命令: step，可以简写为s

再n两次,这时程序计数器到了第14行
``` c
(gdb) n
13              int b = 2;
(gdb)
14              int c = o.sum( a, b );
```

第14行是调用o.sum函数,我们想进去看看,按s就进到函数里面了
``` c
(gdb) s

Breakpoint 3, TestClass::sum (this=0x7fffffffdfc8, a=1, b=2) at test_class.cpp:10
10              int c = a+b;
```

## 查看函数调用栈
命令: backtrace，可以简写为bt
``` c
(gdb) bt
#0  TestClass::sum (this=0x7fffffffdfc8, a=1, b=2) at test_class.cpp:10
#1  0x0000555555555209 in main (argc=2, argv=0x7fffffffe1d8) at main.cpp:14
```

## 切到上一层栈
命令: up
如果我们已经进到了一个函数,想再看看调用函数的地方,可以用up,切到上一层
``` c
(gdb) up
#1  0x0000555555555209 in main (argc=2, argv=0x7fffffffe1d8) at main.cpp:14
14              int c = o.sum( a, b );
```

## 切到下一层栈
命令: down
然后再切到下一层,用down
``` c
(gdb) down
#0  TestClass::sum (this=0x7fffffffdfc8, a=1, b=2) at test_class.cpp:10
10              int c = a+b;
```

## 运行当前函数到结束(与VS shift+F11效果相同)
命令: finish，可以简写为fin  
在函数里面我们可以一直用n运行到函数结束,也可以直接用finish运行到该函数结束返回,finish结束后还会打出函数的返回值
``` c
(gdb) fin
Run till exit from #0  TestClass::sum (this=0x7fffffffdfc8, a=1, b=2) at test_class.cpp:11
0x0000555555555209 in main (argc=2, argv=0x7fffffffe1d8) at main.cpp:14
14              int c = o.sum( a, b );
Value returned is $19 = 3
```

## 修改变量值
命令: print 变量名=值，可以简写为p 变量名=值
``` c
# 先打印一下a的值看看是多少
(gdb) p a
$4 = 1

# 再修改a的值为2
(gdb) p a=2
$5 = 2

# 重新打印一下看看,值已经被改为2了
(gdb) p a
$6 = 2
(gdb)
```

## 打印所有局部变量
命令: info locals, 可以简写为i locals
``` c
(gdb) i locals
o = {_vptr.TestClass = 0x555555557d58 <vtable for TestClass+16>}
a = 2
b = 0
c = -137657753  #从这里开始是程序还没执行的语句,这些变量还未初始化
buff = "p\002\000\000\000\000\000\000\000\360\377\377\377\377\377\377", '\000' <repeats 40 times>, "\220\377\377\377\377\377\377\377\a\000\000\000\000\000\000\000#\000\000\000\000\000\000\000@\234\340\367\377\177\000\000P\002", '\000' <repeats 14 times>, "\204\224\313\367\377\177", '\000' <repeats 18 times>, "@\002", '\000' <repeats 15 times>, "\034\001", '\000' <repeats 21 times>, "\377\033\001\000\000\000\000\000\220\377\377\377\377\377\377\377y\000\000\000\000\000\000\000\220\377\377\377\377\377\377\377"...
```

当我们按n往后走几步后,再打印一下所有变量,可以看到变量已经有正常的值了
``` c
(gdb) i locals
o = {_vptr.TestClass = 0x555555557d58 <vtable for TestClass+16>}
a = 2
b = 2
c = 4
buff = "sum: 2+2=4\n", '\000' <repeats 244 times>
```

#### 打印数组的前n个元素

#### 打印内存值
命令: x/fmt 内存地址  
fmt为: 重复次数 格式 字节长度

格式: o(octal), x(hex), d(decimal), u(unsigned decimal),
  t(binary), f(float), a(address), i(instruction), c(char), s(string)
  and z(hex, zero padded on the left).

下面输出buff的10个16进制字节
``` c
(gdb) x/10xb buff
0x7fdff0: 0x73    0x75    0x6d    0x3a    0x20    0x31    0x2b    0x32
0x7fdff8: 0x3d    0x33
```

下面输出buff的10个10进制2字节
``` c
(gdb) x/10dh buff
0x7fdff0: 30067   14957   12576   12843   13117   10            0       0
0x7fe000: 0             0
```

下面输出buff的10个4字节
``` c
(gdb) x/10xw buff
0x7fdff0: 0x3a6d7573            0x322b3120      0x000a333d      0x0
0x7fe000: 0x0                   0x0             0x0             0x0
0x7fe010: 0x0                   0x0
```

下面输出buff的10个8字节
``` c
(gdb) x/10xg buff
0x7fdff0: 0x322b31203a6d7573            0x0a333d
0x7fe000: 0x0                           0x0
0x7fe010: 0x0                           0x0
0x7fe020: 0x0                           0x0
0x7fe030: 0x0                           0x0
```

## 断点后继续运行(与VS F5效果相同)
命令: continue，可以简写为c

我们先在第23行下个断点
``` c
(gdb)
23              for( int i=1; i<10; ++i ){
24                      int f = i;
25                      int fv = o.fib( f );
26
27                      int fv2 = o.fib2( f );
28
29                      memset(buff, 0x00, sizeof(buff));
30                      snprintf(buff, sizeof(buff), "fib: %d = %d  %d\n", f, fv, fv2 );
31                      printf("%s", buff);
32              }
(gdb) b 23
```

然后按c,会停在第23行
``` c
(gdb) c
Continuing.
hello dafei
sum: 1+2=3

Breakpoint 2, main (argc=1, argv=0x7fffffffe1f8) at main.cpp:23
23              for( int i=1; i<10; ++i ){
```

## 打断运行
当程序已经在运行的时候，想下个断点，可以在gdb里按ctrl+c打断，这时候再下断点。

## 条件断点
现在我们断在了第23行,这是一个for循环,我们先next进到for循环里面
``` c
(gdb) n
24                      int f = i;
(gdb) l
19              char buff[256]; memset(buff, 0x00, sizeof(buff));
20              snprintf(buff, sizeof(buff), "sum: %d+%d=%d\n", a, b, c);
21              printf("%s", buff);
22
23              for( int i=1; i<10; ++i ){
24                      int f = i;
25                      int fv = o.fib( f );
26
27                      int fv2 = o.fib2( f );
28
(gdb)
```

我们现在想在i==5的时候断到第24行,那就可以下个条件断点  
命令: b if 条件语句
``` c
(gdb) b if i==5
Breakpoint 3 at 0x55555555529b: file main.cpp, line 24.
```

下面我们把断点信息打印出来,可以看到第3个断点加了条件 stop only if i==5
``` c
(gdb) i b
Num     Type           Disp Enb Address            What
1       breakpoint     keep y   0x00005555555551b5 in main(int, char**) at main.cpp:8
        breakpoint already hit 1 time
2       breakpoint     keep y   0x0000555555555284 in main(int, char**) at main.cpp:23
        breakpoint already hit 1 time
3       breakpoint     keep y   0x000055555555529b main.cpp:24
        stop only if i==5
```

接着我们再continue放行断点,程序在运行i==5的时候停在了第24行,表示条件断点起作用了
``` c
(gdb) c
Continuing.
fib: 1 = 1  1
fib: 2 = 1  1
fib: 3 = 2  2
fib: 4 = 3  3

Breakpoint 3, main (argc=1, argv=0x7fffffffe1f8) at main.cpp:24
24                      int f = i;
```

打印一下i的值, i为5
``` c
(gdb) p i
$7 = 5
```

## 内存断点
命令: watch 变量名  
当我们想在一个变量的值改变的时候断下来,可以给这个变量下个内存断点  

我们先让程序运行到第24行
``` c
(gdb) n
sum: 1+2=3
23              for( int i=1; i<10; ++i ){
(gdb)
24                      int f = i;
```

再给迭代变量i下个内存断点,当i的值被改变的时候,程序就会断下来  
下面显示i被监视了
``` c
(gdb) watch i
Hardware watchpoint 7: i
```

显示一下当前所有断点,7号为内存断点
``` c
(gdb) i b
Num     Type           Disp Enb Address            What
6       breakpoint     keep y   0x0000555555555269 in main(int, char**) at main.cpp:21
        breakpoint already hit 1 time
7       hw watchpoint  keep y                      i
        breakpoint already hit 1 time
```

按c运行程序后,程序被断了下来,显示Hardware watchpoint 7:i
``` c
(gdb) c
Continuing.
fib: 1 = 1  1

Hardware watchpoint 7: i

Old value = 1
New value = 2
0x0000555555555356 in main (argc=1, argv=0x7fffffffe1f8) at main.cpp:23
23              for( int i=1; i<10; ++i ){
```

## 跳出循环体
命令: until
如果一直for要循环很多次,我们可以用until让程序跑到for结束后断下来
* 只有当程序第二次运行到for语句开始的时候,输入until,才会起作用


## 调用函数
当我们在调试的时候想执行某个函数,可以直接call 函数
``` c
(gdb) call o.fib(10)
$3 = 55
```

## 附加到已运行的程序
如果程序已经在运行的时候我们想用gdb附加到程序上调试，可以用gdb attach 进程号  

先用ps看一下程序的进程号
``` c
bash$ ps -ef | grep main
# 5762 是main运行后的进程号
dafei      5762  3139  0 08:40 pts/0    00:00:00 ./main
```

把gdb附加到该进程上  
``` c 
bash$ gdb attach 5762

# 下面是gdb附加时候的输出
GNU gdb (Ubuntu 7.11.1-0ubuntu1~16.5) 7.11.1
Copyright (C) 2016 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
<http://www.gnu.org/software/gdb/documentation/>.
For help, type "help".
Type "apropos word" to search for commands related to "word"...
attach: No such file or directory.
Attaching to process 5762
Reading symbols from /home/dafei/cpp/test/main...done.
Reading symbols from /lib/x86_64-linux-gnu/libc.so.6...Reading symbols from /usr/lib/debug//lib/x86_64-linux-gnu/libc-2.23.so...done.
done.
Reading symbols from /lib64/ld-linux-x86-64.so.2...Reading symbols from /usr/lib/debug//lib/x86_64-linux-gnu/ld-2.23.so...done.
done.
0x00007fe964c47260 in __read_nocancel () at ../sysdeps/unix/syscall-template.S:84
84	../sysdeps/unix/syscall-template.S: No such file or directory.
(gdb) #现在就可以下断点了
```
