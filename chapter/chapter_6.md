# linux编程入门(六)-编写Makefile文件

[toc]
当我们写好程序后，需要通过编译，链接后生成可执行文件，这个可执行文件也就是我们通常说的程序。  
那么什么是编译，什么是链接，又为啥要编译，链接呢？  
因为程序设计语言五花八门，说啥的都有，比如c，c\++等，但是这些语言都是相对于程序员来说好懂，计算机看不懂。我计算机就只认识一种语言，就是二进制01，这样就需要把高级语言翻译成二进制。这个编译的过程就好比是翻译，这样计算机就可以看懂了，她就知道该做什么了。  
在写代码的过程中会写出很多个代码文件，比如cpp文件，把每个代码文件都编译完，会生成一个obj文件，这里面全是二进制的符号。但这些obj不是一个完整的程序，它们只是程序的一部分，把这些个符号都拼接到一个程序文件里，才是一个完整的可执行程序，这个拼接的过程就是链接。  
闲话少说，上车。  

#### 预备知识
Makefile文件里描述的是编译的时候依赖关系，宏定义，编译参数，链接生成的程序名字等等。  
等Makefile文件写好后，需要用make程序来执行Makefile。  
所以需要先安装make程序  
``` c
bash$ sudo apt install make -y
```

先看一个完整的Makefile示例吧,下面的Makefile会把一个main.cpp或main.c编译成一个main程序:
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

#HEADER += -I./

#LIBS    += -lrt
#LIBS    += -pthread

OBJECT := main.o \

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

下面我们一步一步来实现这个Makefile,看看它在做什么。  

我们先看个最简单的Makefile的例子,把下面的内容保存到文件Makefile里:  
``` c
# 最简单的Makefile
hello:
    @echo "hello Makefile"
```

然后make一下,如果make的时候当前目录有叫Makefile的文件，默认执行这个Makefile，如果需要指定其他文件名，需要用make -f
``` c
bash$ make
# 下面是Makefile的输出
hello Makefile

# 或者指定文件名
bash$ make -f Makefile
hello Makefile
```

上面Makefile文件里的hello表示目标,这个目标的执行动作是打印一行字符串"hello Makefile"。  
我们可以有多个目标,修改Makefile，加入目标hello2:  
``` c
# 多目标的Makefile

# 目标hello, 输出hello Makefile
hello:
    @echo "hello Makefile"
    
# 目标hello2，输出hello dafei
hello2:
    @echo "hello dafei"
```

make一下看看效果
``` c
# 默认执行的是第一个目标: hello
# 也就是哪个目标最先出现，
# 就执行哪个目标
bash$ make
hello Makefile

# 我们也可以指定执行哪个目标
# 比如这里指定执行hello2
bash$ make hello2
hello dafei
```

每个目标可以有相应的依赖项，看下面的例子
``` c
# hello需要依赖ready
hello : ready
    @echo "hello Makefile"
    
hello2 :
    @echo "hello dafei"

# 新增一个目标,输出准备
ready :
    @echo "i'm ready"
```

再make一下看看
``` c
# 默认目标是hello
# 由于hello依赖了ready，所以ready也执行了
bash$ make
i'm ready
hello Makefile

# 指定目标hello2
bash$ make hello2
hello dafei

# 指定目标ready
bash$ make ready
i'm ready
```

目标可以执行一个空的动作，修改Makefile为下面的内容:
``` c
# 定义一个新的目标all,依赖hello和hello2
all : hello hello2

# hello依赖ready
hello : ready
    @echo "hello Makefile"
    
hello2 :
    @echo "hello dafei"

ready :
    @echo "i'm ready"
```

make一下看看效果
``` c
bash$ make
#输出结果如下
i'm ready
hello Makefile
hello dafei
```

