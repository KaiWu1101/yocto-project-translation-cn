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
