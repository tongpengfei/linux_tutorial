# linux编程入门(五)-使用vim编写程序

编写程序大家可以自由选择一种编辑器，常用的可以选vim或emacs。因为我用vim，所以主要介绍一下vim在编写程序时候的用法，基本用法可以看[这里](https://www.jianshu.com/p/f706cb3c7d0e?utm_campaign=hugo&utm_medium=reader_share&utm_content=note&utm_source=weixin-friends&from=singlemessage&isappinstalled=0)。  

[toc]

## 按ctrl+n补全
这里补全的前提是被补全的单词已经出现过，比如在代码里出现过printf，这时我输入pri，再输入ctrl+n就会出现printf的补全候选列表  
* 再按ctrl+n向下移动候选列表光标
* ctrl+p向上移动光标


## vim里搜索当前文件中的关键字

* 把光标移到关键字的单词上，按shift+8  
** 按n切到下一个  
** shift+n切到上一个

* 或者按ESC退出编辑模式，按/,再输入关键字  
** 同样是按n和shift+n上下切换
``` c
~                                    
[Quickfix List]                         2,1            All
/main    #这样表示搜索main，回车确认
```

## 打开cope窗口
进入命令模式，输入cope，会出现一个窗口，cope5表示窗口高5行，默认是10行  
该窗口可以用于显示命令结果，比如搜过结果，make编译结果，如果编译有错，可以在这里直接跳到有错的代码处。
``` c
 11     char a;
 12     int b;
 13     short c;
 14     void func1(){
main.cpp        下面是cope窗口          1,1            Top
  1     
~                                        
~                                            
~                                            
~                                            
[Quickfix List]                         0,0-1          All
:cope5
```

## 关闭cope窗口
按ctrl+w+w 把焦点切到cope窗口上，再在命令模式下输入:q就可以退出当前窗口了


## vim里搜索其他文件中的关键字
命令模式下，输入:grep main *.cpp  
表示当前目录的所有cpp文件里搜过main关键字
``` c
 20 int main(int, char**){
main.cpp        下面是cope窗口          20,1           24%
  1 main.cpp|20| int main(int, char**){             #cope里显示出了搜过结果
  2 test_sort.cpp|15| int main(int, char**)         #共搜索出来两行结果
~                                            
~                                            
~                                            
[Quickfix List]                         1,1            All
:grep main *.cpp
```

## 自动跳到搜过结果的代码
多窗口间切换用ctrl+w+w  
假如当前vim里打开了cope窗口，并且焦点在vim编辑区，这时候想把光标移到cope窗口，可以ctrl+连点两下w，光标就会移到cope窗口，在cope窗口同样按j，k上下移动光标，选中一行搜索结果，按回车，编辑区就会跳到该结果的代码处。


## 打开Tlist窖口  
Tlist窗口可以用来查看代码，该窗口会把函数签名和打开过的文件名都列出来，可以快速的跳到该文件或函数处  
命令模式下输入: Tlist  
``` c
  1 #include <stdio.h>                   |   # 这里就是Tlist窗口
  2 #include <string.h>                  |-  main.cpp (/home/dafei/cpp/ga
  3 #include "test_class.h"              |   
  4                                      |-  test_class.cpp (/home/dafei/
  5 void test_fun(){                     |   
  6 }                                    |-  test_class.h (/home/dafei/cp
  7                                      |   
  8 int main(int argc, char** argv){     |   ~                          
  9     (void)argc;                      |   ~                          
 10     (void)argv;                      |   ~                          
 11                                      |   ~                          
 12     printf("hello dafei\n");         |   ~                          
 13                                      |   ~                          
 14     TestClass o;                     |   ~                          
 15     int a = 1;                       |   ~                          
 16     int b = 2;                       |   ~                          
 17     int c = o.sum( a, b );           |   ~                          
 18                                      |   ~                          
 19     char buff[256]; memset(buff, 0x00|   ~                          
    , sizeof(buff));                     |   ~                          
main.cpp               1,1            Top __Tag_List__   3,1         Bot
:Tlist
```

## 切到另一个文件
可以用命令:e 文件名 切到另一个文件

## 打开当前目录
命令模式: e .  
当我们不记得文件名的时候，可以打开当前目录，选择要打开的文件
``` c
" ===========================================================================
" Netrw Directory Listing                                        (netrw v162)
"   /home/dafei/test_gdb
"   Sorted by      name
"   Sort sequence: [\/]$,\<core\%(\.\d\+\)\=\>,\.h$,\.c$,\.cpp$,\~\=\*$,*,\.o
"   Quick Help: <F1>:help  -:go up dir  D:delete  R:rename  s:sort-by  x:spec
" ===========================================================================
../
./
test_class.h
main.cpp
test_class.cpp
main*
Makefile
main.o
                                                           1,1           Top
```

## 在头文件与cpp间来回切换
假如我们正在编辑cpp，想切到头文件，可以用命令:e切过去，这时候如果想再切到cpp文件，可以用ctrl+shift+6快速切到上一次编辑的文件，这样按ctrl+shift+6可以快速的在头文件和cpp间来回切换  

## 退出所有窗口
当我们打开了Tlist, cope窗口，这时候想退出vim，命令模式:q 只会退出当前窗口, 需要用:qa 关闭所有窗口  



