### 2.3.4 参考发行版(Poky)    
Poky是一个yocto官方提供的参考发行版(Linux发行版)。它包含了[OE构建系统](http://www.yoctoproject.org/docs/2.7/ref-manual/ref-manual.html#build-system-term)(Bitbake和OE-Core)和一些列你可以用来构建属于你自己的发行版的的元数据。
你可以查看什么本手册的2.1章节的这张[图](https://www.yoctoproject.org/docs/2.7/overview-manual/overview-manual.html#what-is-the-yocto-project)来更好的理解Poky和Yocto项目中其他部分之间的关系。    
> 译者注：
在yocto仓库里它有单独的一个文件夹. Poky这个词让人有点困惑。 我们可以把它理解为yocto提供的一个工具箱，里面有一些很重要的工具(bitbake)和一些基本材料（OE-Core, bitbake ...），你能用这个工具箱和基本材料构建出一些基本的发行版来(一个你自定义的Linux发行版)
同时yocto有一个Linux发行版就是使用这些元数据和工具构建出来的，叫做Poky.

总的来说poky有以下特点：    
- 包含了一些重要的构建工具。    
    - 构建系统: bitbake和 OE-Core（poky下的meta文件夹）    
    - 一些必要的metadata，比如meta-poky, meta-yocto-bsp    
- 支持跨平台，交叉编译    
- 对嵌入式开发者提供各种特点和工具   

poky下还有很多交叉编译的工具链（toolchain），通常情况下，建议根据目标机的不同(target machine)使用poky提供的对应的交叉编译的工具链，因为如果你在两台不同的电脑上下载同一个版本的poky,并且使用其提供的toolchain, 那么你这两台电脑上的toolchain也是一致的。就是说相同版本的poky提供的toolchain不会因为开发机的不同而产生不同， 具有一致性。也省去了自己配置toolchain的麻烦。    

关于Poky你可以查看这个[章节](https://www.yoctoproject.org/docs/2.7/overview-manual/overview-manual.html#reference-embedded-distribution)。    
多说一句poky的主发行周期(major release) 和yocto是同步的（每六个月一次，一般是每年的春季和秋季）。   
