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

# 构建你自己的镜像    
使用如下步骤来构建你的镜像，这个构建过程会根据源码生成一个linux发行版，包括工具链。
> 如果你的工作网络存在防火墙，并且没有设置代理的话，你可能在网络上获取源代码时会遇到一些问题(比如说下载失败，或者git出错)。    
> 如果你不了解你的代理设置，可以咨询你当地的网络管理员。检查你的网络浏览器设置也是很重要的一个步骤。最后，你可以在Yocto项目的Wiki页面的**[“在代理网络下工作”](https://wiki.yoctoproject.org/wiki/Working_Behind_a_Network_Proxy)这个章节内了解更多这方面的信息。

1. **初始化构建环境**: 在poky目录下，运行环境设置脚本[oe-init-build-env](http://www.yoctoproject.org/docs/2.7.4/ref-manual/ref-manual.html#structure-core-script)在你的构建主机上定义Yocto项目的构建环境。    
```    
     $ cd ~/poky    
     $ source oe-init-build-env    
     You had no conf/local.conf file. This configuration file has therefore been    
     created for you with some default values. You may wish to edit it to, for    
     example, select a different MACHINE (target hardware). See conf/local.conf    
     for more information as common configuration options are commented.    

     You had no conf/bblayers.conf file. This configuration file has therefore been    
     created for you with some default values. To add additional metadata layers    
     into your configuration please add entries to conf/bblayers.conf.    

     The Yocto Project has extensive documentation about OE including a reference    
     manual which can be found at:    
         http://yoctoproject.org/documentation    

     For more information about OpenEmbedded see their website:    
         http://www.openembedded.org/    

     ### Shell environment set up for builds. ###    

     You can now run 'bitbake <target>'    

     Common targets are:
         core-image-minimal
         core-image-sato
         meta-toolchain
         meta-ide-support

     You can also run generated qemu images with a command like 'runqemu qemux86'    
```    
另外，这个脚本会创建[构建目录](http://www.yoctoproject.org/docs/2.7.4/ref-manual/ref-manual.html#build-directory)(build_xxxx),并且构建目录位于源目录(这里可以理解为克隆仓库后poky的根目录)之下。运行完这个脚本之后，你的工作目录会被切换到构建目录。构建结束以后，构建目录下就包含了构建过程中生成的各种文件。    
> 译者注: 比如你的yocto目录是xxxx/poky/，你在运行了`source oe-init-build-env`之后，poky目录下就会生成一个build目录。

2. **检查你的本地配置文件**: 当你设置好构建环境后，一个本地配置文件local.conf会出现在构建目录的子目录conf目录下(译者注: poky/build/conf或者poky/build_xxx/conf)。在这个例子中，默认设置是将qemux86作为构建目标的，构建生成的镜像可以在虚拟机内仿真。包管理工具设置为RPM。
> Tip    
> 你可以通过使用镜像文件来大幅度加速你的构建过程并且保证下载源码的顺利进行。如果你想使用镜像，把下面几行添加到你构建目录中的本地配置文件中(poky/build/local.conf)。
>  ``` 
>  SSTATE_MIRRORS = "\
>     file://.* http://sstate.yoctoproject.org/dev/PATH;downloadfilename=PATH \n \
>     file://.* http://sstate.yoctoproject.org/2.6.4/PATH;downloadfilename=PATH \n \
>     file://.* http://sstate.yoctoproject.org/2.7.4/PATH;downloadfilename=PATH \n \
>     "
> ```
> 上述的几行代码展示了如何为Yocto 2.6.4, 2.7.4添加sstate路径。想查看sstate的完整索引，可以查看这里http://sstate.yoctoproject.org/. (译者注: 这里的sstate目录是增量构建的存储目录,如果添加了上述代码到local.conf中，可以理解为我们使用了预构建的文件，然后我们的本地构建系统在这个预构建的基础上继续进行构建，而不是从头开始构建，这样自然会减少很多的构建时间)。    

3. **开始构建**: 接着，我们使用如下命令为目标硬件构建一个操作系统镜像(在本例中也就是core-image-sato)    
>         ` $bitbake core-image-sato `
对于如何使用bitbake命令，可以查看Yocto项目总览和概念手册的[**"Bitbake"**](http://www.yoctoproject.org/docs/2.7.4/overview-manual/overview-manual.html#usingpoky-components-bitbake)章节，或者你也可以查看Bitbake用户手册的[**"Bitbake Command"**](http://www.yoctoproject.org/docs/2.7.4/bitbake-user-manual/bitbake-user-manual.html#bitbake-user-manual-command)章节。    

4. 使用QEMU运行镜像: 一旦目标镜像构建完成后，你可以启动QEMU了(Yocto项目随附的Quick Emulator)。
>        ` $bitbake qemux86`    
如果你想学习更多关于如何使用QEMU的信息，查看Yocto项目开发任务手册的[**"使用QEMU"**](http://www.yoctoproject.org/docs/2.7.4/dev-manual/dev-manual.html#dev-manual-qemu)章节。

5. 离开QEQMU: 点击QEMU窗口的shutdown图标，或者在你调用QEMU的终端内输入Ctrl-C来终止QEMU。