来一步一步分解上面Makefile的执行过程
* Makefile的第一个目标是all，所以默认执行了all目标
* 目标all依赖hello和hello2，按顺序执行依赖项
    * 先执行第一个依赖的目标hello
        * hello又依赖了ready
            * 所以执行ready
                * ready没有依赖项
                * 直接执行ready的动作
                * ready的动作是echo "i'm ready"，所以输出i'm ready
            * hello所有依赖项都执行完成的时候，再执行hello自己的动作
            * hello的动作是echo "hello Makefile"，所以输出hello Makefile
    * 再执行第二个依赖的目标hello2
        * hello2没有依赖项
            * 所以执行hello2的动作
            * hello2的动作为echo "hello dafei"，所以输出hello dafei

通过上面的步骤分解，我们可以看出Makefile实际上非常简单，就是一个个目标与依赖结合起来的有序的解析过程。  
了解了Makefile的执行过程，我们只要把目标的动作改为编译和链接就可以了，下面我们看执行一个编译动作的命令。

#### 进入正题

linux下编译c\++代码用g++，c\++代码的文件后缀为.cpp或.cc，编译c代码用gcc，c代码的文件后缀为.c。  
链接程序也用g++。  
g++的常用参数说明:  
* -c 表示编译代码  
* -o 表示指定生成的文件名
* -g 表示编译时候加入调试符号信息，debug时候需要这些信息
* -I (大写i)表示设置头文件路径，-I./表示头文件路径为./
* -Wall 表示输出所有警告信息
* -D 表示设置宏定义,-DDEBUG表示编译debug版本,-DNDEBUG表示编译release版本
* -O 表示编译时候的优化级别，有4个级别-O0,-O1,-O2 -O3，-O0表示不优化,-O3表示最高优化级别
* -shared 表示生成动态库
* -L 指定库路径，如-L.表示库路径为当前目录
* -l (小写L)指定库名，如-lc表示引用libc.so  

新装的ubuntu可能没有g++,可以先在bash里输入g++ -v试一下,如果没有安装会提示先安装，这时候跟着提示apt install g++就可以了。  
安装gcc也一样。

先准备一个main.cpp
``` c
#include <stdio.h>

int main(int, char**){
    printf("hello dafei\n");
    return 0;
}
```

看一下最简单的编译.cpp代码的Makefile怎么写  
``` c
  1 
  2 main : main.o
  3     g++ -o $@ $^
  4 
  5 .cpp.o:
  6     g++ -c -o $@ $<
```

解释一下上面的Makefile:
* 第2行main表示一个目标名为main，依赖为main.o，.o是代码编译后生成的obj文件
* 第3行表示目标main的执行动作，  
  就是执行链接程序的命令，  
  该动作用g\++链接程序，-o $@表示指定链接后生成的程序名为$@，$@是个转义符号，表示该动作的"目标名"，也就是main，$^也是个转义符号，表示该目标的所有依赖，这里的依赖只有一个main.o。
  所以该句命令展开就是 g\++ -o main main.o
  
* 第5行.cpp.o表示这是一个目标,作用是把.cpp文件编译为.o文件
* 第6行是该目标的具体编译命令，-c表示编译, -o $@表示指定生成文件名，$<表示第一个依赖名

make一下，看看效果
``` c
bash$ make
g++ -c -o main.o main.cpp
g++ -o main main.o
```

再执行一下生成的main程序
``` c
bash$ ./main
# 输出了hello dafei,表示程序运行成功。
hello dafei
```

在Makefile里我们可以echo加入各种调试信息，比如可以把$@,$^,$<这些变量都打印出来，看看都 它们是什么。
继续修改Makefile，下面加入了两行echo。
又新加了一个目标: clean，这个目标的作用是删除编译生成的.o文件和main程序，  
这样我们想重新编译的时候就可以执行命令: make clean; make  
或者 make clean && make 区别在于这句只有当make clean执行成功的时候才会执行make
``` c
  1
  2 main : main.o
  3     @echo "linking $@ dependences $^"
  4     g++ -o $@ $^
  5
  6 .cpp.o:
  7     @echo "compiling $< => $@"
  8     g++ -c -o $@ $<
  9
 10 clean:
 11     rm -rf *.o main

```
执行一下看看效果
``` c
bash$ make clean; make
rm -rf *.o main                    #这是clean的命令
compiling main.cpp => main.o       #这是第7行@echo "compiling $< => $@"展开后的输出
g++ -c -o main.o main.cpp          #这是第8行编译命令
linking main dependences main.o    #这是第3行echo输出的
g++ -o main main.o                 #这是第4行g++链接输出的
```

