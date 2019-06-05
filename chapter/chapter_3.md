# linux编程入门(三)-编写shell脚本

[toc]

如果是短的命令我们可以手动输入，但如果命令很长一串串，就需要在shell脚本里执行了，shell脚本的功能非常强大，可以执行顺序，条件，循环语句，还可以定义函数，和编程一样。

### 基础知识

#### 创建一个shell脚本
* shell脚本的后缀名为 .sh
* 脚本的第一行固定为#!/bin/bash，表示用/bin/bash执行这个脚本
* 脚本用chmod +x获得可执行权限后，可以用./脚本名.sh的方式执行
* 如果没有可执行权限，可以用sh ./脚本名.sh或bash ./脚本名.sh的方式执行  


下面我们来建一个 test.sh，里面内容为
``` c
#!/bin/bash

echo "hello dafei"
```

执行一下看看效果
``` c
bash$ sh ./test.sh
hello dafei

bash$ bash ./test.sh
hello dafei
```

给test.sh加上可执行权限后再执行
``` c
bash$ chmod +x test.sh

bash$ ./test.sh
hello dafei
```

#### 定义一个变量
``` c
#定义一个变量，变量的值是一个字符串
STR="this is a string"

echo $STR
```
运行结果
``` c
bash$ bash ./test.sh
# ... 忽略之前的打印，只写出这次新加的
this is a string
```

#### 把一个命令的结果赋给变量
``` c
# 这里把pwd的结果赋给了cur_dir
# 注意pwd是的`符号(〜)那个键括起来的
cur_dir=`pwd`
echo $cur_dir

# 把路径里的'/'换成空格
str=`echo $cur_dir | sed 's/\// /g'`
echo "处理后: $str"
```
运行结果
``` c
bash$ ./test.sh 
/Users/dafei/samples/test_shell
处理后:  Users dafei samples test_shell
```

#### 用for打印字符串中的单个单词
``` c
STR="this is a string"

for i in $STR; do
    echo $i
done
```
运行结果
``` c
bash$ ./test.sh 
this
is
a
string
```

#### 变量自增
``` c
STR="this is a string"

# 先定义一个变量
index=0
for i in $STR; do
    echo "第 $index 个单词是 $i"
    
    #index自增
    index=$(($index+1))  
done
```
运行结果
``` c
bash$ ./test.sh 
第 0 个单词是 this
第 1 个单词是 is
第 2 个单词是 a
第 3 个单词是 string
```

#### 字符串比较
``` c
MODE=debug

if [ "$MODE" == "debug" ]; then
    echo "debug"
else
    echo "release"
fi
```
运行结果
``` c
bash$ ./test.sh 
debug
```

> 注意!!!  
 上面的"$MODE"是用引号括起来的，如果不用引号，  
 当MODE=的时候，if展开就会变成  
 if [  == "debug" ]; then   
 这样就出错了


#### 数字比较
``` c
for (( i=-1; i<2; i++ )); do
    if (( $i > 0 )); then
        echo "$i > 0";
    elif (( $i < 0 )); then
        echo "$i < 0";
    elif (( $i == 0 )); then
        echo "$i == 0";
    fi  
done
```
运行结果
``` c
bash$ ./test.sh 
-1 < 0
0 == 0
1 > 0
```

#### 判断文件是否存在
``` c
file_name=a.txt
if test -f "$file_name"; then
    echo "$file_name 存在"
else
    echo "$file_name 不存在"
fi
```
运行结果
``` c
bash$ ./test.sh 
a.txt 不存在
```

#### 判断文件夹是否存在
``` c
dir_name=dir
if test -d "$dir_name"; then
    echo "$dir_name 存在"
else
    echo "$dir_name 不存在"
fi
```
运行结果
``` c
bash$ ./test.sh 
dir 不存在
```

#### 定义一个函数
``` c
# test_func是函数名, 
# 参数用$1,$2的格式获取
# $1表示第1个参数
test_func(){
    echo "call test_func $1 $2"
    return 0
}

