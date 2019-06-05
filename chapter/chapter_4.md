# linux编程入门(四)-远程登录和远程拷贝
[toc]

# 使用ssh登录远程linux
从本地机器远程登录另一台linux可以用ssh，这是客户端程序，需要被连接机器开启sshd进程，这是服务器程序，sshd运行后会默认监听22号端口，ssh就通过该端口与sshd传送数据。登录到远程机器后，我们就可以像操作本地机器一样操作远程终端。    
被连接的linux机器需要确认是否已经开启sshd进程，我们可以用netstat -npl检测一下sshd进程是否存在。  
netstat的作用是查看本机网络连接状态，比如哪些进程监听了哪些端口，与哪些ip建立了连接，建立的是tcp还是udp连接，该连接处于什么状态，状态分很多种，比如监听状态，已建立连接，已关闭，正在关闭等等。  
netstat命令在网络编程里是非常常用的命令，暂时可以先了解这几个参数，后面有机会讲到网络编程的时候咱们可以详细了解一下这个命令的更多使用方法。

netstat用到的参数说明:  
* -n:用数字ip表示网络地址，否则会用域名表示网络地址
* -a:显示所有状态的连接
* -p:打印进程号和进程名
* -l:只显示监听状态的连接

搜索监听状态里是否有名为sshd的进程,因为sshd是root权限打开的进程,netstat的时候需要加上sudo才能看到,否则netstat里不会显示
``` c
# 为了能看清各字段的意思,grep的时候带上了字段名
# grep -E 可以指定搜索多个关键字,关键字之间用"|"分隔

bash$ sudo netstat -npl | grep -E "Proto Recv-Q|ssh"
# 下面显示出sshd存在,且监听的是22号端口
# 如果不存在就打印不出下面这行
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      433/sshd
```

另一种方法检测本地是否已监听了22号端口
``` c
#从所有状态里找出监听22号端口的tcp连接
bash$ sudo netstat -nat | grep 22
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN
```

用ps查看sshd是否存在
``` c
bash$ ps -e | grep sshd
20002 ?        00:00:00 sshd
```

如果没有检测到sshd进程，可以先看sshd命令是否存在，不存在的需要安装一下sshd进程  
``` c
# 用which查看sshd是否存在
bash$ which sshd

# 如果显示出下面这行，表示找到了sshd命令，位置是/usr/sbin/sshd
# 如果没显示出下面这行，那就需要先安装一下sshd
/usr/sbin/sshd

# 安装sshd
bash$ sudo apt install openssh-server
```

启动sshd
``` c
bash$ sudo service ssh start
```

再次查看一下sshd是否已经启动
``` c
bash$ sudo netstat -nap | grep sshd
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      21380/sshd
```

从上面列出的结果可以看到sshd已经启动了，进程id是21380，监听的是22号端口  

下面我们开始从本地用ssh连接远程的sshd  
``` c
# 参数说明:
# root表示用户名为root,
# 可以用自己登录linux的用户名
# 后面跟的是远程机器的ip
# 用户名与ip之间用"@"连接起来
bash$ ssh root@192.168.1.101
#第一次连接后正常会问是否要连接,需求输入yes
The authenticity of host '192.168.1.101 (192.168.1.101)' can't be established.
ECDSA key fingerprint is SHA256:pN8nJSctF6RbDz37bSDQXiUukxF7Pq+GvEJ6fkINOyA.
Are you sure you want to continue connecting (yes/no)? yes
#接着需要输入密码
root@192.168.1.101's password:
#连接上后会显示这样
Welcome to Ubuntu 18.10 (GNU/Linux 4.18.0-17-generic x86_64)
```

如果sshd监听的是其他的端口,可以用-p指定连接哪个端口
``` c
bash$ ssh -p22 root@192.168.1.101
```

连接上后我们就可以像操作本地终端一样操作远程机器了  
如果连接不上需要检查一下
* 用户名，ip，密码填写是否正确
* ping一下远程机器，看是否可以ping通，ping不通表示无法连到远程机器，需要检查一下网络
* 如果能ping通，就需要再确认一下远程机器sshd是否已经启动，如果没启动需要参考上面步骤启动一下

最后是退出远程登录
```c
按ctrl + d退出远程连接
```

> 小知识  
如果要频繁远程登录,每次输入ip会比较麻烦,这时可以给ssh root@192.168.1.101取个别名，取别名的命令是alias，步骤为:  

