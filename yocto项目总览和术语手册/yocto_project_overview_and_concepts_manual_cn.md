<!-- TOC -->

- [文档阅读：Yocto项目总览和术语手册第一章和第二章](#文档阅读yocto项目总览和术语手册第一章和第二章)
- [第一章: 手册总览](#第一章-手册总览)
- [第二章: yocto介绍](#第二章-yocto介绍)     
    - [2.1 什么是yocto](2.1-什么是yocto)
        - [2.1.1 特点](2.1.1-特点)    
        - [2.1.2 挑战](2.1.2-挑战)
    - [2.2 yocto的分层模型（layer model)](2.2-yocto的分层模)
<!-- /TOC -->

# 文档阅读：Yocto项目总览和术语手册第一章和第二章    
原手册链接：https://www.yoctoproject.org/docs/2.7/overview-manual/overview-manual.html
  

这篇手册是对yocto项目的总览，同时对一些常用术语和概念做了解释，并提供相关链接以便深入了解．一共分为四个小章节

1. 手册总览
2. Yocto介绍
3. Yocto开发环境
4. Yocto概念解释

# 第一章: 手册总览
欢迎来到yocto项目总览和术语手册，这个手册通过向新手提供概念讲解，软件概述，最为熟知的开发方法以及其他总览性的信息说明的方法，让新手了解yocto项目。

很短的章节，主要告诉你这个手册其他三章的主要内容：

第二章介绍了yocto，你会在第二章学到yocto的特点和你在学习中会遇到的挑战,你也会接触以下这些概念: 分层模型，组件和工具，开发方法，poky参考发行版, OpenEmbedded构建系统的工作流程和一些yocto术语.
第三章开始让你了解yocto的开发环境，你会学习到有关开源，开发主机， yocto仓库，使用git的工作流程, 简单的git入门以及许可证的知识.
第四章给你介绍各种yocto有关的概念，你会对组件(components), 开发(development),　交叉工具链（ cross-toolchain) 等有个概念上的认识.
这个手册不会提供给你以下信息

不包含一步步具体的开发步骤，具体的有关步骤会在 [Yocto Project Development Tasks Manual](http://www.yoctoproject.org/docs/2.7/dev-manual/dev-manual.html) 这个手册中涉及，而[Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual](http://www.yoctoproject.org/docs/2.7/sdk-manual/sdk-manual.html)这个手册中包含了如何安装SDK为目标硬件开发应用的具体步骤
不包含具体的参考材料，系统变量等参考或者需要查找的信息这个手册中： [Yocto Project Reference Manual](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html). 而[Yocto Project Board Support Package (BSP) Developer's Guide](https://www.yoctoproject.org/docs/2.7/bsp-guide/bsp-guide.html)包含了BSP相关的参考信息，　简而言之，这两个是查表文档，哪天遇到不认识的变量了，就去这两个文档中搜索一下，能找到具体的解释．
不包含诸如如何使用Git这样不应由Yocto文档详细说明的信息，你应该去查阅相应的官方文档或者通过搜索引擎查找资料。    

# 第二章: yocto介绍

这一章分七个小节

## 2.1 什么是yocto
这个在这里不重复介绍了，在[吴雨儿：怎么开始学习yocto?](https://zhuanlan.zhihu.com/p/67453665)一文中我已经提及了．你也可以查看Drew Moseley的[文章](https://www.embedded.com/why-the-yocto-project-for-my-iot-project/)或者观看[这个视频](https://www.youtube.com/watch?v=utZpKM7i5Z4)来了解yocto。
这个小节还介绍了yocto的特点和你学习yocto将面临的困难．

### 2.1.1 特点
- **yocto在行业被普遍接受**: 很多公司企业都使用了yocto作为生产工具，也有很庞大的社区支持
- **yocto支持很多架构**: 比如ARM, MIPS, AMD, PPC等．大多数ODM(原设计生产厂商)，OSV(操作系统零售商)和芯片提供商会制作并提供硬件的BSP包，你也可以为自己的硬件制作BSP包, yocto还支持通过QEMU仿真大多数的硬件设备。
- **镜像及代码地移植非常容易**: Yocto Project的输出可以在不移动到新的开发环境情况下，很容易地就能迁移到新的架构上(不用为了新的架构搭建新的开发环境)。如果你使 yocto制作了自己的镜像(image)和应用，但是你不能长期为此提供维护和支持的话，诸如Wind River，Mentor Graphics，Timesys，和ENEA这样的商业Linux提供商可以接手并提供后续支持。
- **灵活性**: 企业有很多种方法来使用yocto, 比如说可以制作一个linux镜像作为基础(base), 然后把这个镜像给多个不同的部门，让他们在此基础上根据部门产品开发自己的应用，作为各自的产品。通俗一点说就是给几个人一个一样的毛坯房，但是房子里装什么由他们自己决定。
- **适合资源受限的嵌入式或者IOT设备**: 不同于完整的发行版(比如说ubuntu), yocto能让你自定义你的镜像，你可以决定放哪些功能或者模块到你的镜像中，比如说很多设备没有显示屏幕，那么像X11, GTK+, Qt或者SDL之类的组件就不需要安装。最终的镜像会足够小，而且没有多余的功能。
- **全面的工具链支持**: yocto已有的工具链对大部分生产环境已经适用了， 如果你想自定义自己的工具链,在yocto中也能通过更改平台参数来自定义你的工具链,你也可以使用第三方工具链.
- **Mechanism Rules Over Policy**: (译者注: 这个没看懂， 原文如下Focusing on mechanism rather than policy ensures that you are free to set policies based on the needs of your design instead of adopting decisions enforced by some system software provider)
- **使用分层模型(layer model)**: Yocto Project分层架构将相关联功能分组，放置到不同的bundle中，你可以根据项目需求增加层(Layer)这样做的好处是降低项目复杂性和重复性，方便系统扩展，定制化，保持功能有组织地管理。这里的层更多的是并列关系，很少有上下层的关系，不好翻译这个layer, 因为我们看到 层 的时候就会默认有级别关系，上下或者包含的关系。。
- **支持局部构建（partial builds)**：你能单独构建某个包（package)或者层(layer)。 yocto 使用[shared-state cache](https://www.yoctoproject.org/docs/2.7/overview-manual/overview-manual.html#shared-state-cache)方法来实现这一点，（在build目录下有个sstate-control的目录）
- **严格的发布计划**: 大版本发布每六个月一次，分别是每年的四月和十月。
- **丰富的生态系统**: 作为开源项目，yocto有很活跃的社区，开发者和专家也很可靠。
- **二进制文件的高再生性**: 能保证两次构建的image(镜像）基本相同， （core-image-minimal的再生性达到了99.8%). 应该是说yocto对包的依赖关系做了很好的控制和管理。
- **许可证清单**: yocto能提供所有构建所需包的[许可证清单](http://www.yoctoproject.org/docs/2.7/dev-manual/dev-manual.html#maintaining-open-source-license-compliance-during-your-products-lifecycle)，能让你追踪你项目中用到的开源许可证。 简单来说就是让你知道， 你都使用了哪些包，都是哪来的，会不会让你惹上官司)
### 2.1.2 挑战
- **陡峭的学习曲线** Yocto Project拥有陡峭的学习曲线，相似的任务可以有诸多方式完成。有的时候，既定任务会有很多方式去完成，这可能会让你有选择困难症。
- **当你项目出了问题你想弄清楚怎么修改，可能要花费你很多时间和努力**: 这里先建议阅读[yocto避坑指南](https://www.yoctoproject.org/docs/what-i-wish-id-known/) 和 [开始使用yocto项目前的准备工作](http://www.yoctoproject.org/docs/transitioning-to-a-custom-environment/)
- **项目的工作流程可能很让你困惑**: 如果你对传统的桌面或者服务器软件开发很熟悉的话，[Yocto项目的工作流程](https://www.yoctoproject.org/docs/2.7/overview-manual/overview-manual.html#overview-development-environment)可能会让你感觉很迷惑。在桌面开发环境中存在一些能够让你很轻易的下载和安装新包的方法(使用apt-get等），使用工具直接安装远端已经编译好的包。而在yocto中你必须改动你的配置并且重新构建来添加应用或者包。
- **交叉构建环境的不熟悉**: 有时候开发要在目标上运行的代码时，在实际目标上完成编译，执行和测试比在开发主机上运行BitBake构建然后将二进制文件部署到目标上要快得多。为此yocto也为目标板提供了开发工具，但是你也需要把你在目标板上的修改迁移回Yocto的构建环境中(就是你每次都用bitbake来构建镜像然后部署在开发板上进行测试，因此你可能会在你的开发板上进行开发测试，为此yocto也提供了一些开发工具，但是记得把你在开发板上的临时的开发进度整合到yocto项目中)。 Yocto提供了一种折中的方法，当你在开发机上对开发机上的项目做出修改时，你可以只把你更新的包后者软件部署到你的目标板上。    
Yocto的[OE构建系统](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#build-system-term)能为你生成标准格式(也就是RPM, DEB, IPK和TAR)的包文件。你可以通过在目标板上使用rmp或者ipk把这些包文件部署到你目标板的操作系统中。

- **第一次构建会花费很长时间**: 但是因为使用了shared-state cache机制，之后的构建速度会快很多，第一次构建之后，没有改动的部分在第二次构建过程中不会被重新构建（类似于make)
## 2.2 yocto的分层模型（layer model)

简单来说就是将功能分类，便于开发和管理，每一层就是一类功能的集合。你可以开发自己的layer,同时你也和别人分享你的layer.还可以下载别人的layer. 每一个layer中包含了一系列指令告诉构建系统该具体怎么做，怎么构建。

你也可以下载别人的layer然后修改（通常是用你的配置覆盖其中一些配置）来让它符合你的需求。

> **注释**  
> - 尽量使用来自供应商的BSP layer    
> - 熟悉[Yocto Project curated layer index](https://caffelli-staging.yoctoproject.org/software-overview/layers/) 和[OpenEmbedded layer index](http://layers.openembedded.org/layerindex/branch/master/layers/)。 后者包含更多Layer，但并不是都经过了验证。    
> - [Yocto Project Compatible](http://www.yoctoproject.org/docs/2.7/dev-manual/dev-manual.html#making-sure-your-layer-is-compatible-with-yocto-project) 简单来说就是让你的layer通过一个脚本测试，然后填写一个表格,之后yocto授权你使用Yocto project Compatible的Logo来推广你的产品.这个测试一般是作用在BSP layer。    

为了说明分层(layers)是怎么把开发内容模块化的，可以参考对机器的自定义。这种类型的自定义配置通常都会被放在一个特定的层(通常是bsp层)，而不是放在一个通用的层里。此外，应将机器的定制与支持新GUI环境的配方和元数据分隔开来。比如，此时你有好几个layer,一个是关于机器配置的，一个是关于GUI环境的，你必须要理解的是，在bsp层里你还是可以为GUI层的配方添加机器相关的配置而不用污染GUI层。你可以通过bitbake追加文件(.bbappend)来做到这一点，这个章节的后面会介绍bbappend文件。

> 关于BSP层结构的相关信息，请参考[Yocto项目BSP开发者手册](http://www.yoctoproject.org/docs/2.7/bsp-guide/bsp-guide.html)    

通常，你刚克隆或者创建的源目录(就是你的poky目录)中就包含了一些常见的层和BSP层，源目录中以meta-开头的文件夹就是yocto项目自带的layer.当然你也可以克隆完poky后新建以meta-开头的文件夹来创建你自己的layer。

> layer以meta-开头并不是强制性的要求，但这是yocto社区的共识。

比如说如果你查看那poky仓库的[目录树](https://git.yoctoproject.org/cgit/cgit.cgi/poky/tree/)，你就能看到好几个layer的仓库: meta, meta-skeleton, meta-sefttest, meta-poky和meta-yocto-bsp。每一个仓库都代表了不同的layer。
如果开发者想了解如果创建layer，参考Yocto项目开发任务手册的[理解和创建layer](http://www.yoctoproject.org/docs/2.7/dev-manual/dev-manual.html#understanding-and-creating-layers)章节。

## 2.3 组件和工具    
yocto自带了很多开源的开发工具，这些工具和参考发行版poky以及OpenEmbedded构建系统是分开的（独立，不被poky或者OE包含).

### 2.3.1 开发工具       
- **[CROPS](https://git.yoctoproject.org/cgit/cgit.cgi/crops/about/)**：使用了[Docker容器](https://www.docker.com/)技术的开源跨平台开发框架。CROPS提供一套易于管理且扩展性强的环境，帮助你在Windows，Linux和Mac OS X系统上构建目标代码。简单来说就是如果你是windows，但是你要使用yocto开发的话，这东西用得着。本来就是使用linux的开发者应该不需要它。
- devtool: 这个命令行工具是eSDK中很重要的一部分（原文是conerstone part),你可以使用devtool来帮助构建，测试和打包软件。你也能用它来选择你想集成到映像(image)中的软件和功能。

    _devtool_有一系列的子命令来让你添加，修改或者升级你的recipes. 当你用_devtool add_添加软件或者功能的时候，它会自动帮你生成一个recipe. 当你使用_devtool modify_的时候，指定的recipe(配方)会被用来决定从哪里得到源代码已经如何为之打补丁。这两种情况下，devtool都会为你设置一个环境，以确保在构建配方时使用的是在你控制下的源码树，以便于你可以根据需要对源码进行修改。默认情况下，新的配方文件和新的源代码都会存储在eSDK下的工作区。 _devtool upgrade_命令会更新旧的配方文件，这样你就能根据新的源代码和配方构建一个软件包。
关于devtool 详情请阅读Yocto项目应用开发和可扩展软件开发工具包手册的[在SDK工作流程中使用devtool](http://www.yoctoproject.org/docs/2.7/sdk-manual/sdk-manual.html#using-devtool-in-your-sdk-workflow)这一章节    

- **可扩展软件开发工具包(eSDK)**: eSDK提供了交叉开发的工具链和为具体镜像而裁剪的库。eSDK使得添加新的应用和库文件，修改源代码，在目标板上测试修改以及把其他部件集成到OE构建系统中这些操作变得更加容易。 借由为Yocto Project环境裁剪的一套功能强大的devtool指令集，eSDK可以为你提供更好的工具链体验    
   
    关于eSDK的更多信息可以参考[Yocto项目应用开发和可扩展软件开发工具包手册](http://www.yoctoproject.org/docs/2.7/sdk-manual/sdk-manual.html)    

**Toaster**: yocto项目构建系统的网页界面版本, 能让你配置，运行和查看构建信息。查看[Toaster用户手册](http://www.yoctoproject.org/docs/2.7/toaster-manual/toaster-manual.html)可以得到更多Toaster信息    


### 2.3.2 生产工具    
- **Auto Upgrade Helper**: 如果上游对应配方文件更新的话，这个工具配合[OE构建系统](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#build-system-term)(Bitbake和OE-Core)自动升级更新配方文件,这个工具和bitbake一起能自动帮你升级从远端获取的recipes，我的理解是如过你的一些layer或者recipes是使用的别人的，或者厂家的，这个工具在他人更新这些layer或者recipes的时候帮你本地也自动升级。
- **Recipe Reporting System**: 这个系统跟踪Yocto Project可用的配方版本，帮助你管理你的项目同时提供给你一个关于你项目的概述，简单的说是告诉你的使用的recipes是哪来的，哪些在继续更新，哪些版本能用。这个系统是基于[OE的层索引目录 OpenEmbedded Layer Index](http://layers.openembedded.org/layerindex/layers/)构建。OpenEmbedded Layer Index上索引了一些yocto的关键的layers.    
- **Patchwork**: [Patchwork](http://jk.ozlabs.org/projects/patchwork/)这个项目由[OzLabs](http://ozlabs.org/)发起。Yocto Project使用Patchwork作为管理工具来管理补丁，让开发者更有效率地为项目做更改。        
- **AutoBuilder**: Autobuilder用来做自动测试和QA(quality assurance)。每个人都可以利用公共的AutoBuilder查看Poky的master分支状态。
> 这个工具基于[buildbot](https://buildbot.net/)。 

    Yocto Project的目标之一是创建一个能够进行自动测试和QA质量保证的开源项目。为了达到这个目标，Yocto Project鼓励开发社区发布QA和测试计划，公开演示QA和测试计划，也鼓励为开发社区提交自动化测试工具以及QA流程。

- **Cross-Prelink**:  相比较于运行时进行操作，预链接(preliking)是一个预先计算由动态链接器生成的加载地址和链接表的过程。这样做的好处是，提升应用程序启动性能，减少应用程序之间共享库文件的内存使用量。    

    cross-prelink是由[Jakub Jelínek](http://people.redhat.com/jakub/prelink.pdf)设计的prelink的一个变体，prelink和cross-prelink在同一个仓库不同的两个分支。通过提供一个模拟运行时动态连接器(即glibc衍生的ld.so仿真)cross-prelink project扩展了prelink软件的能力，可以对sysroot环境进行预先链接。不仅如此，经过cross-prelink的软件也拥有在类sysroot环境中工作的能力。    

    动态链接器根据一系列因素，例如映射地址，lib库使用情况，lib函数冲突等，进行标准的加载地址计算。prelink工具利用动态链接器的输入信息，决定那些可被动态链接和执行的ELF二进制文件的唯一加载地址(这些二进制文件就是指那些共享的库文件，就是决定*.so一类动态库文件的加载地址)。prelink工具根据预先计算的信息修改ELF二进制文件，这样做的好处是，重复利用COW（译者注：[COW写时复制](https://blog.csdn.net/u010712083/article/details/8963202)）内存页的共享lib库文件，提升加载能力，降低内存消耗。

    由于依赖目标设备的动态链接器，prelink 项目仅仅支持在目标设备运行prelink，这导致开发跨平台系统时会产生诸多问题。cross-prelink添加了一个运行在（开发）主机上的综合动态加载器，允许不在目标系统上进行cross-prelinking。

- **Pseudo**: 在构建的过程中可能会需要系统权限，这个工具能让你在构建的过程中作为非root用户暂时获得root权限，你能直接用这个工具，也能通过变量LD_PRELOAD来设置。详情可参考[fakeroot](http://man.he.net/man1/fakeroot)。    

### 2.3.3 OE构建系统组件    



