# Yocto项目快速指南    
原手册链接：https://www.yoctoproject.org/docs/2.7.4/brief-yoctoprojectqs/brief-yoctoprojectqs.html

# 欢迎    
欢迎你来到yocto项目，这篇简短的文档将会为你展示使用yocto构建一个常见镜像的整个过程和步骤。这篇文档也会介绍如何为根据特定的硬件来配置你的项目。在这篇文档中你会使用yocto构建一个嵌入式操作系统:poky
> 注意事项
> - 本文档中涉及到的例子都是假设你是在一个运行着Ubuntu最近发行版的Linux主机上进行开发。 如果你的开发主机不是Linux的话，你也还是通过使用CROss PlatformS(也就是CROPS)设置一个容器来完成本文档中的步骤。你可以查看[yocto开发任务手册来的配置CROPS章节](http://www.yoctoproject.org/docs/2.7.4/dev-manual/dev-manual.html#setting-up-to-use-crops)来了解CROPS的更多信息。
> - 如果你想亲自尝试本文档中的步骤的话，请不要使用[Windows Subsystem for Linux(WSL)](https://en.wikipedia.org/wiki/Windows_Subsystem_for_Linux),yocto项目并不兼容WSL。

如果你想了解yocto项目的一些背景和概念的话，请参考[yocto项目总览和概念手册](http://www.yoctoproject.org/docs/2.7.4/overview-manual/overview-manual.html)。

# 兼容的Linux发行版
确认你的[构建主机(也就是你使用yocto构建目标镜像的主机，或者简单来说是你的开发主机](http://www.yoctoproject.org/docs/2.7.4/ref-manual/ref-manual.html#hardware-build-system-term)满足以下需求:     
- 50Gbytes的可用磁盘空间    
- 使用的是支持yocto项目的Linux发行版(也就是Fedora, openSUSE, CentOS, Debian或者Ubuntu的近期发行版)。想查看具体哪些发行版支持yocto项目的话，可以查看yocto项目参考手册的[受支持的Linux发行版](http://www.yoctoproject.org/docs/2.7.4/ref-manual/ref-manual.html#detailed-supported-distros)章节。关于如何配置你的构建主机的详细信息，你可以查看yocto项目开发任务手册的[准备你的构建主机](http://www.yoctoproject.org/docs/2.7.4/dev-manual/dev-manual.html#dev-preparing-the-build-host)章节。    
- 软件版本
    - Git 1.8.3.1或以上版本
    - tar 1.27或以上版本    
    - Python 3.4.0或以上版本
如果你的构建主机不满足上述要求中的任意一项的话，你可以采取一些措施以确保你可以使用yocto项目，具体可以查看yocto项目参考手册的[Git, tar和 Python的版本要求](http://www.yoctoproject.org/docs/2.7.4/ref-manual/ref-manual.html#required-git-tar-and-python-versions)章节。    

# 构建主机所需的软件包    
你必须在你的构建主机上安装一些必要的软件。以下的命令会可以帮助你在你的Ubuntu构建主机上安装这些所需软件
> 注意
> 如果你想查看所有受支持的Linux发行版所需的软件包的话(你可能使用的不是Ubuntu)，可以查看yocto项目参考手册的[构建主机所需的软件包](http://www.yoctoproject.org/docs/2.7.4/ref-manual/ref-manual.html#required-git-tar-and-python-versions)章节。

```
sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib \
     build-essential chrpath socat cpio python python3 python3-pip python3-pexpect \
     xz-utils debianutils iputils-ping python3-git python3-jinja2 libegl1-mesa libsdl1.2-dev \
     xterm
```

# 使用Git克隆Poky    
一旦你完成了上述的准备步骤后，你需要在你的构建主机上克隆Poky仓库。你可以使用如下命令来克隆Poky仓库:
```
 $ git clone git://git.yoctoproject.org/poky
 Cloning into 'poky'...
 remote: Counting objects: 432160, done.
 remote: Compressing objects: 100% (102056/102056), done.
 remote: Total 432160 (delta 323116), reused 432037 (delta 323000)
 Receiving objects: 100% (432160/432160), 153.81 MiB | 8.54 MiB/s, done.
 Resolving deltas: 100% (323116/323116), done.
 Checking connectivity... done.
```    

移动到Poky目录，然后查看以下仓库中的标签:
```
 $ cd poky
 $ git fetch --tags
 $ git tag
 1.1_M1.final
 1.1_M1.rc1
 1.1_M1.rc2
 1.1_M2.final
 1.1_M2.rc1
    .
    .
    .
 yocto-2.5
 yocto-2.5.1
 yocto-2.5.2
 yocto-2.6
 yocto-2.6.1
 yocto-2.6.2
 yocto-2.7
 yocto_1.5_M5.rc8
```    

在此次示例中，我们将切换到yocto-2.7.4版本的分支。    
```
$ git checkout tags/yocto-2.7.4 -b my-yocto-2.7.4
 Switched to a new branch 'my-yocto-2.7.4'
```
上面的`git checkout`指令会创建一个名为`my-yocto-2.7.4`的分支，这个分支中的文件和yocto 2.7.4版本对应。    
对于如何访问yocto项目相关仓库，如果你想了解更多信息的话，可以查看yocto项目开发任务手册的[定位yocto项目的源文件](http://www.yoctoproject.org/docs/2.7.4/dev-manual/dev-manual.html#locating-yocto-project-source-files)章节。




