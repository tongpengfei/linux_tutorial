[toc]
# linux编程入门(一) -使用VirtualBox在Windows下安装Ubuntu虚拟机

如果平时使用的是windows，又没有装双系统的时候，可以先在windows下装个虚拟机练练手。  

## 需要准备的软件
* VirtualBox虚拟机  
VirtualBox是个开源的虚拟机软件，另一个常用的是VMware。VirtualBox是跨平台的，也就是说VirtualBox本身可以安装在windows,linux,mac下，安装虚拟机只要有镜像就可以，比如安装windows虚拟机，就需要windows镜像，安装linux虚拟机，就需要linux镜像，以前也装过mac虚拟机，不过mac虚拟机体验一点都不好。还是到[VirtualBox官网](https://www.virtualbox.org/wiki/Downloads)上[下载windows版](https://download.virtualbox.org/virtualbox/6.0.4/VirtualBox-6.0.4-128413-Win.exe)安装文件。

* Ubuntu系统镜像  
linux有很多版本，各linux版本的使用差异不大，学会一个其他的就很容易上手了，做为入门，咱们选择ubuntu，ubuntu本身也有很多版本，可以下载最新的LTS(LTS = Long-Term Support,长期技术支持)版本即可。首先到[Ubuntu官网](https://www.ubuntu.com/download/desktop)上[下载](http://mirrors.njupt.edu.cn/ubuntu-releases/18.04.2/ubuntu-18.04.2-desktop-amd64.iso)系统镜像文件,后面安装系统时候需要用到。 

## 安装VirtualBox
和安装一般exe一样,略

## 安装Ubuntu虚拟机
![step0](../image/virtualbox/virtualbox_0.png)
![step1](../image/virtualbox/virtualbox_1.png)
![step2](../image/virtualbox/virtualbox_2.png)
![step3](../image/virtualbox/virtualbox_3.png)
![step4](../image/virtualbox/virtualbox_4.png)
![step5](../image/virtualbox/virtualbox_5.png)
![step6](../image/virtualbox/virtualbox_6.png)
![step7](../image/virtualbox/virtualbox_7.png)
![step8](../image/virtualbox/virtualbox_8.png)
![step9](../image/virtualbox/virtualbox_9.png)
![step10](../image/virtualbox/virtualbox_10.png)
![step11](../image/virtualbox/virtualbox_11.png)
![step12](../image/virtualbox/virtualbox_12.png)
![step13](../image/virtualbox/virtualbox_13.png)
![step14](../image/virtualbox/virtualbox_14.png)
![step15](../image/virtualbox/virtualbox_15.png)
![step16](../image/virtualbox/virtualbox_16.png)
![step17](../image/virtualbox/virtualbox_17.png)
![step18](../image/virtualbox/virtualbox_18.png)
![step19](../image/virtualbox/virtualbox_19.png)
![step20](../image/virtualbox/virtualbox_20.png)
![step21](../image/virtualbox/virtualbox_21.png)
![step22](../image/virtualbox/virtualbox_22.png)
![step23](../image/virtualbox/virtualbox_23.png)
![step24](../image/virtualbox/virtualbox_24.png)
![step25_0](../image/virtualbox/virtualbox_25_0.png)
![step25_1](../image/virtualbox/virtualbox_25_1.png)
![step25_2](../image/virtualbox/virtualbox_25_2.png)
![step26](../image/virtualbox/virtualbox_26.png)
![step27](../image/virtualbox/virtualbox_27.png)
![step28](../image/virtualbox/virtualbox_28.png)
![step29](../image/virtualbox/virtualbox_29.png)

## 关闭虚拟机
在终端输入命令sudo halt关机,sudo表示用管理员权限执行命令halt
``` c
tpf@tpf-ubuntu:~$ sudo halt
```
![halt](../image/virtualbox/shutdown/halt.png)

## 重启虚拟机
在终端输入命令sudo reboot重启ubuntu
``` c
tpf@tpf-ubuntu:~$ sudo reboot
```
![reboot](../image/virtualbox/shutdown/reboot.png)


## 设置虚拟机的共享文件夹
设置虚拟机的共享文件夹就可以在windows,linux下都能访问该文件夹下的文件了。  
以下是设置步骤:  
![share0](../image/virtualbox/share_file/share_file_0.png)
![share1](../image/virtualbox/share_file/share_file_1.png)
![share2](../image/virtualbox/share_file/share_file_2.png)
![share3](../image/virtualbox/share_file/share_file_3.png)
![share4](../image/virtualbox/share_file/share_file_4.png)
![share5](../image/virtualbox/share_file/share_file_5.png)
![share6](../image/virtualbox/share_file/share_file_6.png)
![share7](../image/virtualbox/share_file/share_file_7.png)


