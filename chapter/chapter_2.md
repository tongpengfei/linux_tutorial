# linux编程入门(二)-熟悉linux常用命令

&ensp;&ensp;&ensp;&ensp;linux下的命令非常多，但常用的就那么几个，掌握基本命令以后，不常用的只要在用的时候搜一下就行，事实上也记不住太多的命令，经常不用的命令就忘了。  
&ensp;&ensp;&ensp;&ensp;下面主要展示linux下常用命令的使用方法。  
&ensp;&ensp;&ensp;&ensp;闲话少说，上车。

[toc]

# 终端的使用
终端就是我们经常看到的黑屏，我们后面的几乎所有操作都会在终端下进行，比如输入linux命令运行一个程序，查看文件等等。  
* 打开终端  
  CTRL+F2打开搜索框，输入gnome-terminal回车，即可打开终端

* CTRL+SHIFT+T，打开一个新标签  
  一个终端可以有多个标签页，比如在一个标签页下显示代码，在另一个标签页查看其他文档等。
  使用ALT+T打开一个新的标签

* ALT+数字，切换标签  
  ALT+数字可以快速的切换到从左到右的第N个标签，最左边的标签是CTRL+1，依次往后。

* CTRL+PAGE DOWN，切到下一个标签  

* CTRL+PAGE UP，切到上一个标签  
  
* CTRL+D，关闭当前标签页

# 显示当前路径

``` bash
# 显示当前目录路径
# 前面的"bash$"表示终端,后面的pwd是命令,该条代码表示: 在终端下输入命令 pwd 
bash$ pwd

# 显示出来的结果就是这样
/home/tpf/tutorial/samples/
```

> 小知识  
linux下的路径表示方法
> * 以"/"开头的表示从根目录开始,根目录就是linux的最上层目录, 例如 /home  
> * 以"./"开头的表示从当前目录开始,当前目录就是当前终端所在的目录,也称为工作目录,例如 ./samples  
> * 以"~"符号开头的，表示从用户目录开始,用户目录表示当前用户的根目录,用户根目录一般路径为/home/用户名,在我机器上就是/home/tpf, 例如 ~/samples 的绝对路径就是/home/tpf/samples。

# 切换目录

``` bash
bash$ cd 目录路径

#例如从根目录开始的路径地址
bash$ cd /home/tpf/cpp/tutorial/samples

#或者从当前目录开始的路径地址
bash$ cd ./samples

#也可以把"./"省略掉,也表示从当前路径开始
bash$ cd samples

#切换到用户根目录
bash$ cd ~

#切换到上一次的目录,这个是很实用的,有时候我们会快速在两个目录间来回切换,用这个很方便
bash$ cd -
```

# TAB自动补全命令
有些命令比较长，或者我们只记住了命令的开头几个字母，后面的忘了，可以先打出开头的几个字母，再按TAB,这时系统会提示所有符合开头字母的命令，这时候可以根据提示再继续输入。
``` bash
#以pwd为例,假如我们输入p + tab,输出为:
bash$ p #输入p后紧接着按tab补全
Display all 216 possibilities? (y or n)

#上面表示满足p开头的命令有216个，问我们是不是要全部显示出来,输入y表示显示,输入n表示不显示,216个太多了，我们先输入n，不显示
#继续再输入一个字母w，再按tab

bash$ pw #输入pw后紧接着按tab补全
pwd         pwd_mkdb    pwhich      pwhich5.18  pwpolicy

#这一次满足pw开头的命令只有5个了，可以再继续输入d或者其他的字母完成输入，当命令比较长的时候用tab补全可以显著提高输入命令的速度。嗯，手速不够，tab凑。
```

# 使用Man手册查看命令帮助
man手册是linux下非常有用的一个帮助工具,就像windows下的MSDN一样,里面有很多linux命令的使用说明,还有函数的说明,后面我们编程的时候会经常用man查看函数的说明。
``` c
#用man查看ls的帮助文档
bash$ man ls

#按回车会显示ls的帮助文档
#按q退出帮助文档
```


# 使用apt管理软件
**命令格式为: apt 命令 软件名**  
命令说明：  
* apt 是ubuntu下管理软件的工具,安装,卸载查找等都可以用这个命令,还有一个是apt-get  
* 命令有 install:安装 remove:移除 update:更新 search:查找等
* 软件名 就是需要安装的软件名称  

``` bash
#安装软件 
例如:安装tree工具,该工具的作用是打印目录结构

#sudo 表示用root权限执行后面跟着的命令,
#因为安装软件需要root权限,如果不是root用户,
#需要用sudo执行命令  
bash$ sudo apt install tree

#或者用apt-get,效果是一样的
bash$ sudo apt-get install tree

#在安装的过程中需要输入y确认,如果想自动确认可以在命令上加上-y
bash$ sudo apt -y install tree

#删除软件,例如删除tree
bash$ sudo apt remove tree

#查找软件,例如查找tree
bash$ sudo apt search tree

#更新软件,例如更新tree
bash$ sudo apt update tree

#查看apt帮助,一般在命令后面跟-h会显示该命令的帮助说明
bash$ apt -h
```

