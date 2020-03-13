- [Chapter 1 The Yocto Project 开发任务手册](#chapter-1-the-yocto-project-开发任务手册)
  - [1.1 欢迎](#11-欢迎)
  - [1.2 其他](#12-其他)
- [Chapter 2. 环境搭建](#chapter-2-环境搭建)
  - [2.1 新建团队开发环境](#21-新建团队开发环境)
  - [2.2 准备构建主机](#22-准备构建主机)
    - [2.2.1 准备原生Linux主机](#221-准备原生linux主机)
    - [2.2.2 使用CROss PlatformS (CROPS)](#222-使用cross-platforms-crops)
  - [2.3 定位Yocto Project源文件](#23-定位yocto-project源文件)
    - [2.3.1 访问代码仓库](#231-访问代码仓库)
    - [2.3.2 访问发布索引](#232-访问发布索引)
    - [2.3.3 使用下载页面](#233-使用下载页面)
    - [2.3.4 访问Nightly Builds](#234-访问nightly-builds)
  - [2.4 克隆并切换到分支](#24-克隆并切换到分支)
    - [2.4.1 克隆Poky仓库](#241-克隆poky仓库)
    - [2.4.2 切换Poky分支](#242-切换poky分支)
    - [2.4.3 根据Tag切换Tag分支](#243-根据tag切换tag分支)

# Chapter 1 The Yocto Project 开发任务手册

## 1.1 欢迎
欢迎阅读Yocto Project开发任务手册！这篇文档提供在Yocto Project环境（例如开发在目标设备上运行的嵌入式Linux系统以及用户空间应用）中进行开发所需的相关步骤。本文档将相关过程分组为higher-level章节进行说明。根据不同的话题，章节可能包含概括性的介绍或者细节步骤。

本文档将提供如下信息：

- 帮助你熟悉Yocto Project。例如：如何搭建一套构建主机并使用Yocto Project代码仓库
- 如何提交改动到Yocto Project。这些改动可能是改进，新功能，或是bug修复。
- 使用Yocto Projcet开发image及应用时“日常”所需进行的任务。例如，如何创建layer，自定义image，编写recipe...

本文档不提供如下信息：

- 重复性的逐步指导：例如《Yocto Project 应用开发以及可扩展软件开发工具包（eSDK）手册》包含如何安装用来开发目标硬件应用的SDK的详细说明。
- 参考或概念资料：这些有对应的文档介绍，例如系统变量由《Yocto Project参考文档》介绍。
- 不由Yocto Project定义的，公开的信息/细节： 例如如何使用Git可以在网上找到更官方更好的指导。

## 1.2 其他
本文档包含很多方面的内容，希望完全理解这些内容，推荐阅读更多补充资料。如果想了解Yocto Project的介绍性信息，可以访问Yocto Project网站。如果你希望能在不了解Yocto Project的情况下能快速构建image，请参考《Yocto Project 快速构建手册》。

在《Yocto Project 参考文档》里，你可以找到链接和其他文档的综合列表。

# Chapter 2. 环境搭建

本章将介绍相关步骤以准备开始使用Yocto Project。你将学习到创建使用Yocto Project进行开发的团队环境，如何搭建构建主机，如何定位Yocto Project代码仓库，以及如何创建本地Git仓库。

## 2.1 新建团队开发环境
你可能无法立即明白你可以怎么在团队开发环境中使用Yocto Project，或是如何为一个大型开发团队规模化它（scale it）。Yocto Project优势之一就是，它非常的灵活。因此，你可以将它适用于不同的应用案例和场景。然而，如果你旨在创建一个能够在大型团队使用的环境，灵活性同样可能让你遇到很多困难。
为了帮助你理解如何创建这样的环境，本节展示了一个工作流程。这个high level流程提供了一些过去成功的经验，实践，解决方案以及可用的技术。请记住，以下只是起点，你完全可以为特定的工作环境定制化这些步骤。

1. ***谁会进行开发呢***: 你需要明白谁会工作在Yocto Project中并且定义他们的角色。为了完成步骤二、三以准备硬件并搭建开发环境的硬件拓扑，你必须做出这样的决定。

有如下角色:

+ ***应用开发者***: 应用开发者在软件栈上进行应用级的工作。

+ ***核心系统开发者***: 核心系统开发者进行操作系统image内容的开发。

+ ***构建工程师***: 构建工程师管理Autobuilder和发布。不是所有的环境都需要构建工程师。

+ ***测试工程师***: 测试工程师创建并管理自动测试，确保所有应用以及核心系统开发达到了期望的质量标准。

2. ***获取硬件***: 根据团队规模以及构成，完成硬件的准备。理想状况是，任何开发者，构建或测试工程师都能使用运行所支持的Linux分发版本的系统。这些系统原则上应该是高性能的。(例如，双六核Xeons CPU，24GB RAM，足够的硬盘空间). 对于使用于测试或运行Autobuilder的机器，越是高性能越可以保证效率。

3. ***了解硬件拓扑***: 一旦明白硬件信息以及团队构成，你就可以明白开发环境的硬件拓扑。你可以图形化展示开发环境中的机器以及他们的角色。

4. ***使用Git作为代码控制管理(SCM)***: 建议将你的元数据（例如recipes，配置文件，类等）和任何你再开发的软件保存在兼容OpenEmbedded构建系统的控制管理工具中。Yocto Project团队强烈建议使用Gti。Git是分布式系统，易于备份，也支持离线工作，然后再链接回远程仓库。

> **注释**  
> 更多BitBake相关内容请参考《BitBake用户手册》

搭建Git服务，像http://git.yoctoproject.org是容易的，它使用gitolite服务器端软件，并使用cgit生成网页端接口，让你查看仓库。gitolite使用SSH key验证用户，允许通过控制分支的方式让你能够一点一点的改动仓库。

> **注释**  
> 搭建这些服务不在本手册讨论范围内。然而，以下网站可以帮助到你：  
> [*Git documentation*](http://git-scm.com/book/ch4-8.html): 如何在服务器端安装gitolite  
> [*Gitolite*](http://gitolite.com/): gitolite相关  
> [*Interfaces, frontends, and tools*](https://git.wiki.kernel.org/index.php/Interfaces,_frontends,_and_tools): 如何创建Git接口和前端

5. ***搭建应用开发机器***: 如前文所说，应用开发者在已有软件栈上开发应用。搭建应用开发机器有以下最佳实践:

+ 使用包含软件栈并且已构建好的工具链。然后，在软件栈上开发应用。这个方法适用于少量的相对独立的应用开发过程。

+ 保持更新你的交叉开发工具栏。你可以通过下载新的工具链或者通过opkg包管理系统来更新你已有的工具链。具体方案依赖于本地策略。

+ 在本地不同路径安装多份工具链以开发不同版本的应用

6. ***搭建核心开发机器***: 如前文所说，核心开发者开发操作系统内容。搭建开发image的机器有一下最佳实践:

+ 本地需要有OpenEmbedded构建系统，这样开发者可以在本地构建软件栈

+ 尽量不要改动核心系统，在核心系统上创建layer以完成你的工作。这样可以尽可能地在更新核心系统或者板卡支持包（BSP）时拥有可移植性。

+ Share layers amongst the developers of a particular project and contain the policy configuration that defines the project.

7. ***搭建Autobuilder***: Autobuilders往往是开发中的核心部分。它获取各个开发者的改动在一起，并测试。基于自动化构建和测试环境，可以决定是否发布。Autobuilders同样允许“持续集成”以测试软件组件和回归识别及追溯。

参考["Yocto Project Autobuilder"](http://autobuilder.yoctoproject.org/)已阅读更多Autobuilders和buildbot相关信息。Yocto Project团队认为这份文档很合适，例子就是Yocto Project Autobuilders, Yocto Project团队用它测试项目的整体健康状况。

这个系统的特点有:

+ 当提交使构建失败时，突出显示

+ 填充sstate缓存，开发者可以直接获取缓存，而无需本地构建

+ 允许提交触发钩子，以触发新的构建任务

+ 允许触发在QEMU中的镜像自动启动和测试

+ 同时支持增量构建和重新构建

+ 共享输出，允许开发者测试（developer testing）和历史回归调查（historical regression investigation）

+ 创建可以用来发布的输出

+ 允许构建schedule以高效利用资源

8. ***搭建测试机器***: 少数共用，高性能的系统用作测试。开发可以使用这些机器进行更多测试，同时仍可以使用自己的开发环境进行本地开发。

9. ***将策略和流程文档化***: Yocto Project使用层级结构和pull model。脚本可以和用来创建并发送Pull Request(i.e. create-pull-request and send-pull-request). Pull model和其他开源项目一样，各个维护者只需要对项目特定部分负责，最终由单一维护者处理最终的合并。

> **注释**  
> 你也可以使用push model（collective?），gitolite软件同时支持push model和pull model。

和任何开发环境一样，将策略文档化，作为项目指导以便所有人理解，是非常重要的。在项目指导中，有良好的结构化提交信息也是一个不错的主意。好的提交信息对于任何时候来回看并理解改动，是非常必要的。

如果你发现必须改动核心层的代码时，尽快与社区分享是很值得的。当你发现有需要改动的地方时，可能社区的其他人正需要这个改动。

10.   ***开发环境总结***: 除开上述步骤以外，Yocto Project开发环境还有以下最佳实践：

+ 使用Git作为代码控制系统

+ 在Layer中维护你的元数据。阅读Yocto Project概述及概念手册中的["The Yocto Project Layer Model" section](http://www.yoctoproject.org/docs/2.7/overview-manual/overview-manual.html#the-yocto-project-layer-model)章节，以及["Understanding and Creating Layers"](https://www.yoctoproject.org/docs/2.7/dev-manual/dev-manual.html#understanding-and-creating-layers)章节以了解更多关于layer的信息。

+ 使用不同的Git仓库以区分离目元数据和代码。阅读Yocto Project概述及概念手册中的["Yocto Project Source Repositories"](http://www.yoctoproject.org/docs/2.7/overview-manual/overview-manual.html#yocto-project-repositories)章节以了解更多关于这些仓库的信息。阅读["Locating Yocto Project Source Files"](https://www.yoctoproject.org/docs/2.7/dev-manual/dev-manual.html#locating-yocto-project-source-files)章节以了解如何在本地创建相关的Yocto Project Git仓库。

+ 在需要的地方创建shared state cache([SSTATE_DIR](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#var-SSTATE_DIR))目录。例如，在同一组织中开发者所使用的系统上创建sstate cache，共享同样的代码目录。

+ 搭建AutobuilderSet，填充sstate cache和代码目录。

+ Yocto Project社区鼓励你发送补丁以修复bug或者增加功能。如果你想提交补丁，请遵循提交指南["Submitting a Change to the Yocto Project"](https://www.yoctoproject.org/docs/2.7/dev-manual/dev-manual.html#how-to-submit-a-change)以编辑良好的提交信息。

+ 尽可能早的将改动发送过来，因为其他人很可能会遇到相同的问题。发送方式请参考["Submitting a Change to the Yocto Project"](https://www.yoctoproject.org/docs/2.7/dev-manual/dev-manual.html#how-to-submit-a-change)。邮件地址可参考Yocto Project参考文档中的["Mailing Lists"](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#resources-mailinglist)章节。

## 2.2 准备构建主机
本节介绍如何为[构建主机](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#hardware-build-system-term)搭建系统，以使用Yocto Project进行开发。推荐使用原生Linux机器，或者也可以在主机（Linux, Mac, Windows）上使用能利用Docker容器的[CROPS](https://git.yoctoproject.org/cgit/cgit.cgi/crops/about/)。

> **注释**  
> 不支持Windows Subsystem for Linux(WSL)。Yocto Project不兼容WSL。

为了使用Yocto Project，构建主机准备好之后，根据具体需求，你还需要完成一些步骤。以下是关于如何为开发BSP和内核开发所需准备的参考信息：

+ ***BSP开发***: 请参考Yocto Project Board Support Package (BSP) Developer's Guide中的["Preparing Your Build Host to Work With BSP Layers"](http://www.yoctoproject.org/docs/2.7/bsp-guide/bsp-guide.html#preparing-your-build-host-to-work-with-bsp-layers) 章节。

+ ***内核开发***: 请参考Yocto Project Linux Kernel Development Manual的["Preparing the Build Host to Work on the Kernel"](http://www.yoctoproject.org/docs/2.7/kernel-dev/kernel-dev.html#preparing-the-build-host-to-work-on-the-kernel)章节。

### 2.2.1 准备原生Linux主机
完成以下步骤为Yocto Project构建主机准备原生Linux机器：

1. ***使用一个被支持的Linux版本***: 你应当有一个基于Linux的主机系统。你可以选用Fedora, OpenSUSE, Debian, Ubuntu, 或者CentOS，他们经常被验证于Yocto Project，官方支持。参考《Yocto Project参考文档》的["Supported Linux Distributions"](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#detailed-supported-distros)以及[Distribution Support](https://wiki.yoctoproject.org/wiki/Distribution_Support)，你可以了解到被验证的发行版本以及状态。

2. ***足够的空间Have Enough Free Memory***: 系统至少需要50GB的空间以构建镜像。

3. ***工具版本最低需求***: OpenEmbedded构建系统需要满足以下版本要求才能运行：

+ Git 1.8.3.1 或以上

+ tar 1.27 或以上

+ Python 3.4.0 或以上

如果主机不满足以上任一版本需求，你需要一些步骤以继续使用Yocto Project。请参考《Yocto Project参考手册》的["Required Git, tar, and Python Versions"](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#required-git-tar-and-python-versions)。

4. ***安装开发主机包***: 根据使用Yocto Project完成的目标的不同，需要的开发主机包也不一样。总体来说，如果你想覆盖所有案例，包的总量会很大。  
所需包列表，请参考《Yocto Project参考手册》["Required Packages for the Build Host"](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#required-packages-for-the-build-host)

完成以上步骤后，你可以准备在Linux机器上准备开发路径了。如果你打算使用BitBake, 请参考["Cloning the poky Repository"](https://www.yoctoproject.org/docs/2.7/dev-manual/dev-manual.html#cloning-the-poky-repository)。如果你打算使用eSDK，请参考《Yocto Project应用开发和eSDK手册》["Using the Extensible SDK"](http://www.yoctoproject.org/docs/2.7/sdk-manual/sdk-manual.html#sdk-extensible)。如果你工作和内核有关，请参考[《Yocto Project Linux内核开发手册》](http://www.yoctoproject.org/docs/2.7/kernel-dev/kernel-dev.html)。如果你准备使用Toaster，请参考《Toaster用户手册》["Setting Up and Using Toaster"](http://www.yoctoproject.org/docs/2.7/toaster-manual/toaster-manual.html#toaster-manual-setup-and-use)章节。 

### 2.2.2 使用CROss PlatformS (CROPS)
使用CROPS，你就可以创建与操作系统无关的Yocto Project开发环境。你可以在Windows, Mac 或者Linux机器上使用容器，进行Yocto Project开发。

完成以下步骤，以使你的Windows, Mac或者Linux机器作为Yocto Project构建主机：

1. ***决定构建主机需求***: 你需要在主机上安装Docker。你可能还需要安装其他软件以支持Docker容器。请参考[Docker安装页面](https://docs.docker.com/install/#supported-platforms)以了解需求。

2. ***选择哪些需要安装***: 根据是否满足系统要求，你需要安装Docker CE Stable或者Docker Toobox。大多数情况是安装Docker CE。然而，如果主机不满足需求（Windows10之前的版本或者Windows10家庭版），你需要安装的则是Docker Toolbox。

3. ***浏览安装页面***: 点击使用于主机原生系统的Docer版本。[Docker安装页面](https://docs.docker.com/install/#supported-platforms)

4. ***安装软件***: 一旦你理解了所有前提需求，你可以下载并安装对应软件。根据对应机器和软件类型进行下载安装：

+ [Docker CE for Windows](https://docs.docker.com/docker-for-windows/install/#install-docker-for-windows-desktop-app)

+ [Docker CE for Macs](https://docs.docker.com/docker-for-mac/install/#install-and-run-docker-for-mac)

+ [Docker Toolbox for Windows](https://docs.docker.com/toolbox/toolbox_install_windows/) (不符要求的Windows版本)

+ [Docker Toolbox for MacOS](https://docs.docker.com/toolbox/toolbox_install_mac/) (不符要求的Mac版本)

+ [Docker CE for CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)

+ [Docker CE for Debian](https://docs.docker.com/install/linux/docker-ce/debian/)

+ [Docker CE for Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)

+ [Docker CE for Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)

5. ***（可选）了解Docker***: 如果你还不甚了解Docker和容器概念，你可以在这里学习更多 - https://docs.docker.com/get-started/.

6. ***启动Docker或者Docker Toolbox***: 你可以启动Docker或者Docker Toolbox，开发主机上会有一个终端出现。

7. ***启动容器，使用Yocto Project***: 访问https://github.com/crops/docker-win-mac-docs/wiki 以了解对应主机系统(i.e. Linux, Mac, or Windows)更多信息。

完成这些步骤后，你就有了Poky, Extensible SDK, Toaster容器。你可以点击链接以了解如果使用这些容器。

当你启动容器后，你就好像是在原生Linux机器上开发一样。
[Poky容器](https://www.yoctoproject.org/docs/2.7/dev-manual/dev-manual.html#cloning-the-poky-repository)
[Extensible SDK容器](http://www.yoctoproject.org/docs/2.7/sdk-manual/sdk-manual.html#sdk-extensible)
[Toaster容器](http://www.yoctoproject.org/docs/2.7/toaster-manual/toaster-manual.html#toaster-manual-setup-and-use)

## 2.3 定位Yocto Project源文件
本节为你展示如果定位并访问Yocto Project源文件。你将创建并使用这些本地文件。

> **Notes**  
> + Git相关信息请阅读《Yocto Project概述及概念手册》["Git"](http://www.yoctoproject.org/docs/2.7/overview-manual/overview-manual.html#git)章节 
> + Yocto Project代码仓库信息请阅读《Yocto Project概述及概念手册》["Yocto Project Source Repositories"](http://www.yoctoproject.org/docs/2.7/overview-manual/overview-manual.html#yocto-project-repositories)

### 2.3.1 访问代码仓库
Working from a copy of the upstream Yocto Project Source Repositories is the preferred method for obtaining and using a Yocto Project release. You can view the Yocto Project Source Repositories at http://git.yoctoproject.org. In particular, you can find the poky repository at http://git.yoctoproject.org/cgit/cgit.cgi/poky/.

Use the following procedure to locate the latest upstream copy of the poky Git repository:

1. ***访问仓库***: Open a browser and go to http://git.yoctoproject.org to access the GUI-based interface into the Yocto Project source repositories.

2. ***选择仓库***: Click on the repository in which you are interested (e.g. poky).

3. ***找到用以克隆的链接***: At the bottom of the page, note the URL used to clone that repository (e.g. http://git.yoctoproject.org/poky).

> **注释**  
> For information on cloning a repository, see the "Cloning the poky Repository" section.

### 2.3.2 访问发布索引
Yocto Project maintains an Index of Releases area that contains related files that contribute to the Yocto Project. Rather than Git repositories, these files are tarballs that represent snapshots in time of a given component.

> **Tip**  
> The recommended method for accessing Yocto Project components is to use Git to clone the upstream repository and work from within that locally cloned repository. The procedure in this section exists should you desire a tarball snapshot of any given component.

Follow these steps to locate and download a particular tarball:

1. ***访问发布索引***: Open a browser and go to http://downloads.yoctoproject.org/releases to access the Index of Releases. The list represents released components (e.g. bitbake, sato, and so on).

> **注释**  
> The yocto directory contains the full array of released Poky tarballs. The poky directory in the Index of Releases was historically used for very early releases and exists now only for retroactive completeness.
Select a Component: Click on any released component in which you are interested (e.g. yocto).

2. ***找到Tar包***: Drill down to find the associated tarball. For example, click on yocto-2.7 to view files associated with the Yocto Project 2.7 release (e.g. poky-warrior-21.0.0.tar.bz2, which is the released Poky tarball).

3. ***下载Tar包***: Click the tarball to download and save a snapshot of the given component.

### 2.3.3 使用下载页面
The Yocto Project Website uses a "DOWNLOADS" page from which you can locate and download tarballs of any Yocto Project release. Rather than Git repositories, these files represent snapshot tarballs similar to the tarballs located in the Index of Releases described in the "Accessing Index of Releases" section.

> **Tip**  
> The recommended method for accessing Yocto Project components is to use Git to clone a repository and work from within that local repository. The procedure in this section exists should you desire a tarball snapshot of any given component.

1. ***访问Yocto Project页面***: Open The Yocto Project Website in your browser.

2. ***找到下载区域***: Select the "DOWNLOADS" item from the pull-down "SOFTWARE" tab menu near the top of the page.

3. ***选择Yocto Project发布***: Use the menu next to "RELEASE" to display and choose a recent or past supported Yocto Project release (e.g. warrior, thud, and so forth).

> **Tip**  
> For a "map" of Yocto Project releases to version numbers, see the Releases wiki page.
You can use the "RELEASE ARCHIVE" link to reveal a menu of all Yocto Project releases.

4. ***下载工具或者BSP***: From the "DOWNLOADS" page, you can download tools or BSPs as well. Just scroll down the page and look for what you need.

### 2.3.4 访问Nightly Builds
Yocto Project maintains an area for nightly builds that contains tarball releases at https://autobuilder.yocto.io//pub/nightly/. These builds include Yocto Project releases ("poky"), toolchains, and builds for supported machines.

Should you ever want to access a nightly build of a particular Yocto Project component, use the following procedure:

1. ***定位Nightly Build索引***: Open a browser and go to https://autobuilder.yocto.io//pub/nightly/ to access the Nightly Builds.

2. ***选择日期***: Click on the date in which you are interested. If you want the latest builds, use "CURRENT".

3. ***选择Build***: Choose the area in which you are interested. For example, if you are looking for the most recent toolchains, select the "toolchain" link.

4. ***找到Tar包***: Drill down to find the associated tarball.

5. ***下载Tar包***: Click the tarball to download and save a snapshot of the given component.

## 2.4 克隆并切换到分支
To use the Yocto Project for development, you need a release locally installed on your development system. This locally installed set of files is referred to as the Source Directory in the Yocto Project documentation.

The preferred method of creating your Source Directory is by using Git to clone a local copy of the upstream poky repository. Working from a cloned copy of the upstream repository allows you to contribute back into the Yocto Project or to simply work with the latest software on a development branch. Because Git maintains and creates an upstream repository with a complete history of changes and you are working with a local clone of that repository, you have access to all the Yocto Project development branches and tag names used in the upstream repository.

### 2.4.1 克隆Poky仓库
以下步骤为远程Poky仓库创建本地版本：

1. ***设定目录***: 切换到你希望创建本地Poky仓库的工作目录

2. ***克隆仓库***: 
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
Unless you specify a specific development branch or tag name, Git clones the "master" branch, which results in a snapshot of the latest development changes for "master". For information on how to check out a specific development branch or on how to check out a local branch based on a tag name, see the "Checking Out By Branch in Poky" and Checking Out By Tag in Poky" sections, respectively.

Once the local repository is created, you can change to that directory and check its status. Here, the single "master" branch exists on your system and by default, it is checked out:
```
     $ cd ~/poky
     $ git status
     On branch master
     Your branch is up-to-date with 'origin/master'.
     nothing to commit, working directory clean
     $ git branch
     * master
```                    
Your local repository of poky is identical to the upstream poky repository at the time from which it was cloned. As you work with the local branch, you can periodically use the git pull ‐‐rebase command to be sure you are up-to-date with the upstream branch.

### 2.4.2 切换Poky分支
当克隆远程Poky分支时，你可以访问所有开发分支。每一个开发分支都是从master分支fork而来，各不相同。为了阅读并利用这些文件，你需要指导分支名并且用check out命令获取。

> **注释**  
> 根据分支名获取到开发分支可以得到这个分支的快照，当你获取后其开发分支仍有可能有新的改动。

1. ***切换到Poky目录***: 如果你有本地Poky仓库，切换到Poky目录。如果没有，阅读“克隆Poky仓库”章节

2. ***查询分支名***:

     $ git branch -a
     * master
       remotes/origin/1.1_M1
       remotes/origin/1.1_M2
       remotes/origin/1.1_M3
       remotes/origin/1.1_M4
       remotes/origin/1.2_M1
       remotes/origin/1.2_M2
       remotes/origin/1.2_M3
           .
           .
           .
       remotes/origin/pyro
       remotes/origin/pyro-next
       remotes/origin/rocko
       remotes/origin/rocko-next
       remotes/origin/sumo
       remotes/origin/sumo-next
       remotes/origin/thud
       remotes/origin/thud-next
       remotes/origin/warrior
                    
3. ***切换分支***: 切换到你想工作的开发分支
```
     $ git checkout -b warrior origin/warrior
     Branch warrior set up to track remote branch warrior from origin.
     Switched to a new branch 'warrior'
```                    
以上命令可以讲远程warrior分支去到本地

以下指令可以显示你本地Poky仓库的分支。 *号表明正在工作的分支。
```
     $ git branch
       master
     * warrior
```

### 2.4.3 根据Tag切换Tag分支
和分支类似，远程仓库会使用tag来标记一些特定的提交以表明其重要意义（发布或者即将发布）。你也许希望根据这些Tag创建本地分支。操作方法和切换分支类似。

> **注释**  
> 切换到Tag分支使你拥有一个不会被开发分支上的提交而改动文件。

1. ***切换到Poky目录***: 如果有本地的Poky仓库，切换到Poky仓库，如果没有，请阅读“克隆Poky仓库”章节

2. ***获取Tag名***: 
```
     $ git fetch --tags
     $
```

3. ***列出Tag名***: 
```
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
     yocto-2.5.3
     yocto-2.6
     yocto-2.6.1
     yocto-2.6.2
     yocto-2.7
     yocto_1.5_M5.rc8
```

4. ***切换分支***:
```
     $ git checkout tags/yocto-2.7 -b my_yocto_2.7
     Switched to a new branch 'my_yocto_2.7'
     $ git branch
       master
     * my_yocto_2.7
```
                    
The previous command creates and checks out a local branch named "my_yocto_2.7", which is based on the commit in the upstream poky repository that has the same tag. In this example, the files you have available locally as a result of the checkout command are a snapshot of the "warrior" development branch at the point where Yocto Project 2.7 was released.
