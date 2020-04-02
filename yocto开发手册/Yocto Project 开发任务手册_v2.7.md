- [Chapter 1 The Yocto Project 开发任务手册](#chapter-1-the-yocto-project-开发任务手册)
  - [1.1 欢迎](#11-欢迎)
  - [1.2 其他](#12-其他)
- [Chapter 2. 环境搭建](#chapter-2-环境搭建)
  - [2.1 新建团队开发环境](#21-新建团队开发环境)
  - [2.2 准备构建主机](#22-准备构建主机)
    - [2.2.1 准备原生Linux主机](#221-准备原生linux主机)
    - [2.2.2 使用CROss PlatformS (CROPS)](#222-使用cross-platforms-crops)

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

