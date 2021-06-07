# Linux 内核及内核编程

## Linux2.6后的内核特点

### 1. 新的调度器

目前主要有CFS(完全公平调度)调度算法和EDF(最早截止期限优先)调度算法。

### 2. 内核抢占

Linux2.6以后版本的linux内核支持内核任务的抢占，但linux内核本身只提供软实时的能力，因为linux内核中仍然存在，中断，软中断，自旋锁等不能被抢占的上下文。

### 3. 改进的线程模型

Linux2.6以后的线程模型采用NPTL(Native POSIX Thread Library, 本地POSIX线程库)模型。

### 4. 虚拟内存管理优化

Linux2.6以后Linux的虚拟内存管理引入了反向映射(r-map)技术，改善了虚拟内存的性能。

### 5. 文件系统

增加了对日志文件系统功能的支持。

### 6. 音频支持

高级Linux音频体系结构(Advanced Linux Sound Architecture, ALSA)取代了原来的OSS(Open Sound System)。

### 7. 总线，设备，驱动模型

总线，设备，驱动模型实现对设备的控制。

### 8. 电源管理

支持高级配置和电源接口(Advanced Configuration and Power Interface, ACPI), 用于调整CPU在不同的负载下工作于不同的时钟频率以降低功耗。

### 9. 联网和IPSec

Linux 2.6 内核中加入了对 IPSec 的支持，删除了原来内核内置的 HTTP 服务器 khttpd，加入了对新的 NFSv4（网络文件系统）客户机 / 服务器的支持，并改进了对 IPv6 的支持。

### 10. 用户界面层

Linux2.6内核重写了帧缓冲/控制台层，内核API中增加了不少新功能，例如内存池，sysfs文件系统，内核模块从.o改为.ko，驱动模块的编译方式等。

### 11. ARM架构的变更

  Linux3.0以后ARM Linux代码在时钟，DMA, pinmux等诸多方面做了优化和调整，Linux3.7以后的内核可以支持多平台，即用同一份内核镜像可以运行于多家SoC公司的芯片，实现一个Linux可适用于所有的ARM系统。

## Linux 内核组成

![image-20210519131723107](Linux 内核及内核编程.assets/image-20210519131723107.png)

### 1. 进程调度(Schedule)

![image-20210519132134612](Linux 内核及内核编程.assets/image-20210519132134612.png)

### 2. 内存管理（Memory Management）

![image-20210519132623344](Linux 内核及内核编程.assets/image-20210519132623344.png)

### 3. 虚拟文件系统

```mermaid
graph TD;
应用程序-->虚拟文件系统;
虚拟文件系统-->Minix;
虚拟文件系统-->Ext2;
虚拟文件系统-->FAT;
虚拟文件系统-->设备文件;
```

Linux 虚拟文件系统隐藏了各种硬件的具体细节，为所有设备提供了统一的接口。而且，它独立于各个具体的文件系统，是对各种 文 件 系 统 的 一 个 抽 象。 它 为 上 层的应用程序提供了统一的 vfs_read()、vfs_write() 等接口，并调用具体底层文件系统或者设备驱动中实现的 file_operations 结构体的成员函数。  

### 4. 网络接口

![image-20210519180649151](Linux 内核及内核编程.assets/image-20210519180649151.png)

网络接口提供了对各种网络标准的存取和各种网络硬件的支持。如图 3.8 所示，在 Linux中网络接口可分为网络协议和网络驱动程序，网络协议部分负责实现每一种可能的网络传输协议，网络设备驱动程序负责与硬件设备通信，每一种可能的硬件设备都有相应的设备驱动程序。  

### 5. 进程间通信

Linux支持进程间的多种通信机制包括：

1. 信号量
2. 信号
3. 共享内存
4. 消息队列
5. 管道
6. UNIX域套接字

## Linux内核的配置、编译和加载

### 1. 内核配置

在编译内核之前需要进行内核的配置，可以使用下面的命令之一：

```shell
make config 		#基于文本的最为传统的配置界面
make menuconfig		#基于文本菜单的配置界面
make xconfig		#基于QT的图形界面，要求QT被安装
make gconfig		#基于GTK+，要求GTK+被安装
```

其中最推荐的是make menuconfig命令，它不依赖于QT或GTK+，相较于纯文的界面也更加直观方便。

Linux内核配置系统主要由3个部分组成：

- Makefile: 分布在linux内核源码中，定义Linux内核的编译规则
- Kconfig: 配置文件，给用户提供配置选择的功能
- 配置工具：包括配置命令解释器和配置用户界面。