> 小知识  
Makefile里的echo和rm前面带了@表示不要打印执行该命令时候命令本身的输出，比如  
rm -rf *.o main在执行的时候会输出这句命令"rm -rf *.o main" 如果把rm改为@rm，  
再make clean的时候就不会输出"rm -rf *.o main"命令本身了。  
看下面的测试:

``` c
#Makefile里的clean
 10 clean:
 11     rm -rf *.o main

bash$ make clean
# 这是clean不带@的rm
rm -rf *.o main

#Makefile里的clean
 10 clean:
 11     @rm -rf *.o main
 
bash$ make clean
# 这是clean动作为@rm的效果,什么也没有输出
```

看到这里基本上再回头看开头的完整Makefile就能看懂了,需要重点说的是编译的参数,头文件路径和库引用的写法,下面我们一步一步写上注释
``` c
# 这句是链接时候的命令,在g++前面加入了@echo linking $@
# 这样在链接时候就会先输出linking xxx,
# 这行直接写g++也是没有任何问题的
LINK    = @echo linking $@ && g++ 

# 编译c++代码时候用的,一样会显示compiling xxx
GCC     = @echo compiling $@ && g++ 

# 编译c代码时候用gcc
GC      = @echo compiling $@ && gcc 

# 生成静态库时候用ar命令
AR      = @echo generating static library $@ && ar crv

# 这是编译时候的参数设置,下面-g表示编译时候加入调试信息,
# -DDEBUG表示编译debug版本
# -W -Wall表示输出所有警告
# -fPIC是生成dll时候用的
FLAGS   = -g -DDEBUG -W -Wall -fPIC
GCCFLAGS = 
DEFINES = 

# 这里指出头文件的目录为./
HEADER  = -I./

# 需要引用的库文件
LIBS    = 
LINKFLAGS =

# 更多头文件可以用 += 加进来
#HEADER += -I./

# 如果需要librt.so,就把-lrt加进来
#LIBS    += -lrt

# 如果需要写多线程程序,就需要引用-pthread
#LIBS    += -pthread

# 这里是主要需要修改的地方,每一个.c或.cpp对应于这里的一项,
# 如main.cpp对应于main.o
# 多个.o可以用空格分开,也可以像下面这样用"\"换行,然后写在新一行
OBJECT := main.o \

# 下面举个例子,这里编译表示两个代码文件
# OBJECT := main.o \
#           other.o

BIN_PATH = ./

TARGET = main

# 链接用的,可以看到后面加了$(LIBS),因为只有链接时候才需要引用库文件
$(TARGET) : $(OBJECT) 
    $(LINK) $(FLAGS) $(LINKFLAGS) -o $@ $^ $(LIBS)

# 编译cpp代码用这个目标
.cpp.o:
    $(GCC) -c $(HEADER) $(FLAGS) $(GCCFLAGS) -fpermissive -o $@ $<

# 编译c代码用这个
.c.o:
    $(GC) -c $(HEADER) $(FLAGS) -fpermissive -o $@ $<

# 把生成的$(TARGET)拷贝到$(BIN_PATH)下
install: $(TARGET)
    cp $(TARGET) $(BIN_PATH)

clean:
    rm -rf $(TARGET) *.o *.so *.a
```