``` c
# 向~/.bashrc文件追加一行,内容为:
# alias ssh101='ssh root@192.168.1.101'
bash$ echo alias ssh101="'ssh root@192.168.1.101'" >> ~/.bashrc

# 使新改的~/.bashrc生效
bash$ source ~/.bashrc

# 再连接的时候就可以用ssh101带替后面的一长串串命令了
# 下面的命令等同于ssh root@192.168.1.101
bash$ ssh101
```

> 如果不想每次都输入密码，一样可以免密登录，步骤为:  

``` c
# 本地需要先确定是否~/.ssh该目录，后面需要用到
bash$ ls ~/.ssh

# 如果没有可以新建一个
bash$ mkdir ~/.ssh
```

> 本地使用ssh-keygen产生公钥私钥对，生成的公钥私钥在~/.ssh下

``` c
bash$ ssh-keygen
```

> 查看一下生成的公钥私钥
 * id_rsa是私钥,id_rsa.pub是公钥,可以用cat查看一下这两个文件里都写的啥东西
 * 第一次使用ssh-keygen的时候没有known_hosts文件,这个文件里放的都是远程机器的ip和公钥数据,免密登录的每台机器都会在这里生成一条记录

``` c
bash$ ls ~/.ssh
id_rsa		id_rsa.pub	known_hosts
```

> 使用ssh-copy-id将公钥复制到远程机器中,后面的机器地址需要换成自己的远程机器的用户名和ip  
 这一步会在远程机器的~/.ssh/authorized_key文件中加入一条记录  
 其实就是把本地~/.ssh/id_rsa.pub里的数据追加到远程的~/.ssh/authorized_key文件里  
 所以我们直接上远程机器上编辑~/.ssh/authorized_key写上id_rsa.pub里的内容效果和使用ssh-copy-id是一样的
 这样远程机器就认识本地机器了  

``` c
bash$ ssh-copy-id -i .ssh/id_rsa.pub root@192.168.1.101
#下面需要输入密码
root@192.168.1.101's password:
```

> 再登录的时候就不需要密码了
``` c
bash$ ssh101
Welcome to Ubuntu 18.10 (GNU/Linux 4.18.0-17-generic x86_64)
```


# 使用scp远程拷贝文件和目录

还记得刚开始学linux的时候不知道有scp，当时在windows与linux下拷贝文件是用ftp，也就是linux下建个ftp服务器，windows下装个ftp客户端，直到后来发现scp的时候感觉整个世界就变了。  
闲话少说，上车。

假如本地机器当前目录下有个文件叫test.txt的文件,我想把这个文件拷到ip为192.168.1.101的linux /home/dafei/test/目录下,可以用下面的命令  
``` c
# scp和ssh的命令比较像,都需要用户名和ip
bash$ scp test.txt dafei@192.168.1.101:/home/dafei/test/
#正常会显示下面的进度条
test.txt                100%    6     0.0KB/s   00:00

# 然后ssh到192.168.1.101下看看是否有了test文件
bash$ ssh dafei@192.168.1.101

# 用ls查看一下/home/dafei/test目录,
# 可以看到已经有test.txt文件了
bash$ ls /home/dafei/test
test.txt
```

也可以把远程文件拷到本地
``` c
# 最后的./test1.txt表示拷到本地后,
# 把文件重命名为test1.txt了
bash$ scp dafei@192.168.1.101:/home/dafei/test/test.txt ./test1.txt

# 在本地机器上ls看一下
bash$ ls
# 远程的test.txt拷到本地的test1.txt了
test.txt  test1.txt
```

拷目录需要加上参数-r
``` c
# 先准备好测试目录testdir,并且包含a.txt, b.txt
bash$ mkdir testdir
bash$ touch testdir/a.txt
bash$ touch testdir/b.txt

# 注意下面scp后面加了参数-r
bash$ scp -r testdir dafei@192.168.1.101:/home/dafei/test/
# 下面显示了远程拷贝的进度条
a.txt               100%    0     0.0KB/s   00:00
b.txt               100%    0     0.0KB/s   00:00

# 在远程机器下查看一下
bash$ tree /home/dafei/test/
# 可以看到testdir被拷到/home/dafei/test/下面了
/home/dafei/test/
├── testdir
│   ├── a.txt
│   └── b.txt
└── test.txt

1 directory, 3 files
```