在Linux内核中增加程序需要完成以下三项工作：

- 将编写的源代码复制到Linux内核源代码的相应目录中。
- 在目录的Kconfig文件中增加关于新源代码对应项目的编译配置选项。
- 在目录的Makefile文件中增加对新源代码的编译条目。

一般而言，驱动开发者会在内核源代码的drivers目录下的相应子目录中增加新的设备驱动源代码或者在arch/arm/mach-xxx下新增加板级支持的代码，同时增加或修改Kconfig配置脚本和Makefile脚本。

#### Kconfig

1. 大多数内核配置选项都对应Kconfig中的一个配置选项config，config关键字定义新的配置选项，之后的几行代码定义了该配置选项的属性，这些属性包括类型，数据范围，输入提示，依赖关系，选择关系及帮助信息，默认值等。

   1. 每个配置选项都必须指定类型，类型可以为bool, tristate, string, hex和int。

   2. 输入提示的一般格式为：`prompt <prompt> [if <expr>]` 其中的if可选，用来表示该提示的依赖关系

   3. 默认值的格式为: `default <expr> [if <expr>]` 

   4. 依赖关系的格式为：`depends on <expr>` 或者 `requires <expr>` 。如果定义了多重依赖关系，它们之间用&&间隔。

   5. 选择关系的格式为：`select <symbol> [if <expr>]` 也称为反向依赖关系，A如果选择了B，则在A被选中的情况下，B自动被选中。

   6. 数据范围定义的格式为：`range <symbol> <symbol> [if <expr>]` 表示范围在两个symbol之间。

   7. 帮助信息的格式为:

      ``` Kconfig
      help(或---help---)
      	开始
      	...
      	结束
      ```

2. Kconfig中的菜单结构

   配置选项在菜单树结构中的位置可以由两种方法决定。第一种方式：

   ``` makefile
   menu "Network device support"
   	depends on NET
   config NETDEVICES
   	...
   endmenu
   ```

   通过menu和endmenu来显式的声明菜单，中间所有包含的选项都成为Network device support的子菜单。所有子菜单（config）选项都会继承父菜单(menu)的依赖关系。

   第二种方式：直接通过分析依赖关系生成菜单结构，如果菜单项在一定程度上依赖前面的选项，它就能成为该选项的子菜单。

   此外Kconfig中还支持"choices ... endchoice", "comment", "if ... endif"这样的语法结构。其中：

   ```makefile
   choice
   <choice options>
   <choice block>
   endchoice
   ```

   定义了一个选择群，其接受的选项可以是前面描述的任何属性。

### 2. 内核编译

Linux内核的编译由Linux源码下各级的makefile脚本文件控制。对于内核编译Makefile中的编译目标声明，一般采用如下形式：

```makefile
obj-y += foo.o
obj-$(CONFIG_ISDN) += isdn.o
```

第一种是直接将foo纳入内核编译，第二种则是根据内核配置文件的选项决定是否编译以及如何编译isdn模块。在内核makefile文件中，obj-y表示直接编译入内核，obj-m表示编译为内核模块，obj-n表示不编译。

除了obj-x形式的目标外，还有lib-y表示编译为library库，hostporgs-y表示编译为主机程序。

如果一个模块由多个文件组成，这时候就需要采用模块名加-y或-objs后缀的形式来定义模块的组成文件，如：

```makefile
# Makefile for the linux ext2-filesystem routines

obj-$(CONFIG_EXT2_FS) += ext2.o
ext2-y := balloc.o dir.o file.o fsync.o ialloc.o inode.o \
	ioctl.o namei.o super.o symlink.o
ext2-$(CONFIG_EXT2_FS_XATTR) += xattr.o xattr_user.o xattr_trusted.o
ext2-$(CONFIG_EXT2_FS_POSIX_ACL) += acl.o
```

上面的脚本表示模块的编译目标是ext2，该模块的编译依赖balloc.o， dir.o，dir.o等多个目标文件，最终连接生成ext2.o直至ext2.ko文件。并且是否包含xattr.o, acl.o等取决于内核的配置文件。

当编译需要用到子目录的文件时，需要将目录也加到编译选项的迭代中，如：

`obj-$(CONFiG_EXT2_FS) += ext2/` 当CONFIG_EXT2_FS为Y或者M时，内核编译系统会把ext2目录列入向下迭代的目标中。

### 3. 内核加载

