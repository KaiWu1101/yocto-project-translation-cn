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
    - [2.3.4 访问Nightly Builds（每日构建）](#234-访问nightly-builds每日构建)
  - [2.4 克隆并切换到分支](#24-克隆并切换到分支)
    - [2.4.1 克隆Poky仓库](#241-克隆poky仓库)
    - [2.4.2 切换Poky分支](#242-切换poky分支)
    - [2.4.3 根据Tag切换Tag分支](#243-根据tag切换tag分支)
- [Chapter 3. 常见任务](#chapter-3-常见任务)
  - [3.1 理解并创建Layer](#31-理解并创建layer)
    - [3.1.1 创建你自己的Layer](#311-创建你自己的layer)

# Chapter 1 The Yocto Project 开发任务手册

## 1.1 欢迎
欢迎阅读Yocto Project开发任务手册！这篇文档提供在Yocto Project环境（例如开发在目标设备上运行的嵌入式Linux系统以及用户空间应用）中进行开发所需的相关步骤。在本文档中，相关的操作步骤被分类到了同一章节，根据内容的不同，有些章节会给读者提供详细的操作步骤，有些章节则只提供概括性的介绍。

本文档将提供如下信息：

- 帮助你熟悉Yocto Project。例如：如何搭建一套构建主机并使用Yocto Project代码仓库
- 如何提交改动到Yocto Project。这些改动可以是改进，新功能，或是bug修复。
- 使用Yocto Projcet开发镜像及应用时的日常操作，例如：如何创建layer，自定义image，编写recipe...

本文档不提供如下信息：

- 重复性的逐步指导：例如[Yocto Project应用开发和eSDK手册](http://www.yoctoproject.org/docs/2.7/sdk-manual/sdk-manual.html)已包含如何安装用来开发目标设备应用的SDK的详细步骤。
- 参考或概念资料：这些有对应的文档介绍，例如系统变量的说明及解释会包含在[Yocto Project参考手册](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html)中。
- 不由Yocto Project定义的，公开的信息/细节： 例如如何使用Git可以在网上找到更官方更好的指导。

## 1.2 其他
本文档包含很多方面的内容，如果你想完全理解这些内容，推荐阅读更多补充资料。如果想了解Yocto Project的介绍性信息，可以访问[Yocto Project](https://www.yoctoproject.org/)网站。如果你希望能在不了解Yocto Project的情况下能快速构建镜像，请参考[Yocto Project快速构建](http://www.yoctoproject.org/docs/2.7/brief-yoctoprojectqs/brief-yoctoprojectqs.html)文档。

在[Yocto Project参考手册](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html)里，你可以找到链接和其他文档的综合列表。

# Chapter 2. 环境搭建

本章将介绍如何开始搭建和使用Yocto Project的相关步骤。你将学习到创建如何使用Yocto Project进行开发的团队环境，如何搭建构建主机，如何定位Yocto Project代码仓库，以及如何创建本地Git仓库。

## 2.1 新建团队开发环境
你可能无法在项目的开始就知道你将如何在团队开发环境中使用Yocto Project，或是如何调整Yocto Project使它适应一个大型开发团队。然而，Yocto Project优势之一就是，它非常的灵活。因此，你可以将它适用于不同的应用案例和场景。当然，如果你旨在创建一个适合大型团队使用的开发环境，Yocto的灵活性同样可能会让你遇到很多困难。
为了帮助你理解如何创建这样的环境，本节展示了一个工作流程。这个概括性的流程提供了一些过去成功的经验，实践，解决方案以及可用的技术。请记住，以下只是起点，你完全可以为特定的工作环境定制化这些步骤。

1. ***谁会进行开发呢***: 首先，你需要明白谁会从事Yocto Project工作，并确定他们在工作中的角色。完成这一步对于完成第二步(准备硬件)和第三步(理解硬件架构和环境)至关重要。

有如下角色:

+ ***应用开发者***: 应用开发者在软件栈上进行应用级的工作。

+ ***核心系统开发者***: 核心系统开发者进行操作系统image内容的开发。

+ ***构建工程师***: 构建工程师管理Autobuilder和发布。不是所有的环境都需要构建工程师。

+ ***测试工程师***: 测试工程师创建并管理自动测试，确保所有应用以及核心系统开发达到了期望的质量标准。

2. ***获取硬件***: 根据团队规模以及构成，完成硬件的准备。理想状况是，任何开发者，构建或测试工程师都能使用支持Yocto Project的Linux发行版。这些系统原则上应该是高性能的。(例如，双六核Xeons CPU，24GB RAM，足够的硬盘空间). 对于使用于测试或运行Autobuilder的机器，越是高性能越可以保证效率。

3. ***了解硬件拓扑***: 一旦明白硬件信息以及团队构成，你就可以明白开发环境的硬件拓扑，更直观地了解硬件及其角色。

4. ***使用Git作为源代码管理工具***: 建议将你的元数据（例如recipes，配置文件，类等）和任何你在开发的软件保存在兼容OpenEmbedded构建系统的控制管理工具中。Yocto Project团队强烈建议使用Git。Git是分布式系统，易于备份，也支持离线工作，然后再链接回远程仓库。

    > **注释**  
    > 更多BitBake相关内容请参考[BitBake用户手册](http://www.yoctoproject.org/docs/2.7/bitbake-user-manual/bitbake-user-manual.html)

    搭建一个像[http://git.yoctoproject.org](http://git.yoctoproject.org)那样的服务是很容易的，它基于gitolite并使用cgit生成网页，方便你查看仓库。gitolite使用SSH key验证用户，允许通过控制分支的方式让你能够一点一点的改动仓库。

    > **注释**  
    > 搭建这些服务不在本手册讨论范围内。然而，以下网站可以帮助到你：  
    > [*Git documentation*](http://git-scm.com/book/ch4-8.html): 如何在服务器端安装gitolite  
    > [*Gitolite*](http://gitolite.com/): gitolite相关  
    > [*Interfaces, frontends, and tools*](https://git.wiki.kernel.org/index.php/Interfaces,_frontends,_and_tools): 如何创建Git接口和前端

5. ***搭建应用开发机器***: 如前文所说，应用开发者在已有软件栈上开发应用。针对搭建应用开发机器有以下建议做法:

   + 使用包含软件栈并且已构建好的工具链，然后在软件栈上开发应用。这个方法适用于少量且相互之间相对独立的应用开发。

   + 保持更新你的交叉开发工具链。你可以通过下载新的工具链或者通过opkg包管理系统来更新你已有的工具链。具体方案依赖于本地策略。

   + 在本地不同路径安装多份工具链以开发不同版本的应用

6. ***搭建核心开发机器***: 如前文所说，核心开发者开发操作系统内容。搭建开发image的机器有以下最佳实践:

   + 本地需要有[OpenEmbedded构建系统](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#build-system-term)，这样开发者可以在本地构建软件栈

   + 尽量不要改动核心系统，而是在核心系统上创建layer以完成你的工作。这样可以让你的系统在更新核心系统或者板级支持包（BSP）时拥有更高的可移植性。

   + 将项目中的layer和策略配置共享给其他开发者。

7. ***搭建Autobuilder***: Autobuilders往往是开发中的核心部分。它获取各个开发者的改动，并进行集中测试。基于自动化构建和测试结果，你可以决定是否发布你的产品。Autobuilder还允许对软件组件,回归识别和追踪进行“持续集成”式的测试。

    参考["Yocto Project Autobuilder"](http://autobuilder.yoctoproject.org/)以阅读更多关于Autobuilders和buildbot的相关信息。Yocto Project团队使用Yocto Project Autobuilders测试项目的整体健康状况。

    以下是这个系统的特点:

   + 高亮显示导致构建失败的提交

   + 将数据存入[sstate缓存](http://www.yoctoproject.org/docs/2.7/overview-manual/overview-manual.html#shared-state-cache)，开发者可以直接获取缓存，而无需本地构建

   + 允许提交触发新的构建任务

   + 允许触发在QEMU中的镜像自动启动和测试

   + 同时支持增量构建和重新构建

   + 共享输出，允许开发者测试（developer testing）和历史回归调查（historical regression investigation）

   + 构建的结果可以用来发布

   + 允许对构建过程和时间进行规划安排，以更有效地利用（硬件）资源

8. ***搭建测试机器***: 使用少数共享的、高性能的系统来进行测试。开发可以使用这些机器进行更多测试，同时仍可以使用自己的开发环境进行本地开发。

9. ***将策略和流程文档化***: Yocto Project使用层级结构和pull model。脚本可以和用来创建并发送Pull Request(例如 create-pull-request 和 send-pull-request). Pull model和其他开源项目一样，各个维护者只需要对项目特定部分负责，最终的合并(合并到主分支)由某个维护者进行处理。

    > **注释**  
    > 你也可以使用push model（译者注：原文的collective不清楚是什么特性），gitolite软件同时支持push model和pull model。

    和任何开发环境一样，将使用的策略和主要项目的指南记录在文档中，以便所有人理解，是非常重要的。建议开发者将提交信息(commit message)结构化，格式化， 这对项目指导也有积极的意义。好的提交信息对于修改的追溯和理解是非常必要的。

    如果你发现必须改动核心层的代码时，尽快与社区分享是很值得的。当你发现有需要改动的地方时，可能社区的其他人正需要这个改动。

10. ***开发环境总结***: 除开上述步骤以外，Yocto Project开发环境还有以下最佳实践：

    + 使用Git作为代码控制系统

    + 在Layer中维护你的元数据。阅读Yocto Project概述及概念手册中的[Yocto Project Layer模型](http://www.yoctoproject.org/docs/2.7/overview-manual/overview-manual.html#the-yocto-project-layer-model)章节，以及[理解并创建Layer](https://www.yoctoproject.org/docs/2.7/dev-manual/dev-manual.html#understanding-and-creating-layers)章节以了解更多关于layer的信息。

    + 使用不同的Git仓库分离项目目元数据和代码，yocto的源代码就是按照功能分成了一个个单独的仓库。阅读Yocto项目总览和术语手册中的[Yocto Project代码仓库](http://www.yoctoproject.org/docs/2.7/overview-manual/overview-manual.html#yocto-project-repositories)章节以了解更多关于这些仓库的信息。阅读[定位Yocto Project代码文件](https://www.yoctoproject.org/docs/2.7/dev-manual/dev-manual.html#locating-yocto-project-source-files)章节以了解如何在本地创建相关的Yocto Project Git仓库。

    + 在需要的地方创建[shared state cache缓存目录](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#var-SSTATE_DIR)。例如，在一个组织的开发系统上创建sstate cache缓存目录，然后让所有的开发者共享这个目录。

    + 搭建Autobuilder，并使用它来将代码获取到本地目录，构建时向sstate cache缓存目录填充共享数据。

    + Yocto Project社区鼓励你发送补丁以修复bug或者增加功能。如果你想提交补丁，请遵循提交指南[向Yocto Project提交改动](https://www.yoctoproject.org/docs/2.7/dev-manual/dev-manual.html#how-to-submit-a-change)以编辑良好的提交信息。

    + 对于OE-core部分的修改，请尽可能早的将其提交给上游，因为其他人很可能会遇到相同的问题。提交代码改动的方式请参考[向Yocto Project提交改动](https://www.yoctoproject.org/docs/2.7/dev-manual/dev-manual.html#how-to-submit-a-change)。现在可用的邮件地址及其说明，可参考Yocto Project参考文档中的[邮件列表](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#resources-mailinglist)章节。

## 2.2 准备构建主机
本节介绍如何为[构建主机](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#hardware-build-system-term)搭建系统，以使用Yocto Project进行开发。推荐使用原生Linux机器，或者也可以在其他类型的主机（Linux, Mac, Windows）上使用[CROPS](https://git.yoctoproject.org/cgit/cgit.cgi/crops/about/)(利用了[Docker容器](https://www.docker.com/)技术)进行开发。

> **注释**  
> 不支持在[Windows Subsystem for Linux(WSL)](https://en.wikipedia.org/wiki/Windows_Subsystem_for_Linux)中使用Yocto Project。Yocto Project不兼容WSL。

一旦你的构建主机完成设置能使用yocto项目了，根据具体需求，你还需要完成一些步骤。以下参考信息将告诉你进行BSP开发和内核开发要做哪些准备：

+ ***BSP开发***: 请参考Yocto Project BSP开发者手册中的[使用构建主机开发BSP](http://www.yoctoproject.org/docs/2.7/bsp-guide/bsp-guide.html#preparing-your-build-host-to-work-with-bsp-layers) 章节。

+ ***内核开发***: 请参考Yocto Project Linux内核开发手册中的[使用构建主机开发Linux内核](http://www.yoctoproject.org/docs/2.7/kernel-dev/kernel-dev.html#preparing-the-build-host-to-work-on-the-kernel)章节。

### 2.2.1 准备原生Linux主机
请按照以下步骤准备你的Linux机器作为Yocto项目的构建主机：

1. ***使用一个被支持的Linux版本***: 你应当有一个基于Linux的主机系统。你可以选用Fedora, OpenSUSE, Debian, Ubuntu, 或者CentOS，这些都是Yocto反复测试并且官方支持的发行版。参考Yocto Project参考文档的[被支持的Linux发行版](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#detailed-supported-distros)以及[发行版支持](https://wiki.yoctoproject.org/wiki/Distribution_Support)，你可以了解到这些被Yocto验证的Linux发行版的受支持状况。

2. ***足够的空间***: 系统至少需要50GB的空间以构建镜像。

3. ***满足最低的版本需求***: 建主机需要含有以下版本的软件，OpenEmbedded构建系统才能在上面正常运行：

   + Git 1.8.3.1 或以上

   + tar 1.27 或以上

   + Python 3.4.0 或以上

    如果主机上这些软件中任何一个不满足以上的版本需求，你需要一些步骤以继续使用Yocto Project。请参考《Yocto Project参考手册》的["Required Git, tar, and Python Versions"](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#required-git-tar-and-python-versions)。

4. ***安装开发主机包***: 根据需求的不同，需要的开发主机包也不一样。总体来说，如果你想覆盖所有案例，包的总量会很大。  
所需的包列表，请参考《Yocto Project参考手册》["Required Packages for the Build Host"](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#required-packages-for-the-build-host)

完成以上步骤后，你可以在Linux机器上使用开发目录继续工作了。如果你打算使用BitBake, 请参考[克隆Poky仓库](https://www.yoctoproject.org/docs/2.7/dev-manual/dev-manual.html#cloning-the-poky-repository)。如果你打算使用eSDK，请参考《Yocto Project应用开发和eSDK手册》[使用eSDK](http://www.yoctoproject.org/docs/2.7/sdk-manual/sdk-manual.html#sdk-extensible)。如果你工作和内核有关，请参考[《Yocto Project Linux内核开发手册》](http://www.yoctoproject.org/docs/2.7/kernel-dev/kernel-dev.html)。如果你准备使用Toaster，请参考《Toaster用户手册》[搭建并使用Toaster](http://www.yoctoproject.org/docs/2.7/toaster-manual/toaster-manual.html#toaster-manual-setup-and-use)章节。 

### 2.2.2 使用CROss PlatformS (CROPS)
使用CROPS，你就可以创建与操作系统无关的Yocto Project开发环境。你可以在Windows, Mac 或者Linux机器上使用容器，进行Yocto Project开发。

完成以下步骤，以使你的Windows, Mac或者Linux机器作为Yocto Project构建主机：

1. ***决定构建主机需求***: 你需要在主机上安装Docker。你可能还需要安装其他软件以支持Docker容器。请参考[Docker安装页面](https://docs.docker.com/install/#supported-platforms)以了解需求。

2. ***选择哪些需要安装***: 如果构建主机满足系统要求，你需要安装Docker CE。如果主机不满足需求（Windows10之前的版本或者Windows10家庭版），你需要安装的则是Docker Toolbox。

3. ***浏览安装页面***: 浏览[Docker安装页面](https://docs.docker.com/install/#supported-platforms)，找到与你的操作系统对应的Docker软件，点击链接进行下载。

4. ***安装软件***: 一旦你了解了所有前提需求，你可以下载并安装对应软件。根据对应机器和所需的软件类型进行下载安装：

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

7. ***启动容器，使用Yocto Project***: 访问https://github.com/crops/docker-win-mac-docs/wiki 以了解如何在对应主机系统上(例如Linux, Mac, 或 Windows)配置你的Docker容器来使用Yocto项目。

完成这些步骤后，你就有了Poky, Extensible SDK, Toaster容器。你可以点击本文中相应的链接以了解如何使用这些容器。

当你启动容器后，你就好像是在原生Linux机器上开发一样。关于如何使用这些容器，请参考： 
[Poky容器](https://www.yoctoproject.org/docs/2.7/dev-manual/dev-manual.html#cloning-the-poky-repository)
[Extensible SDK容器](http://www.yoctoproject.org/docs/2.7/sdk-manual/sdk-manual.html#sdk-extensible)
[Toaster容器](http://www.yoctoproject.org/docs/2.7/toaster-manual/toaster-manual.html#toaster-manual-setup-and-use)

## 2.3 定位Yocto Project源文件
本节为你展示如果定位并访问Yocto Project源文件。你将创建并使用这些本地文件。

> **Notes**  
> + Git相关信息请阅读《Yocto Project概述及概念手册》["Git"](http://www.yoctoproject.org/docs/2.7/overview-manual/overview-manual.html#git)章节 
> + Yocto Project代码仓库信息请阅读《Yocto Project概述及概念手册》["Yocto Project Source Repositories"](http://www.yoctoproject.org/docs/2.7/overview-manual/overview-manual.html#yocto-project-repositories)

### 2.3.1 访问代码仓库
我们推荐使用[上游Yocto Project代码仓库](http://www.yoctoproject.org/docs/2.7.2/overview-manual/overview-manual.html#source-repositories)，来进行Yocto Project发布的获取和使用。你可以在[http://git.yoctoproject.org](http://git.yoctoproject.org)浏览Yocto Project代码仓库。需要特别说明的是，你可以在[http://git.yoctoproject.org/cgit/cgit.cgi/poky/](http://git.yoctoproject.org/cgit/cgit.cgi/poky/)找到poky仓库。

使用以下步骤定位最新的上游poky Git仓库拷贝：

1. ***访问仓库***: 打开浏览器输入[http://git.yoctoproject.org](http://git.yoctoproject.org)，访问Yocto Project代码仓库的图形化界面。

2. ***选择仓库***: 点击你感兴趣的仓库（例如poky）。

3. ***找到用以克隆仓库的链接***: 在页面底部有用来克隆的仓库地址（例如[http://git.yoctoproject.org/poky](http://git.yoctoproject.org/poky)）。

> **注释**  
> 请参考[克隆Poky仓库](https://www.yoctoproject.org/docs/2.7/dev-manual/dev-manual.html#cloning-the-poky-repository)了解更多关于克隆仓库的信息。

### 2.3.2 访问发布索引
Yocto Project维护发布索引列表，它包含对Yocto Project有贡献的相关文件。与Git仓库不同，这些文件是某个组件快照的tar包。

> **提示**  
> 推荐使用Git克隆上游仓库以获取Yocto Project组件，并在本地克隆好的仓库中进行你的工作。本节介绍这些步骤的目的，是为了在你的开发过程中要用到tar包的时候能有所参考。

参考以下步骤定位并下载一个特定的tar包：

1. ***访问发布索引***: 打开浏览器输入http://downloads.yoctoproject.org/releases 访问发布索引列表，包括bitbake，sato等。

    > **注释**  
    > Yocto目录包含发布的Poky tar包的完整列表，发布索引里的poky目录是早期发布的poky版本(译者注：上一次修改时间是2012年)，存在的目的仅仅是为了追溯完整性。

2. ***选择组件***: 点击你感兴趣的发布组件（例如yocto）。

3. ***找到Tar包***: 向下展开，找到相关tar包。例如，点击yocto-2.7,查看Yocto Project 2.7发布版本的相关文件（例如poky-warrior-21.0.0.tar.bz2，它是发布的Poky tar包）。

4. ***下载Tar包***: 点击tar包，下载组件的快照。

### 2.3.3 使用下载页面
[Yocto Projec网站](http://www.yoctoproject.org/)提供了下载页面，你可以定位并下载Yocto Project发布的tar包。与Git仓库不同的是，这些文件代表的是快照tar包，和[2.3.2 访问发布索引](#232-访问发布索引)介绍的类似。

> **提示**  
> 推荐访问Yocto Project组件的方式仍然是使用Git克隆上游仓库，并以本地克隆好的仓库进行工作。本节所介绍步骤，是为了你想用tar包的时候有所参考。

1. ***访问Yocto Project页面***: 使用浏览器访问[http://www.yoctoproject.org/](http://www.yoctoproject.org/)

2. ***找到下载区域***: 在页面顶部导航栏，在“SOFTWARE”下拉菜单中选择“DOWNLOAD”项。

3. ***选择Yocto Project发布***: 使用"RELEASE"旁边的目录菜单选择最近的或者早期版本的Yocto Project发布（例如warrior, thud等等）。

    > **提示**  
    > 浏览[Wiki页面](https://wiki.yoctoproject.org/wiki/Releases)找到Yocto Project发布和版本号的对照列表（译者注：例如2.7版本被称之为Warrior，3.0版本被称之为Zeus）。
    > 你可以使用"RELEASE ARCHIVE"链接查看到所有Yocto Project的发布。

4. ***下载工具或者BSP***: 从“DOWNLOADS”页面，你也可以下载到工具或者BSP，你只需要向下滚动页面，寻找你想要的项目。

### 2.3.4 访问Nightly Builds（每日构建）
Yocto Project在[https://autobuilder.yocto.io//pub/nightly/](https://autobuilder.yocto.io//pub/nightly/)（译者注：此地址暂时404） 维护了Nightly Builds，包含tar包形式的发布。这些构建包括Yocto Project发布（"poky"），工具链，和针对所支持机器的构建。

如果你希望访问一个特定的Yocto Project组件的每日构建，使用以下步骤：

1. ***定位Nightly Build索引***: 打开浏览器，访问[https://autobuilder.yocto.io//pub/nightly/](https://autobuilder.yocto.io//pub/nightly/) 。

2. ***选择日期***: 选择日期。如果你想要最新构建，点击“CURRENT”。

3. ***选择Build***: 选择类型。比如你在搜寻的是最近的工具链，那么就选择“toolchain”。

4. ***找到Tar包***: 向下浏览，找到相关tar包。

5. ***下载Tar包***: 点击tar包，下载组件的快照。

## 2.4 克隆并切换到分支
使用Yocto Project进行开发，你需要在本地用来开发的机器上，选择某个版本的Yocto Project发布并安装。在Yocto Project中，把你本地安装的文件目录称为源目录[Source Directory](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#source-directory)（即poky目录）。

创建Source Directory的推荐方式是，使用Git从上游poky仓库克隆一份本地拷贝，在上游仓库的克隆版本上进行开发，使得你能向Yocto Project贡献你的代码或者仅仅是确保你正在使用的是最新的yocto项目进行开发。因为Git可以访问到上游仓库的完整改动历史（开发分支或是标签）。

### 2.4.1 克隆Poky仓库
以下是从远程[Poky](http://www.yoctoproject.org/docs/2.7.2/ref-manual/ref-manual.html#poky)仓库创建本地版本的操作步骤：

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
除非你指定了开发分支或者标签名，Git默认克隆"master"分支，它是"master"分支最新开发改动的快照。关于如何切换一个开发分支或是如何使用标签名来切换本地分支，请阅读[2.4.2 切换Poky分支](#242-切换poky分支)和[2.4.3 根据Tag切换Tag分支](#243-根据tag切换tag分支)小节。

本地仓库一旦创建，你可以跳转到那个目录，检查Git状态。如下示例的含义是，只有"master"分支在本地，默认地，切换的也是master分支。
```
     $ cd ~/poky
     $ git status
     On branch master
     Your branch is up-to-date with 'origin/master'.
     nothing to commit, working directory clean
     $ git branch
     * master
```                    
你本地的poky仓库和克隆时的上游poky仓库文件信息是一样的，当你工作在本地分支时，你可以定期的使用`git pull --rebase`命令确保与上游分支同步更新。

### 2.4.2 切换Poky分支
当克隆远程Poky分支时，你可以访问到所有开发分支。每一个开发分支都是从master分支fork而来，各不相同。为了阅读或者使用某个分支的一些文件，你需要知道分支的名字然后用`check out`命令切换到这个特定的开发分支。

> **注释**  
> 根据分支名切换到一个活跃的开发分支可以得到这个分支此时的快照，但是当你切换到这个分支后它对应的上游分支仍有可能有新的改动。

1. ***切换到Poky目录***: 如果你有本地Poky仓库，切换到Poky目录。如果没有，阅读[2.4.1 克隆Poky仓库](#241-%e5%85%8b%e9%9a%86poky%e4%bb%93%e5%ba%93)章节

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
以上命令可以将远程warrior分支取到本地。

以下指令可以显示你本地Poky仓库的分支。 *号表明正在工作的分支。
```
     $ git branch
       master
     * warrior
```

### 2.4.3 根据Tag切换Tag分支
和分支类似，远程仓库会使用tag来标记一些特定的提交以表明其重要意义（发布或者即将发布）。根据需求，你可以用Tag名切换到这个特定的Tag分支，操作方法和[2.4.2 切换Poky分支](#242-%e5%88%87%e6%8d%a2poky%e5%88%86%e6%94%af)类似。

> **注释**  
> 基于Tag名切换的分支中的文件，不会被其他分支所影响。

1. ***切换到Poky目录***: 如果有本地的Poky仓库，切换到Poky仓库，如果没有，请阅读[2.4.1 克隆Poky仓库](#241-%e5%85%8b%e9%9a%86poky%e4%bb%93%e5%ba%93)章节

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
                    
第一条命令`git checkout tags/yocto-2.7 -b my_yocto_2.7`会创建并切换到一个名为"my_yocto_2.7"的本地分支。这个分支和上游仓库的yocto-2.7标签对应的提交(commit)一致。

# Chapter 3. 常见任务
本章将介绍在使用Yocto Project时，需要经常处理的任务，例如创建layer，新加软件包，扩展/定制化镜像，移植到新硬件等基础功能。

## 3.1 理解并创建Layer
OpenEmbedded（译者注：后文以OE代替）构建系统支持管理多个layer的[元数据（Metadata）](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#metadata)。Layer允许你将不同类型的自定义设置独立开来。请阅读《Yocto Project Overview and Concepts Manual》的["The Yocto Project Layer Model"](http://www.yoctoproject.org/docs/2.7/overview-manual/overview-manual.html#the-yocto-project-layer-model)章节以获取Layer Model的介绍信息。

### 3.1.1 创建你自己的Layer
使用OE构建系统来创建你自己的Layer是很容易的一件事，Yocto Project还提供了工具让你更加快速地创建Layer。为了让你更好地理解Layer这个概念，本节将一步步的展示如何创建Layer。请阅读《Yocto Project Board Support Package (BSP) Developer's Guide》的["Creating a New BSP Layer Using the bitbake-layers Script"](http://www.yoctoproject.org/docs/2.7/bsp-guide/bsp-guide.html#creating-a-new-bsp-layer-using-the-bitbake-layers-script)章节和本文档的"使用bitbake-layers脚本创建通用Layer"章节以了解更多Layer创建工具。

参照以下步骤，在不使用工具地情况下来创建Layer:

1. ***查看已有Layer***: 在创建一个新Layer之前，你需要确认下元数据并没有被包含于其他人创建的Layer中。你可以查看OE社区的[OpenEmbedded MEtadata Index](http://layers.openembedded.org/layerindex/layers/)中可以被用在Yocto Project里的Layer索引。你可以找到你想要的或是差不多的一个Layer。

2. ***创建新目录***: 为你的Layer创建目录。创建Layer时，确保目录和Yocto Project代码目录(例如克隆下来的poky仓库)无关。

     尽管没有强制要求，最好在目录名前加上"meta-"前缀，例如：
     ```
     meta-mylayer
     meta-GUI_xyz
     meta-mymachine
     ```                        
     除例外情况，Layer名方方式如下：
     ```
     meta-root_name
     ```                        
     遵守这样的命名规范，你就不会遇到因工具，模块或者变量默认你的Layer名是以"meta-"前缀开始而产生的困扰。相关配置文件示例在后续步骤中有所展示，不带"Meta-"前缀的Layer名会自动加上这个前缀赋值给配置中的几个变量。

3. ***创建Layer配置文件***: 在新建的Layer文件夹中，你需要创建`conf/layer.conf`文件。最简单的方式是拷贝一份已有配置到你的Layer配置目录中，然后根据需要改动它。

     Yocto Project代码仓库的`meta-yocto-bsp/conf/layer.conf`文件说明了其语法。你需要在你的配置文件中，将"yoctobsp"替换成一个唯一标识（例如Layer "meta-machinexyz"的名字"machinexyz"）：
     ```
     # We have a conf and classes directory, add to BBPATH
     BBPATH .= ":${LAYERDIR}"

     # We have recipes-* directories, add to BBFILES
     BBFILES += "${LAYERDIR}/recipes-*/*/*.bb \
                 ${LAYERDIR}/recipes-*/*/*.bbappend"

     BBFILE_COLLECTIONS += "yoctobsp"
     BBFILE_PATTERN_yoctobsp = "^${LAYERDIR}/"
     BBFILE_PRIORITY_yoctobsp = "5"
     LAYERVERSION_yoctobsp = "4"
     LAYERSERIES_COMPAT_yoctobsp = "warrior"
     ```                        
     以下是Layer配置文件说明：

   + [BBPATH](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#var-BBPATH): 将此Layer根目录添加到BitaBake搜索路径中。利用BBPATH变量，BitBake可以定位类文件（.bbclass），配置文件，和被include的文件。BitBake使用匹配BBPATH名字的第一个文件，这与给二进制文件使用的PATH变量类似。同样也推荐你为你的Layer中类文件和配置文件起一个唯一的名字。

   + [BBFILES](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#var-BBFILES): 定义Layer中recipe的路径

   + [BBFILE_COLLECTIONS](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#var-BBFILE_COLLECTIONS): 创建唯一标识符以给OE构建系统参照。此示例中，标识符"yoctobsp"代表"meta-yocto-bsp"Layer。

   + [BBFILE_PATTERN](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#var-BBFILE_PATTERN): 解析时提供Layer目录

   + [BBFILE_PRIORITY](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#var-BBFILE_PRIORITY): OE构建系统在不同Layer找到相同名字recipe时所参考的使用优先级

   + [LAYERVERSION](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#var-LAYERVERSION): Layer的版本号。你可以通过LAYERDEPENDS变量指定使用特定版本号的Layer

   + [LAYERSERIES_COMPAT](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#var-LAYERSERIES_COMPAT): Lists the Yocto Project releases for which the current version is compatible. This variable is a good way to indicate if your particular layer is current.列出当前版本兼容的Yocto Project释放版本。它可以表示Layer是否有效。

4. ***添加内容***: 根据Layer类型添加内容。如果这个Layer支持某一设备，在`conf/machine/`目录下的文件中添加此设备的配置。如果这个Layer新增发行版本策略，就在`conf/distro/`下文件添加发行版本配置。如果这个Layer引入新的recipe，把recipe放入`recipes-*`前缀的子目录中。

     > **注释**  
     > 请参考《Yocto Project Board Support Package (BSP) Developer's Guide》以阅读更多关于遵从Yocto Project的Layer层级的解释

5. ***（可选）测试兼容性***: 如果你希望获得许可，以便在你的Layer或使用了你的Layer的应用中使用Yocto Project Compatibility Logo，请阅读[3.1.3 确保你的Layer兼容Yocto Project](#313-确保你的layer兼容yocto-project)章节以获得更多信息。