> 小知识  
> 关于apt和apt-get的区别,有兴趣的同学可以看[这里](https://www.sysgeek.cn/apt-vs-apt-get/)


# 显示目录结构
``` bash
#不带参数的tree打印当前目录
bash$ tree

#打印出来的格式就是这样,"."表示当前目录, 
#"dir"是一个文件夹的名字，下面有两个文件,
#分别是a.txt, b.txt  
.
└── dir
    ├── a.txt
    └── b.txt

#然后用命令tree打印"samples/dir/"这个目录结构
bash$ tree samples/dir/

#下面打印出来的格式,samples/dir/表示我们要打印的最上层目录,
#该目录下有两个文件，分别是a.txt.b.txt
samples/dir/
├── a.txt
└── b.txt

#显示隐藏文件
bash$ tree -a
.
└── dir
    ├── .hide.txt    #以.开头的文件名是隐藏文件
    ├── a.txt
    └── b.txt
```

# 显示当前目录下的所有文件
``` c
# 现在我们有个测试目录，目录结构是这样的,可以用tree打印目录结构
.
└── dir
    ├── a.txt
    └── b.txt

#然后当我们cd到dir目录下
#显示当前目录下的文件,后面的"."表示当前目录，可以省略掉
bash$ ls .
a.txt	b.txt

#以列表形式显示目录下的文件
bash$ ls -l
total 16
-rw-r--r--  1 tpf  staff  8  4 16 08:49 a.txt
-rw-r--r--  1 tpf  staff  7  4 16 08:49 b.txt

# 显示当前目录下的所有文件，包括隐藏文件, "."表示当前目录, ".hide.txt"是个隐藏文件,以"."开头的文件名是隐藏文件
bash$ ls -a
.		..		.hide.txt	a.txt		b.txt

```


# 创建目录
命令为: mkdir
``` c
#在当前目录下创建一个test目录
bash$ mkdir test

#创建带父目录的文件夹,带上-p参数表示给定的路径,
#如果父目录不存在,就把父目录也创建了
#比如下面想创建一个test/test1/test2/test3,
#其中test1/test2不存在,
#mkdir -p会把所有不存在的目录一起创建了
bash$ mkdir -p test/test1/test2/test3

```


# 删除目录
命令为: rm -rf 目录名
``` c
#删除目录test,用rm -rf可以删除文件和删目录,
#删目录主要是得用参数"r",表示删掉目录下的所有子目录和文件,
#删文件不需要"r",
#"f"表示force强制的意思,
#后面写删除文件时候可以详细说一下,这里先知道一下这个命令
bash$ rm -rf test
```

# 新建文件  
新建文件的方法有很多,下面列出几种我一般会用的到的新建文件的命令  
* **命令: touch 文件名**  

``` c
#新建一个文件test.txt
bash$ touch test.txt

#然后ls看一下新建的文件是否存在
bash$ ls
a.txt  #这是ls的执行结果,就是我们刚刚新建的
```

> 小知识  
touch 的另一个作用是更新文件的时间戳,也就是最后编辑时间,内容保持不变。

* **命令: echo > 文件名**  
echo的作用是显示字符串,windows下也有这个命令,这个命令里的 ">"表示把echo 显示的内容写到文件里,因为这里echo后面没有跟字符串，所以就是把一个空的字符串写到文件里，也就是新建了一个空文件了。这个">"符号叫重定向符号,以后会经常用到。

``` c
#新建一个空文件b.txt
bash$ echo > b.txt

#用ls看一下是否新建了b.txt
bash$ ls

#新建一个文件c.txt,并且向里面写入"i love linux"
bash$ echo "i love linux" > c.txt

#用cat查看c.txt里面的内容
bash$ cat c.txt
i love linux   #这是cat的显示结果
```

# 编辑文件
**这里是需要大家下功夫，多练的地方，因为会用和熟练使用完全是两个概念。**  
* **使用vim编辑文件**  
vim是linux下最常用的文本编辑工具之一，功能非常强大，有很多插件，是掌握linux必备的技能之一，vim也是后面我们编写程序的工具。熟练操作vim在编写代码时候会得心应手，那运指如飞，行云流水的感觉会让你由衷的感叹打代码到底有多爽，如果再配上一个拉风的机械键盘，分分钟让你变成整条gai最闪亮的仔！可以看我的另一篇文章:[vim常用操作简明教程附我用了多年的vim配置文件](https://www.jianshu.com/p/f706cb3c7d0e?utm_campaign=hugo&utm_medium=reader_share&utm_content=note&utm_source=weixin-friends&from=singlemessage&isappinstalled=0)。

* **使用Emacs编辑文件**  
另一个功能强大的文本编辑工具是Emacs,不仅是文本编辑工具，还是个集成开发环境(度娘的词),有兴趣的同学可以自己去研究研究,而且强烈推荐一开始的时候都了解一下,体验一下,最后看看喜欢哪个就用哪个。  

这里顺便说一下,在linux下要学习的东西非常非常多,大飞在这里只是起个带路人的作用,后面很多东西需要自己动手,主动探索,多玩,玩的多了自然就会了。  

又扯多了，上车。



# 查看文件

* **使用cat查看文件**  
cat是最常用的查看文件的工具,cat会输出文件的所有内容。
``` c
#查看test.txt
bash$ cat test.txt
#下面是输出的文件内容
file a

test

#查看test.txt，并打印行号
bash$ cat -n test.txt
#下面是输出的文件内容,并输出了行号,空行也算上行号了
     1	file a
     2	
     3	test
     
#查看test.txt,并打印行号,行号不算空行
bash$ cat -b test.txt
#下面是输出的文件内容，注意下面的空行没有算行号
     1	file a

     2	test

#使用man cat查看cat更多帮助
bash$ man cat

```

* **使用head查看文件前几行**
``` c
#head默认输出文件前10行
bash$ head test.txt
line 1
line 2
line 3
line 4
line 5
line 6
line 7
line 8
line 9
line 10

#指定输出前5行
bash$ head -n5 test.txt 
line 1
line 2
line 3
line 4
line 5

```

* **使用tail查看文件后几行**
``` c
#tail默认输出文件后10行
bash$ tail test.txt
line 31
line 32
line 33
line 34
line 35
line 36
line 37
line 38
line 39
line 40

#指定输出最后5行
bash$ tail -n5 test.txt
line 36
line 37
line 38
line 39
line 40

#监视文件追加的行，这个一般用来查看日志，
#比如有个程序在向test.txt里持续输出日志,
#这时候可以用tail -f test.txt输出新加的日志,
#-f默认输出最后10行
#-5f表示输出最后5行,行数可以改为自己想要的数
bash$ tail -5f test.txt
line 36
line 37
line 38
line 39
line 40
▊          #注意这里并没有结束，而是一个光标符号,
            表示等待输出新的内容

#按ctrl+c退出tail -f监视

```



# 删除文件  
命令: rm 文件名  

``` c
#假如现在有个文件名叫test.txt的文件,运行下面的命令会删掉这个文件
bash$ rm test.txt

#如果test.txt文件[不]存在会提示没有找到这个文件
bash$ rm test.txt
rm: cannot remove 'test.txt': No such file or directory

#强制删除,有就删,没有也别提示,我一般是用这个命令的,不让它提示
bash$ rm -f test.txt

#如果是删除目录,需要加上-r参数,
#r表示recursive,递归删除所有子目录。
#假如我们有个目录名为test_dir,
#如果没有可以先建一个
#建目录命令为
bash$ mkdir test_dir

#然后ls看一下目录是否被建立
bash$ ls

#然后再删掉
bash$ rm -r test_dir

#强制删除目录,效果和强删文件是一样的,
#如果有就删掉，没有也不要提示找不到，
#在实际应用中我最常用的是这种，就是不
#管删文件还是文件夹，一律用rm -rf,
#但这个纯看个人喜好。
bash$ rm -rf test_dir
```


# 复制文件或目录
**命令: cp -rf 源文件 目标文件**  
* -r表示递归复制子目录和子文件
* -f表示如果目标已存在，强制覆盖，不用问是否要覆盖。
``` c
#复制文件
#例如要复制a.txt为b.txt, 如果b.txt已经存在，会覆盖已存在的b.txt
cp a.txt b.txt

#复制目录
#如果要复制目录test_dir到用户根目录下的test_dir2,
#需要加上参数-r,因为复制目录需要把子目录和子文件一
#起复制过去
cp -r test_dir ~/test_dir2

#我常用的是不管复制啥，直接加参数-rf
#复制文件的时候其实不需要-r，但是没关系，
#加上也没错，用的时间长了，会产生用法上的
#习惯或者毛病，直接cp -rf就可以了，就不用
#区分要复制的到底是个文件还是个文件夹了。  
cp -rf test_dir test_dir2
```


# 移动文件
命令: mv 源文件 目标文件  
作用是把一个文件移动到另一个目录下，或者移动到当前目录下的另一个文件名(效果等同于重命名)
``` c
#重命名,把a.txt重命名为b.txt
bash$ mv a.txt b.txt

#把a.txt移到用户根目录下
bash$ mv a.txt ~/

#把a.txt移到用户根目录下，并重命名为b.txt
bash$ mv a.txt ~/b.txt

#新建一个目录test,并把a.txt移到当前目录里的test下面
bash$ mkdir test
bash$ mv a.txt ./test/

#移动目录和移动文件一样
#先新建个测试目录dir1
bash$ mkdir dir1

#把目录dir1重命名为dir2
bash$ mv dir1 dir2

#注意: 如果想把dir1放到当前目录里的dir2下面，
#需要在最后加上"/"
bash$ mv dir1 dir2/

```

# 查找文件
命令参考: find . -name "test*.txt"
用find可以根据文件名查找，或者根据类型查找，通过不同的参数设置
``` c
#在当前目录找文件名为a.txt的路径
bash$ find . -name "a.txt"
./ccc/a.txt   #这子目录里的a.txt路径
./aaa/a.txt
./a.txt       #这是当前目录下搜出来的结果

#找出一个绝对路径下的一般文件
bash$ find /home/tpf/samples/dir/bbb -type f
/home/tpf/samples/dir/bbb/c.txt
/home/tpf/samples/dir/bbb/b.txt
/home/tpf/samples/dir/bbb/a.txt

#找出当前目录下的目录
bash$ find . -type d
.            #这是当前目录
./ccc        #这是子目录
./aaa
```

# 搜索文件中的字符
命令: grep
搜过文件中的字符是非常常用的功能之一,比如写代码时候搜过一个函数都在哪里调用了。或者分析日志时候过滤出有用数据都可以用grep。
``` c
#在当前目录下搜索包含test字符的.txt文件
bash$ grep test *.txt
a.txt:test     #搜索结果a.txt里包含test

#在当前目录及子目录下搜过包含test字符的所有文件
#"."表示从当前目录开始
#"-r":表示搜索子目录
bash$ grep test . -r
./bbb/d.cpp:test   #子目录下的d.cpp里有test
./bbb/c.txt:test   #子目录下的c.txt里也有test
./a.txt:test       #当前目录下的a.txt里有test


#下面是从绝对路径查找
#"-i":忽略大小写
bash$ grep TEST /home/tpf/samples/*.txt -i
#grep里写的是TEST,由于加上了-i，
#忽略了大小写,所以把test也找出来了
/home/tpf/samples/dir/a.txt:test

#"-n":打印搜过结果在文件中的行号
bash$ grep test *.txt -n
a.txt:3:test      #test在a.txt中的第3行

#"-a"表示不要忽略二进制文件,
#把所有的文件都当做文本文件处理
#使用的时候可以把需要的参数都列上,如: -rnia
bash$ grep test *.txt -rnia
./bbb/c.txt:2:test
./a.txt:3:test

#"-v":反向查找,即搜索结果不要包含该字符串
grep -v test . -rnia
./bbb/a.txt:1:bbba
./c.txt:1:this is a string
./b.txt:1:file b
./a.txt:1:file a

```

# 统计文本行数字数和词数
命令: wc
先准备一个文本文件test.txt
``` c
bash$ echo "hello world
i love linux" > test.txt
```
用cat查看一下新建的文本
``` c
bash$ cat test.txt 
hello world
i love linux
```

统计test.txt的行数
``` c
bash$ wc -l test.txt
#输出的第一列是行数,
#下面显示是2行
       2 test.txt
```

统计单词个数
``` c
bash$ wc -w test.txt
#输出的第一列是单词个数，
#下面显示有5个单词
       5 test.txt
```

统计字符个数
``` c
bash$ wc -c test.txt
#下面显示有25个字符
      25 test.txt
```

上面显示25个字符,但是我们数出来的只有23个字符,这是怎么回事?我们可以用xxd查看文件的十六进制格式,看看有什么猫腻
``` c
bash$ xxd test.txt
#下面以十六进制格式显示了test.txt的数据
#最左边的数字是每行首字节偏移地址,是十六进制的
#中间的数据是文本的十六进制格式的数据
#最右边是每行数据对应的字符
00000000: 6865 6c6c 6f20 776f 726c 640a 6920 6c6f  hello world.i lo
00000010: 7665 206c 696e 7578 0a                   ve linux.

```
进一步观察，会发现hello world和i之间有个'.',  
这是个换行符,十六进制是0x0a  
linux后面也跟了一个看不见的'.',所以加上这两个字符就是25个字符。  

#### 使用sed替换文本内容


#### 使用awk格式化输出文本

# 使用管道组合多个命令
在linux下可以通过管道组合多个命令完成一个强大的功能,其原理是前一个程序的输出是后一个程序的输入，程序对输入的数据处理后，再输出到下一个程序，数据像水一样从管道的一头流向另一头。例如对一个文本文件的内容排序,去重,过滤,再保存到另一个文件。下面我们就来完成这个功能。  

* **先准备好一个文本文件**  
复制下面的代码到终端执行后会生成一个fruit.txt文本,文本内容是5种水果的名字,  
每行数据有两列,分别是:  
id name  
我们会sort分别对id和name做排序,数据里故意加入了空行,我们会用sed命令把空行去掉。  
注意从echo "开始到 " > fruit.txt都是命令内容。

``` c
bash$ echo "
id name
1 apple

3 strawberry


2 banana

5 orange
4 wattermelon
" > fruit.txt

```

下面是组合命令的最终效果,对文本做出的整理包括: 
* cat 输出文本
* sed 去掉空行
* tail 去掉表头
* sort 排序 
各命令之间用"|"连接起来。"|"称为管道符。
```c
#然后我们看看组合命令的最终效果
bash$ cat fruit.txt | sed '/^$/d' | tail -n +2 | sort -k 1
#下面是组合命令的输出内容
1 apple
2 banana
3 strawberry
4 wattermelon
5 orange
```

我们来逐个解释一下各个命令的作用
```c
#首先是输出fruit.txt的内容
bash$ cat fruit.txt

id name
1 apple

3 strawberry


2 banana

5 orange
4 wattermelon

#可以看到cat fruit.txt输出的内容
#和我们输入的内容完全一样
```

cat fruit.txt后面出现了一个"|"符号,这个符号是管道符,两个命令之间加一个"|"表示前一个命令的输出会成为后一个命令的输入,在这里就表示cat fruit.txt输出的文本会传到sed,然后由sed处理cat fruit.txt输出的内容。
```c
#sed '/^$/d'用于删除空行,
#sed后面跟的是正则表达式,'^'表示行开头,'$'表示行结尾
#/^$/的意思是匹配行开头和行结尾紧挨着的行,也就是空行,
#如果想匹配包含orange的行,就用表达式: /orange/
#d表示删除, '/^$/d'的意思就是找到空行并删掉。
#来看一下效果:

bash$ cat fruit.txt | sed '/^$/d'
#下面的输出会发现空行没了,这就是
#这里sed语句的作用
id name
1 apple
3 strawberry
2 banana
5 orange
4 wattermelon
```

然后是去掉表头，即把id name去掉，只打印数据
```c
#tail -n +2表示从第二行开始打印,即不显示第一行
#tail -n 2表示输出最后2行
#看看效果
bash$ cat fruit.txt | sed '/^$/d' | tail -n +2
#注意下面的输出中第一行id name没有了
1 apple
3 strawberry
2 banana
5 orange
4 wattermelon
```

下一步是用sort对数据排序
```c
#sort默认情况下对数据第一列排序
bash$ sort fruit.txt
#下面是排序的结果,可以看到id从小到大






1 apple
2 banana
3 strawberry
4 wattermelon
5 orange
id name

#===============

#如果想指定列数,可以用-k 列数,比如我们 
#指定对第二列排序,并用cat输出行号,
#这里输出行号主要是想识别空行

bash$ sort -k 2 fruit.txt | cat -n
#下面是排序的结果,可以看到排序为name列
#按字母顺序从小到大排的
     1	
     2	
     3	
     4	
     5	
     6	
     7	1 apple
     8	2 banana
     9	id name
    10	5 orange
    11	3 strawberry
    12	4 wattermelon

#===============

#对数据做逆序排序,并输出行号
bash$ sort -r fruit.txt | cat -n
#下面是排序的结果，可以看到id列是从大到小排序的
     1	id name
     2	5 orange
     3	4 wattermelon
     4	3 strawberry
     5	2 banana
     6	1 apple
     7	
     8	
     9	
    10	
    11	
    12	

```

最后我们再把用到的命令都连起来看一下
``` c
bash$ cat fruit.txt | sed '/^$/d' | tail -n +2 | sort -k 1
#下面是组合命令的输出内容
1 apple
2 banana
3 strawberry
4 wattermelon
5 orange
```

以上的例子我们可以看到由不同功能单一的命令组合起来便成一个强大的工具。
当我们遇到一个问题的时候，可以先把需求按条划分，个个列出来，再一个一个找相应的命令，最后组合起来就可以了。
比如我们想把数据的每一行格式化输出一下,变成"第[i]行,水果名称是[name]"
``` c
#下面的命令多了awk语句
#TODO awk的解释
bash$ cat fruit.txt | sed '/^$/d' | tail -n +2 | sort -k 1 | awk -F" " '{ print "第[" $1 "]行,水果名称是[" $2 "]" }'
#输出内容为: 
第[1]行,水果名称是[apple]
第[2]行,水果名称是[banana]
第[3]行,水果名称是[strawberry]
第[4]行,水果名称是[wattermelon]
第[5]行,水果名称是[orange]

#是不是很过瘾^.^
```

最后当我们把需求分隔成一条一条的命令的时候,遇到某一条需求不知道用什么命令的时候就很简单了,上网搜一下就可以了,比如现在新来一个需求是显示文本内容的时候,需要把空格换成逗号,这里就需要用替换命令,就搜"linux 替换字符",大家可以上网搜一下。

留个作业吧
``` c
#大家可以试一下用哪些命令
#可以把fruit.txt显示成下面的效果
1,apple
2,banana
3,strawberry
4,wattermelon
5,orange
```

# xargs的用法
xargs是一个命令行参数的列表，可以把上一个命令的输出做为参数，传给下一个命令。乍一听，感觉没啥用，下面我们通过例子来理解这个命令的神奇之处。


先看一下xargs对参数的影响,下面显示当前目录下的所有.txt文件
``` c
bash$ ls *.txt
a.txt		b.txt		c.txt		fruit.txt
```

当我们把ls的输出传到xargs的时候,可以看到xargs的输出格式已经变了,表示xargs起作用了
``` c
bash$ ls *.txt | xargs
a.txt b.txt c.txt fruit.txt
```

我们可以改变每行的输出列数,下面我们让输出变为每行两列
``` c
bash$ ls *.txt | xargs -n 2
#可以看到每行2列
a.txt b.txt
c.txt fruit.txt
```

再改成每行一列试试
``` c
bash$ ls *.txt | xargs -n 1
a.txt
b.txt
c.txt
fruit.txt
```

如果我们想把列出的文件名加上绝对路径:
``` c
bash$ ls *.txt | xargs -I i echo /home/tpf/i
/home/tpf/a.txt   #这里可以看到echo里的i都被换成了相应的文件名
/home/tpf/b.txt
/home/tpf/c.txt
/home/tpf/fruit.txt
```

上面的命令中,xargs多了-I i参数,意思是把xargs输出的参数，取个名字叫"i",然后后面的echo就可以用"i"来带替xargs输出的参数了,所以  
echo /home/tpf/i.txt  
扩展开就是  
echo /home/tpf/用I定义的替换变量.txt  

还可以用-t把xargs后面的命令语句打印出来,方便调试
``` c
bash$ ls *.txt | xargs -I i -t echo /home/tpf/i
#可以看到下面每行的结果前面都有一行echo,那便是
#xargs -t后面要执行的语句被打印出来了。
echo /home/tpf/a.txt        #这行就是xargs -t的输出,表示xargs后面要执行的语句是echo /home/tpf/a.txt
/home/tpf/a.txt             #这行是echo的输出
echo /home/tpf/b.txt
/home/tpf/b.txt
echo /home/tpf/c.txt
/home/tpf/c.txt
echo /home/tpf/fruit.txt
/home/tpf/fruit.txt
```


**再来个例子**  
需求:列出当前及子目录下所有包含"test"的.txt文件,并把文件后缀改为.test

首先拆解一下需求:
* 1.列出当前及子目录下所有.txt的文件
* 2.在上面过滤出来的.txt文件里搜过"test"字符串的文件
* 3.把文件后缀改为.test

下面开始第1步,列出当前及子目录下的所有.txt文件,如果不包含子目录，
我们可以用ls *.txt,但要求是包含子目录,我们可以用find
``` c
bash$ find . -name "*.txt"
#find 列出了子目录里的.txt文件
./.hide.txt
./bbb/c.txt
./bbb/b.txt
./bbb/a.txt
./fruit.txt
./c.txt
./b.txt
./a.txt
```

然后需要找出带test的字符串,下面要特别注意一下带xargs和不带xargs的区别,假如我们不用带xargs的语句,如  
``` c
bash$ ls *.txt | grep test  
```
这条语句是从ls *.txt的结果里搜带test字符串的行，所以搜出来的是"文件名"里带test的行。  
而我们要的效果是"文件"里面带test字符串的,这时就得用xargs了。  
这里有点绕，可以通过例子来看一下区别:  

先来一个不带xargs的:  
为了这个例子需要先新建个叫test.txt的文件  
``` c
bash$ find . -name "*.txt" | grep test
#可以看到文件名里包含test的被搜出来了
./test.txt
```

再来看搜文件里面字符串的用法
``` c
bash$ find . -name "*.txt" | xargs grep test -n
#下面是文件里面带"test"字符串的,
#-n打出了test所在的行号
./bbb/c.txt:2:test
./c.txt:2:test hello
```

还可以写成下面这样,这样应该更好理解一点,注意下面有个-rn，如果只有-n不会显示文件名，  
下面xargs -I i表示把find . name "*.txt"输出的参数用变量i表示,并传到grep里
``` c
bash$ find . -name "*.txt" | xargs -I i grep test i -rn
./bbb/c.txt:2:test
./c.txt:2:test hello
```

这是grep不带-r的效果,这里不用特意记什么时候带-r什么时候不带,全打上就可以了,打出的结果不对的时候再调整命令。
``` c
bash$ find . -name "*.txt" | xargs -I i grep test i -n
2:test
2:test hello
```

最后一步是把文件名后缀从.txt改为.test
首先观察一下第二步生成的结果:
``` c
./bbb/c.txt:2:test
./c.txt:2:test hello
```
观察会发现文件名后面还跟了行号和那一行的数据,所以需要先把没用的行号和数据去掉,只取文件名,进一步观察会发现文件名和行号之间用的是":"分隔开的,这时候我们就可以找个命令,看什么命令可以定制分隔符,并取出相应的列,有印象的同学可能已经想出来了，  
可以用awk -F":"指定以":"为分隔符,然后打印出第一列
下面来操作一下:
``` c
bash$ find . -name "*.txt" | xargs grep test -n | awk -F":" '{print $1}'
./bbb/c.txt
./c.txt
```

到了这里就好说了，我们需要先把后缀去掉，上一步也可以直接以".txt"为分隔符直接取不带后缀的文件名，咱们这里为了举例说明有时候一长串命令里需要多次使用同一个命令的情况，所以我们再用awk去掉后缀，这里我们用"."为分隔符，先看一下效果
``` c
bash$ find . -name "*.txt" | xargs grep test -n | awk -F":" '{print $1}' | awk -F"." '{print $2}'
/bbb/c
/c
```

上面我们发现文件名是取出来了，但是最开头的'.'也被去掉了,文件路径由相对路径变成绝对路径了，这样就不对了,awk输出时候,需要再把'.'加上
``` c
bash$ find . -name "*.txt" | xargs grep test -n | awk -F":" '{print $1}' | awk -F"." '{print "."$2}'
./bbb/c
./c
```

现在已经取到文件名了，做了一大堆工作，终于到了"真-改名"的时候了，改名我们可以用mv a.txt a.test达到效果。  
下面来看一下命令
``` c
bash$ find . -name "*.txt" | xargs grep test -n | awk -F":" '{print $1}' | awk -F"." '{print "."$2}' | xargs -I i -t mv i.txt i.test
#下面的输出是xargs -t输出的,
#如果不带-t是不会打印的
mv ./bbb/c.txt ./bbb/c.test
mv ./c.txt ./c.test
```

最后显示一下文件，看看后缀是否被替换了
``` c
bash$ tree
.
├── a.txt
├── b.txt
├── bbb
│   ├── a.txt
│   ├── b.txt
│   ├── c.test   #这里c.txt变成了c.test
│   └── d.cpp
├── c.test       #这里也变了
├── fruit.txt
└── test.txt

1 directory, 9 files
```

大家可以再把.test后缀改回到.txt试试。



# 查看历史命令
有时候我们需要查看用过了哪些命令,比如想确定最后有没有执行某一步操作,可以看一下历史命令,或者想找到上一步操作，重新执行一遍命令。

``` c
按方向键"上"可以顺序往前找执行过的命令
```

``` c
按方向键"下"可以切到下一次命令
```

使用history查看全部历史命令
``` c
bash$ history
会输出最近用过的命令，最多可以记录1000条
```

history和less相结合,可以翻页显示,可以
* 按j,k或上,下滚屏
* 按PAGE UP,PAGE DOWN翻页
* 按大写"G"翻到最后
``` c
bash$ history | less
```

显示最后5条命令
``` c
bash$ history 5
#下面显示了最后5条用过的命令
 1567  history | less
 1568  history | less
 1569  history 5
 1570  history 5
 1571  history 5
```

# 搜过使用历史命令
按ctrl+r可以搜过用过的命令,这个是非常实用的命令,当我们想再输入一遍长长的命令的时候,经常需要搜过以前输过的,避免再打一遍。
``` c
bash$ ctrl+r
#输入出ctrl+r终端会显示下面这样的字符:
#表示等待输入搜过关键字,
#比如我们想搜过用过的带tr的命令
(reverse-i-search)`': 

#当输入"tr"后，命令tree被搜了出来
(reverse-i-search)`tr': tree

#当输入"xarg"后，搜出了包含xarg的一长串串命令
(reverse-i-search)`xarg': find . -name "*.test" | xargs grep test -n | awk -F":" '{print $1}' | awk -F"." '{print "."$2}' | xargs -I i -t mv i.test i.txt
```


#### 软链接


#### 硬链接

#### ps查看当前进程

#### htop查看内存使用情况

#### df查看硬盘大小

#### du查看文件夹大小

# 使用chmod更改文件权限
linux是多用户多任务操作系统，这意味着多个用户可以同时登录linux，在不同的终端下执行不同的命令。这个时候如果我dafei建了一个文件，这个文件的拥有者就是dafei，文件的用户组就是dafei所在的用户组。  
我可以设置这个文件只有dafei可以读写，dafei的用户组只能读，不能写，其他人不能读，不能写，也不能执行，就这么任性。  
总结一下就是linux下文件权限按使用对象可以分三级，分别是文件拥有者，用户组和其他。  
每级都可以设置读，写，执行权限。执行权限就是运行这个文件，比如是个shell脚本的时候，可以执行这个脚本，或者是可执行程序的时候，可以运行这个程序。  
使用chmod可以改变每级的权限。  

下面我们先查看一下文件的权限。  
先新建个测试目录moddir和test_shell.sh文件
``` c
bash$ sudo mkdir moddir

#再新建个shell文件
bash$ echo "#$/bin/bash
echo 'hello dafei'" > test_shell.sh

#查看一下test_shell.sh里的内容
bash$ cat test_shell.sh
#下面两行是test_shell.sh的内容
#$/bin/bash
echo 'hello dafei'
```

再用ls -l查看一下，显示结果的第一列有10个字符，第一个字符是文件类型，后面9个字符每三个一组，分别为:
拥有者权限: 读,写,执行  
用户组权限: 读,写,执行  
其他人权限: 读,写,执行  

第一个字符'-'表示这是一个文件, 'd'表示目录

``` c
bash$ ls -l
# 第一列的第一个字符为'd'表示这是一个目录
# 后面每三个字符一组
# 第一组是 rwx 表示拥有者root 可以读(r)，可以写(w)，可以执行(x)
# 第二组是 r-x 表示用户组staff可以读(r)，不能写(-)，可以执行(x)
# 第三组是 r-x 表示其他用户   可以读(r)，不能写(-)，可以执行(x)
drwxr-xr-x  2 root  staff   64  5  8 08:36 moddir

# 第一列的第一个字符为'-'表示这是一个文件
# 第一组是 rw- 表示拥有者dafei可以读(r)，可以写(w)，不能执行(-)
# 第二组是 r-- 表示用户组staff可以读(r)，不能写(-)，不能执行(-)
# 第三组是 r-- 表示其他用户   可以读(r)，不能写(-)，不能执行(-)
-rw-r--r--  1 dafei  staff  11  5  8 08:28 test_shell.sh
```

现在我们去掉dafei的test_shell.sh读权限,这样dafei就只能写,不能读
``` c
# 我们可以用-r参数,表示去掉读权限
bash$ chmod -r test_shell.sh
```

再ls -l查看一下test_shell.sh的权限
``` c
bash$ ls -l test_shell.sh
# 一看减多了，把用户组和其他用户的读权限也去掉了，
# 我们其实只想去掉dafei的读权限
--w-------  1 dafei  staff  31  5  8 09:00 test_shell.sh
```

那就再把读权限加回去吧  
``` c
# 下面用了+r参数，表示加上读权限
bash$ chmod +r test_shell.sh 
bash$ ls -l test_shell.sh
# 下面可以看到三组的读权限去加回去了
-rw-r--r--  1 dafei  staff  31  5  8 09:00 test_shell.sh
```

如果只想去掉dafei的读权限,需要用参数u-r,  
u表示user，就是只对文件的拥有者做-r操作，也就是只去掉文件拥有者的读权限。
``` c
# 下面的参数加了u,表示对文件拥有者做后面的操作
bash$ chmod u-r test_shell.sh
bash$ ls -l test_shell.sh 
# 可以看到权限只有第一组的r被去掉了
--w-r--r--  1 dafei  staff  31  5  8 09:00 test_shell.sh
```

这时候我们试着读一下test_shell.sh的内容,看看效果
``` c
bash$ cat test_shell.sh 
# 嗯，显示结果在意料之中，访问被拒绝。
cat: test_shell.sh: Permission denied
```

那如果我们用sudo来读一下呢?
``` c
bash$ sudo cat test_shell.sh 
Password:
# 文件内容被读出来了
#$/bin/bash
echo 'hello dafei'
```

如果我们把所有的读权限都去掉了，sudo还能读出来吗?
``` c
bash$ chmod -r test_shell.sh
bash$ ls -l test_shell.sh 
--w-------  1 dafei  staff  31  5  8 09:00 test_shell.sh

bash$ sudo cat test_shell.sh
#$/bin/bash
echo 'hello dafei'
```

结果显示就算把所有组的读权限都去掉，sudo依然可以读出文件内容，root果然是爸爸。

再把读权限都加上，方便后面测试
``` c
bash$ chmod +r test_shell.sh
```

同理  
+w表示加写权限  
-w表示去掉写权限  
+x表示加执行权限  
-x表示去掉执行权限  

u表示限定拥有者  
g表示限定用户组  
o表示限定其他人  

下面我们看一个常用的，在linux下我们经常需要写个shell脚本做一些任务，写完脚本后需要给脚本文件个可执行权限才能方便的运行。

``` c
# 给test_shell.sh个可执行权限
bash$ chmod +x test_shell.sh

# 查看一下权限
bash$ ls -l test_shell.sh
# 可以看到每个权限分组都有了x可执行权限
-rwxr-xr-x  1 dafei   staff   31  5  8 09:00 test_shell.sh

# 下面我们执行一下脚本,
# 结果显示输出了一行"hello dafei"
bash$ ./test_shell.sh
hello dafei
```

还可以用数字777设置权限，数字7在二进制里是111，每一个二进制位对应于rwx，如果只有读权限，那二进制表示就是100，换算成十进制就是4  
|        | 二进制 | 十进制 | 字  母 |
| ------ |:------:| ------:| ------:|
| 只  读 |   100  |    4   |   r--  |
| 只  写 |   010  |    2   |   -w-  |
| 可执行 |   001  |    1   |   --x  |


看上面的图，如果读写执行全要就是十进制的4,2,1加起来，也就是7，1个7表示其他分组的权限，777就是三个分组的权限，要什么权限就把哪个数加进来就可以了，比如可读写，不能执行，那对应的数字就是4+2=6。  

下面我们把test_shell.sh的所有权限都先去掉
``` c
# 下面用0表示没有任何权限
bash$ chmod 0 test_shell.sh

# 查看一下，结果很是酸爽
bash$ ls -l test_shell.sh 
----------  1 tpf  staff  31  5  8 09:00 test_shell.sh
```

再给个读权限
``` c
bash$ chmod 4 test_shell.sh
bash$ ls -l test_shell.sh
# 结果显示只有其他分组的权限变成了可读
-------r--  1 tpf  staff  31  5  8 09:00 test_shell.sh
```

但我们想要的是所有分组都可读，那就得用444
``` c
bash$ chmod 444 test_shell.sh
bash$ ls -l test_shell.sh
# 结果可以看到所有分组都有可读权限了
-r--r--r--  1 tpf  staff  31  5  8 09:00 test_shell.sh
```

再给个所有分组的777权限看一下
``` c
bash$ chmod 777 test_shell.sh
bash$ ls -l test_shell.sh
# 结果显示所有分组都有了全套权限
-rwxrwxrwx  1 tpf  staff  31  5  8 09:00 test_shell.sh
```



# 使用chown更改文件所属用户
在linux下，root用户拥有最高权限，他是系统管理员，可以操作任何文件，一般用户像我dafei等级不够，是不能操作系统管理员的文件的，比如我想修改删除root用户的文件会被系统严词拒绝的。  
假如有个test.txt文件是root用户的，而我dafei今天心情不爽就是想往test.txt里写点东西，咋弄?  
这时候要么我们用sudo获得root权限执行命令，要么把test.txt改成dafei的。  
下面我们演示一下如何把root用户的文件改成一般用户的，即在我这是dafei的。


> 注意!!  
写这段的时候我用的是mac机器，mac和linux的很多命令都是一样的，在这里dafei的用户组是staff, root的用户组是wheel，和linux下有些区别，大家不用非得是wheel和staff，以自己的为准。

> 小电影  
想看unix,linux,mac,windows之间的故事可以看[这里](https://www.jb51.net/os/other/159236.html)，《现代操作系统》里也有相同的故事，看起来很过瘾。

先用ls -l查看文件属于哪个用户  
``` c
bash$ ls -l test.txt
# 下面显示该文件属于用户tpf,用户组为staff
-rw-r--r--  1 dafei  staff  25  4 29 08:55 test.txt
```

为了演示测试效果，我们先把test.txt改为root用户  
``` c
bash$ sudo chown root test.txt
Password: #第一次使用sudo的时候需要输入密码

# 再查看一下用户
bash$ ls -l test.txt
# 下面显示该文件已经属于root用户了
-rw-r--r--  1 root  staff  25  4 29 08:55 test.txt
```

然后我们试着往test.txt里追加一行文字，看看有什么反应，  
注意!! 这里我们是以非root用户往test.txt里追加文字的
``` c
bash$ echo "hello root" >> test.txt
# 结果显示操作被严词拒绝，因为我们没有权限
-bash: test.txt: Permission denied
```

那么我们有两种办法可以修改这个文件
* 以sudo获得root权限执行命令，这种一般在改系统配置的时候会用到
``` c
# 用sudo vim打开test.txt，然后修改
bash$ sudo vim test.txt
```

* 把文件改成非root用户
``` c
bash$ sudo chown dafei test.txt

# 再ls -l查看的时候可以看文件已经变成dafei了
bash$ ls -l test.txt
-rw-r--r--  1 dafei  staff  22  5  7 08:28 test.txt
```

如果想把目录下的所有文件都改成用户dafei的,需要参数-R  
假设我们现在有个测试目录bbb
``` c
# 先看一下bbb下的文件所属用户
bash$ ls -l bbb
total 32
-rw-r--r--  1 dafei  staff  5  4 22 08:41 a.txt
-rw-r--r--  1 dafei  staff  4  4 19 09:08 b.txt
-rw-r--r--  1 dafei  staff  9  4 22 08:13 c.txt
-rw-r--r--  1 dafei  staff  9  4 23 08:58 d.cpp


# 下面的命令表示把bbb及其下的所有文件改成root用户:wheel用户组
bash$ sudo chown -R root:wheel bbb

# 查看一下bbb下的文件
bash$ ls -l bbb
# 下面显示用户变成了root，用户组变成了wheel
total 32
-rw-r--r--  1 root  wheel  5  4 22 08:41 a.txt
-rw-r--r--  1 root  wheel  4  4 19 09:08 b.txt
-rw-r--r--  1 root  wheel  9  4 22 08:13 c.txt
-rw-r--r--  1 root  wheel  9  4 23 08:58 d.cpp
```


#### linux的主要目录作用


#### 本章总结
用到的命令有  
* 显示当前目录: pwd 
* 切换目录: cd
* 安装软件: sudo apt-get install 软件名
* 查看man手册: man 命令名
* 显示目录结构: tree



#### 综合练习
现在我们来个综合练习吧,目标是  
* **在用户目录下新建一个目录test**  
* **在test目录里新建一个文件名为a.txt**
* **用echo 向a.txt里写入一个字符串**
* **查看a.txt里的文本**
* **把a.txt复制为b.txt**
* **把a.txt删掉**
* **把b.txt重命名为c.txt**
* **把以上命令写入一个shell脚本myshell.sh,然后执行myshell.sh完成以上操作,并把每一步的操作都打印出来**
