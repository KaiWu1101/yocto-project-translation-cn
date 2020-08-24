以下列表列举了OE构建系统相关的组件:

- **Bitbake**: bitbake是yocto项目的核心组件，是由OpenEmbedded Core发布用来构建镜像的，很有意思的是bitbake的维护是和yocto分开的，相当于bitbake是单独维护的。    
bitbake是一个执行工具，用来执行一些用bash或者python编写的任务，同时能解决任务之间的依赖关系。总而言之，Bitbake是一个构建引擎，它通过解析特定格式的配方文件来执行配方中指定的任务    
想了解bitbake详细情况的话，参考[bitbake user manual](http://www.yoctoproject.org/docs/2.7/bitbake-user-manual/bitbake-user-manual.html). 这个manual有时间我也会进行阅读和翻译。    
- **OpenEmbedded-Core**: OE-Core本身是一个常用或者说常见的元数据层(参考上面layer的概念，元数据由配方，类和相关文件组成），可以理解为这是yocto项目的基础层layer，这个layer是由yocto和OpenEmbedded共同维护的。OE-Core这个层被很多OE衍生的项目所使用，Yocto就是其中之一。Core可以理解为这是整个构建系统的核心层。之前的话，OE-Core是被集成在poky中的
> 译者注:(这里我是没完全弄懂，不过我的理解是OE-Core的各种metadata(e.g. bbclass,recipes files......)原本是在poky中散布的，但是在yocto 1.0版本之后，yocto和OE决定共同维护,共享和开发这些核心的metadata, 于是就把这些核心metadata收集起来然后单独做了一个层layer,(进入poky文件夹后，有一个文件夹meta, 里面就是OE-Core)    

简而言OE-Core就是一些基础，必须,共用的metadata,通过这一层（layer）,你能构建出一些基本的工具或者包(package). OE-Core里面的metadata都是精心维护的，这样OE和yocto有了一个质量很高的核心,这个核心同时满足了OE和Yocto项目的目标要求
    - OE:拥有一个可以高度控制并且质量得到保证的核心
    - Yocto: 在核心内拥有少数几个功能完整的工具而不是拥有多个不同的工具    

共享元数据使得poky成为了基于OE-Core的一个集成层layer。你可以查看这个[图](https://www.yoctoproject.org/docs/2.7/overview-manual/overview-manual.html#yp-key-dev-elements)来理解他们之间的关系。Yocto项目包含了多个部分，比如说Bitbake, OE-Core, 脚本胶水(译者注:理解为连接脚本的一些配置文件)和关于Yocto构建系统的文件。