# 调用函数
test_func 1, 2
test_func 2, 3
```

运行结果
``` c
bash$ ./test.sh
call test_func 1, 2
call test_func 2, 3
```

#### case多条件分支
``` c
test_case(){
    case $1 in
    1)  
        echo "$1 该吃饭了"
    ;;  
    2)  
        echo "$1 正在吃饭"
    ;;  
    3)  
        echo "$1 吃完了"
    ;;  
    *)  
        echo "$1 你说啥?"
    ;;  
    esac
}

# 调用函数
test_case 1
test_case 2
test_case 3
test_case 4
```

运行结果
``` c
bash$ ./test.sh
1 该吃饭了
2 正在吃饭
3 吃完了
4 你说啥?
```



### 综合练习
下面我们要实现一个装机脚本，比如每次我们装了个新机器的时候，需要安装大量的软件，手动一个一个装也太麻烦了，这时候我们可以定制一个装机脚本，就叫install_ubtuntu.sh吧，shell脚本的后缀名为 .sh。  

install_ubuntu.sh脚本的需求是:  
* 脚本执行完成的时候，所有需要的软件都被安装好了
* 安装的时候，需要显示出被安装的是第几个软件   

脚本需要接收的参数:  
* -l 显示所有可安装的软件名列表
* -y 表示静默安装，不要提示
* -h 显示该帮助

好，下面我们一步一步实现这个脚本。  

* 新建install_ubuntu.sh脚本文件
``` c
bash$ touch install_ubuntu.sh
```

* 用vim编辑install_ubuntu.sh，写入内容:
``` c
#!/bin/bash        #这行指出用bash执行该文件

echo "hello dafei";
```

* 用chmod给install_ubuntu.sh一个可执行权限
``` c
bash$ chmod +x install_ubuntu.sh
```

* 运行一下，看看效果
``` c
bash$ ./install_ubuntu.sh
hello dafei

# 当脚本没有可执行权限时候，也可以指定用sh执行脚本
bash$ sh ./install_ubuntu.sh
hello dafei

或者用bash执行
bash$ bash ./install_ubuntu.sh
hello dafei
```

* 获取参数的个数，和参数列表
``` c
#!/bin/bash

#下面两行是取参数个数和参数列表，类似于c语言里的int main(int argc, char** argv)
ARGC=$#
ARGV=$@

echo "共有 $ARGC 个参数"
echo "参数列表是: $ARGV"
```


* 下面是完整的脚本，代码很简单，主要是看一下shell的格式
``` c
#!/bin/bash

#需要安装的软件列表，各软件名之间用空隔隔开
SOFT_LIST="vim"
SOFT_LIST+=" gcc"
SOFT_LIST+=" g++"
SOFT_LIST+=" gdb"

#下面两行是取参数个数和参数列表，类似于c语言里的int main(int argc, char** argv)
ARGC=$#

# 如果没有参数，默认显示帮助，然后退出脚本
if (( $ARGC < 1 )); then
    help="请输入运行参数:"
    help+="\n-l 显示所有可安装的软件名列表"
    help+="\n-y 表示静默安装，不要提示"
    help+="\n-h 显示该帮助"
    echo -e $help
    exit -1
fi

ARGV=$@
echo "共 $ARGC 个参数,参数列表是: $ARGV"

#挨个打印出参数
index=1
is_silence=false
for arg in $ARGV; do
    echo "第 $index 个参数是: $arg"
    index=$(($index+1))

    # 比如参数是否为-y,如果是
    if [ "$arg" == "-y" ]; then
        is_silence=true
    fi
done

index=1
for soft in $SOFT_LIST; do
    echo "准备安装第 $index 个软件 $soft"

    if $is_silence; then
        # sudo apt install $soft -y
        # 这里因为是测试，所以只echo一下命令
        echo "sudo apt install $soft -y"
    else
        echo "sudo apt install $soft"
    fi

    index=$(($index+1))
done
```

运行一下看看效果
``` c
bash$ ./install_ubuntu.sh -y
共 1 个参数,参数列表是: -y
第 0 个参数是: -y
准备安装第 0 个软件 vim
sudo apt install vim -y
准备安装第 1 个软件 gcc
sudo apt install gcc -y
准备安装第 2 个软件 g++
sudo apt install g++ -y
准备安装第 3 个软件 gdb
sudo apt install gdb -y
```