> 小知识  
上面引用多线程库的时候为什么用-pthread而不用-lpthread?感兴趣的小伙伴可以看[这里](https://chaoslawful.iteye.com/blog/568602)


#### 生成动态链接库  

linux动态链接库的后缀为 .so，生成动态链接库也比windows要方便的多，只要在link的时候加上-shared参数就可以了，下面我们看个例子。

先来看一下完整测试的目录结构，最上层目录是test_makefile_so
``` c
test_makefile_so
├── Makefile      #这个是总的Makefile，管理所有子目录的Makefile
├── bin
│   ├── libfun.so
│   └── main
└── src
    ├── Makefile
    ├── fun
    │   ├── Makefile
    │   ├── fun.cpp
    │   └── fun.h
    └── main.cpp

3 directories, 8 files
```

main.cpp的内容
``` c
#include <stdio.h>
#include "fun.h"

int main(int, char**){
    printf("hello so\n");

    int a = 1;
    int b = 2;
    int c = sum( a, b );
    printf( "sum: %d + %d = %d\n", a, b, c );
    return 0;
}
```

src/Makefile的内容
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

HEADER += -I./fun

#LIBS    += -lrt
#LIBS    += -pthread
#这里表示链接的时候从bin目录下找libfun.so
LIBS    += -L../bin -lfun

OBJECT := main.o \

#这里加了bin的相对路径,编完的main会install到bin目录下
BIN_PATH = ../bin/

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
    rm -rf $(TARGET) *.o *.so
```

fun.h的内容
``` c
#ifndef __fun_h__
#define __fun_h__

int sum(int a, int b); 

#endif//__fun_h__
```

fun.cpp的内容
``` c
#include "fun.h"

int sum(int a, int b){
    return a+b;
}
```

fun/Makefile的内容
``` c
LINK    = @echo linking $@ && g++ 
GCC     = @echo compiling $@ && g++ 
GC      = @echo compiling $@ && gcc 
FLAGS   = -g -DDEBUG -W -Wall -fPIC
GCCFLAGS = 
DEFINES = 
HEADER  = -I./
LIBS    = 

#修改的地方1: 这里加了-shared，表示生成动态库
LINKFLAGS = -shared

#HEADER += -I./

#LIBS    += -lrt
#LIBS    += -pthread

OBJECT := fun.o \    #修改的地方2: 表示编译fun.cpp

#修改的地方3: 指出了bin的相对路径
BIN_PATH = ../../bin/

#修改的地方3，生成的文件名叫libfun.so，动态库一般以lib为前缀
TARGET = libfun.so

$(TARGET) : $(OBJECT) 
    $(LINK) $(FLAGS) $(LINKFLAGS) -o $@ $^ $(LIBS)

.cpp.o:
    $(GCC) -c $(HEADER) $(FLAGS) $(GCCFLAGS) -fpermissive -o $@ $<

.c.o:
    $(GC) -c $(HEADER) $(FLAGS) -fpermissive -o $@ $<

install: $(TARGET)
    cp $(TARGET) $(BIN_PATH)

clean:
    rm -rf $(TARGET) *.o *.so
```

先编译动态库libfun.so，因为main程序要依赖libfun.so。  
在fun目录下先make install一下，会生成libfun.so，并且自动拷贝到bin目录下
``` c
bash$ cd fun

bash$ make install
compiling fun.o
linking libfun.so
cp libfun.so ../../bin/
```

切换到src目录下，再编译main程序
``` c
bash$ make clean; make install
rm -rf main *.o *.so
compiling main.o
linking main
cp main ../bin/
```

然后切到bin目录下，运行main程序
``` c
bash$ cd ../bin

# bin目录下现在有两个文件,libfun.so和main
bash$ ls
libfun.so	main

# 运行main程序
bash$ ./main
mac:bin tpf$ ./main 
hello so
sum: 1 + 2 = 3   #这行是由libfun.so里的函数执行的
```

#### 使用Makefile执行工程下所有子目录的Makefile
如果我们有个大工程，有很多个动态库，每次都要进到相应的目录编译也太麻烦了。我们可以在test_makefile_so目录下写一个总的Makefile，用这个Makefile执行所有的子Makefile，这样想全总重新编译的时候运行这一个Makefile就可以了。  

还是先看完整的Makefile吧。

``` c
test_makefile_so/
├── Makefile        #这个是新加的Makefile，管理所有的子目录
├── bin
│   ├── libfun.so
│   └── main
└── src
    ├── Makefile
    ├── fun
    │   ├── Makefile
    │   ├── fun.cpp
    │   └── fun.h
    └── main.cpp

3 directories, 8 files
```

test_makefile_so/Makefile的内容
``` c
# 所有包含Makefile的子目录都加到这里

SUBDIRS = src/fun  #注意这里先编库
SUBDIRS += src     #最后编程序，因为程序要依赖库


BIN_PATH = ./bin

# 定义一个函数,遍历所有的$(SUBDIRS), $1是参数
define make_subdir
@for i in $(SUBDIRS); do\
(cd $$i && make $1) \
done;
endef


# 默认的目标为编译所有子目录
ALL:
    $(shell if ! test -d $(BIN_PATH); then mkdir $(BIN_PATH); fi;)
    $(call make_subdir)

# 清空所有子目录
clean:
    $(shell if test -d $(BIN_PATH); then rm -rf $(BIN_PATH); fi;)
    $(call make_subdir, clean)

# 把子目录里生成的程序或so都拷到$(BIN_PATH)目录下
install:
    $(shell if ! test -d $(BIN_PATH); then mkdir $(BIN_PATH); fi;)
    $(call make_subdir, install)
```

在test_makefile_so目录里运行一下，看看效果  
下面可以看到，src和fun目录下的Makefile都被执行了，而且被安装到bin目录下了，整个世界都清静了。  
这样是完全可以管理一个大型工程的。
``` c
bash$ make clean; make install
rm -rf libfun.so *.o *.so
rm -rf main *.o *.so
compiling fun.o
linking libfun.so
cp libfun.so ../../bin/
compiling main.o
linking main
cp main ../bin/
```

#### 生成静态库
生成静态库和动态库差不多，区别是静态库是链接时候用一下，运行期就不用了。动态库是链接和运行期都要用。  
静态库里就是一堆.o文件的集合，在最后链接的时候把这些.o一起链到程序里，所以生成的程序会非常大。  
而动态库只是把一些符号信息链到程序里，最后运行的时候再根据符号信息从so里找相应的函数，所以使用动态库的程序非常小。  
动态库还可以方便更新，以前我们有个项目就是用动态库做插件，更新游戏服务器的时候更新相应的so就可以了。  
如果想在程序运行时加载so，可以使用dlopen函数。感兴趣的同学可以自己研究一下。

生成静态库用归档命令ar,如下面的命令最会后生成libtest.a
``` c
bash$ ar crv libtest.a fun.o fun2.o  
```
* c 如果需要生成新的库文件，不要警告
* r 代替库中现有的文件或者插入新的文件
* v 输出详细信息

下面我们看下生成静态库的Makefile。  
还是先看一下完整的目录结构:  
``` c
test_makefile_so
├── Makefile
├── bin
│   ├── libfun.so
│   ├── libstatic_fun.a
│   └── main
└── src
    ├── Makefile
    ├── fun
    │   ├── Makefile
    │   ├── fun.cpp
    │   └── fun.h
    ├── main.cpp
    └── static_fun           #这个目录是新加的,其实就是用fun目录拷贝过来的
        ├── Makefile
        ├── static_fun.cpp   #把fun.cpp改名为static_fun.cpp了
        └── static_fun.h     #把fun.h改名为static_fun.h了

4 directories, 12 files
```

然后static_fun/Makefile里的内容是:
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

#HEADER += -I./

#LIBS    += -lrt
#LIBS    += -pthread

OBJECT := static_fun.o \       #修改1. 把这行改为static_fun.o了

BIN_PATH = ../../bin/

TARGET = libstatic_fun.a       #修改2. 静态库的后缀是.a

$(TARGET) : $(OBJECT) 
#   $(LINK) $(FLAGS) $(LINKFLAGS) -o $@ $^ $(LIBS)    #修改3. 注掉了这行,静态库不用链接
    $(AR) $@ $^                #修改4. 加了这行，生成静态库用的是打包命令

.cpp.o:
    $(GCC) -c $(HEADER) $(FLAGS) $(GCCFLAGS) -fpermissive -o $@ $<

.c.o:
    $(GC) -c $(HEADER) $(FLAGS) -fpermissive -o $@ $<

install: $(TARGET)
    cp $(TARGET) $(BIN_PATH)

clean:
    rm -rf $(TARGET) *.o *.so *.a
```

static_fun.h的内容:
``` c
#ifndef __static_fun_h__
#define __static_fun_h__

int mul(int a, int b); 

#endif//__static_fun_h__
```

static_fun.cpp的内容
``` c
#include "static_fun.h"

int mul(int a, int b){
    return a*b;
}
```

在static_fun目录下make看看效果
``` c
bash$ make clean; make install
rm -rf libstatic_fun.a *.o *.so *.a
compiling static_fun.o
generating static library libstatic_fun.a
a - static_fun.o
cp libstatic_fun.a ../../bin/
```

下面是src/main.cpp里需要引用libstatic_fun.a的函数
``` c
#include <stdio.h>

//这里写fun.h而不写./fun/fun.h的原因是在Makefile里加了-I./fun
#include "fun.h"

//这里写./static_fun/static_fun.h的原因是Makefile里没有加-I./static_fun，所以需要指出相对路径
#include "static_fun/static_fun.h" 

int main(int, char**){
    printf("hello so\n");

    //这里用的是动态库的函数
    int a = 1;
    int b = 2;
    int c = sum( a, b );
    printf( "sum: %d + %d = %d\n", a, b, c );

    //这里用的是静态库的函数
    printf("hello static lib\n");
    int d = mul( a, b );
    printf( "mul: %d * %d = %d\n", a, b, d );
    return 0;
}
```

再看一下src/Makefile的内容
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

HEADER += -I./fun
#HEADER += -I./static_fun    #修改1. 本来加了这行，为了看出加不加头文件目录的效果，把这行特意屏掉了

#LIBS    += -lrt
#LIBS    += -pthread
LIBS    += -L../bin -lfun
LIBS    += -L../bin -lstatic_fun   #修改2. 加了这行，引用静态库libstatic_fun.a

OBJECT := main.o \

BIN_PATH = ../bin/

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

然后在src下make一下，编译main
``` c
bash$ make clean; make install
rm -rf main *.o *.so *.a
compiling main.o
linking main
cp main ../bin/
```

进到bin目录下，运行main程序
``` c
bash$ cd ../bin

bash$ ./main 
hello so
sum: 1 + 2 = 3
hello static lib
mul: 1 * 2 = 2
```

最后不要忘了把static_fun目录加到总的Makefile里
``` c
# 所有包含Makefile的子目录都加到这里

SUBDIRS = src/fun  #先编库
SUBDIRS += src/static_fun   #修改1. 加了这行
SUBDIRS += src     #最后编程序

BIN_PATH = ./bin

# 定义一个函数,遍历所有的$(SUBDIRS), $1是参数
define make_subdir
@for i in $(SUBDIRS); do\
(cd $$i && make $1) \
done;
endef


# 默认的目标为编译所有子目录
ALL:
    $(shell if ! test -d $(BIN_PATH); then mkdir $(BIN_PATH); fi;)
    $(call make_subdir)

# 清空所有子目录
clean:
    $(shell if test -d $(BIN_PATH); then rm -rf $(BIN_PATH); fi;)
    $(call make_subdir, clean)

# 把子目录里生成的程序或so都拷到$(BIN_PATH)目录下
install:
    $(shell if ! test -d $(BIN_PATH); then mkdir $(BIN_PATH); fi;)
    $(call make_subdir, install)
```

再使用总的Makefile运行一下，爽得很
``` c
bash$ make clean; make install
rm -rf libfun.so *.o *.so *.a
rm -rf libstatic_fun.a *.o *.so *.a
rm -rf main *.o *.so *.a
compiling fun.o
linking libfun.so
cp libfun.so ../../bin/
compiling static_fun.o
generating static library libstatic_fun.a
a - static_fun.o
cp libstatic_fun.a ../../bin/
compiling main.o
linking main
cp main ../bin/
```
