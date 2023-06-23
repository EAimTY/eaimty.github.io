+++
title = "ZFS──瑞士军刀般的文件系统"
date = 2020-02-15

[extra.comments]
issue_id = 35

[[extra.comments.addition]]
user = { login = "Benny", html_url = "https://www.bennythink.com/", avatar_url = "https://secure.gravatar.com/avatar/38a94d1b56f913b2531ab3a36577d3e4?d=retro" }
created_at = "2020-02-16T03:50:55Z"
body = "> > 说的好，zfs的许可证确实是个问题🤔🤔🤔，所以我选择ntfs😂😂🧐\n> \n> 土豆你分明是个Apple Boy，怎么能叛变呢？🤣\n\n哦哦哦，对，我是Apple Girl，那么，APFS大法好！"

[[extra.comments.addition]]
user = { login = "EAimTY", html_url = "https://www.eaimty.com/", avatar_url = "https://secure.gravatar.com/avatar/f64037e002ad7b01fe9ee60351bfd1d2?d=retro" }
created_at = "2020-02-16T03:42:46Z"
body = "> 说的好，zfs的许可证确实是个问题🤔🤔🤔，所以我选择ntfs😂😂🧐\n\n土豆你分明是个Apple Boy，怎么能叛变呢？🤣"

[[extra.comments.addition]]
user = { login = "Benny", html_url = "https://www.bennythink.com/", avatar_url = "https://secure.gravatar.com/avatar/38a94d1b56f913b2531ab3a36577d3e4?d=retro" }
created_at = "2020-02-16T00:36:22Z"
body = "说的好，zfs的许可证确实是个问题🤔🤔🤔，所以我选择ntfs😂😂🧐"
+++

你是否曾遇到过如下情况：

- 应该给这个分区分配多少空间？另一个呢？
- 我正在覆写一个文件，突然间设备断电了！我的源文件和更改全都损坏了！
- 硬 RAID 卡太贵了，mdadm 用起来又太麻烦...
- 我不想用 RAID，但是想把两块硬盘上的空间分配至一个分区中，有没有除了用 LVM 外的其它办法？
- LUKS 用起来好麻烦，每加载一个分区都要输入一次密钥...
- 每次备份系统都要把整个系统打包，太浪费空间了！
- 我的硬盘太太太大了！
- 我的某个文件太太太大了！

那么为何不试试这个瑞士军刀般的文件系统──ZFS？

<!--more-->

***

玩过 NAS 的朋友们应该对 ZFS 不陌生，FreeNAS 之类的很多存储服务器系统都原生支持 ZFS 文件系统，配置简单而优点众多。

ZFS 有非常多优点，然而为什么在 PC 上使用 ZFS 却不多见呢？我很早之前就听说过 ZFS 的各种优点，于是在写这篇文章前一个月，我把自己的主力 PC 的整个文件系统（包括根分区）迁移至 ZFS。经过一个月的使用体验，我发现：

**ZFS 真香！**

**注：**
**①这是一篇介绍与安利 ZFS 的文章，不会深入分析其架构与实现。同时本文也不是使用教程，如果你想尝试在 Linux 下 ZFS，请移步 [ZFS on Linux Wiki](https://github.com/zfsonlinux/zfs/wiki) 与各大发行版的 Wiki；**
**②我的所有体验都是在 Linux(Debian sid) 下完成的，也就是说下面内容基于 [ZFS on Linux 项目](https://github.com/zfsonlinux)，Solaris 系、BSD 系对 ZFS 的支持应该会更好。另外，我使用的 Kernel 版本是5.4，太低的内核版本可能会影响使用体验。**

***

# ZFS 的前生今世

ZFS 的开发并非是一帆风顺的。

### 诞生

ZFS 与 Solaris 是密不可分的。
让我们回到 20 年前。当时，Sun 公司如日中天，1992 年发布的 Solaris、1995 年发布 Java 都是其辉煌的证明。1993 年，Sun 进入了财富 500 强。

在公司业绩蒸蒸日上的环境下，ZFS 诞生了。

2001 年，Solaris 已诞生了整整 10 年，Sun 希望改进 Unix 文件系统（[Unix File System](https://en.wikipedia.org/wiki/Unix_File_System)）以解决一些问题。Sun 内部的 Jeff Bonwick 就是在此时有了对 ZFS 架构的基本构思。他说服了 Sun 高管，组建了 ZFS 的开发团队。

2004 年 9 月 14 日，Sun 正式[宣布了 ZFS 文件系统](https://web.archive.org/web/20041204083530/http://wwws.sun.com/software/solaris/10/ds/zfs.jsp)。2005 年 6 月 14 日，Sun 公司将正在开发中的 Solaris 11 的源代码以 CDDL 许可开放，这一开放版本就是 OpenSolaris。2005 年 10 月 31 日，ZFS 并入了 Solaris 开发的主干源代码，并在 2005 年 11 月 16 日作为 OpenSolaris build 27 的一部分发布。

### 波折

好景不长，2009 年 4 月 20 日，甲骨文公司宣布以 74 亿美金收购 Sun 公司。此时的 Sun 已如夕阳。由于甲骨文公司对 OpenSolaris 计划没有积极支援的意图。OpenSolaris 委员会于 2010 年 7 月 12 日对甲骨文给出“最后通牒”，要求在 8 月 16 日派出一位代理人商讨计划的走向，否则将在 8 月 23 日的委员会会议中做出回应。由于甲骨文未加回应，委员会于该日达成共识，解散 OpenSolaris 委员会，社区将不再提供新的源码，计划的控制权由开发员社区交还给甲骨文。

至此，Sun 的 ZFS 已成为 Oracle 的专有软件，ZFS 成为 Oracle 的注册商标，而 OpenSolaris，包括其中的 ZFS，由 [illumos 项目](https://illumos.org/) 继续着开发。

### 壮大

但 ZFS 并没有至此没落。社区在 2013 年成立了 [OpenZFS](http://open-zfs.org/wiki/Main_Page) 以配合 ZFS 在 illumos 中的开源发展，由 Jeff Bonwick 当年组建的 ZFS 开发团队中的 [Matt Ahrens](http://open-zfs.org/wiki/User:Mahrens) 作为 OpenZFS 项目的负责人。在开源社区的努力下，ZFS 不断增加新特性、提升稳定性。

回到 2007 年，苹果将 ZFS 移植到了 Mac OS X 上，但该项目在 2009 年被关闭，之后 MacZFS 项目继续维护着代码；2008 年，ZFS 随 FreeBSD 7.0 正式进入了 FreeBSD；同年，ZFS 开始被原生移植至 Linux，后来成为了 OpenZFS 下属的 [ZFS on Linux 项目](https://github.com/zfsonlinux)。如今，几乎所有主流操作系统都支持了 ZFS，甚至有 [OpenZFS on Windows 项目](https://openzfsonwindows.org/) 为 Windows 提供 ZFS 移植！

我们今天所说的 ZFS 在绝大多数情况下指的都是 OpenZFS。

***

# 什么是 ZFS

ZFS 这个名字本身没有含义，只是"Zettabyte File System"的首字母缩写。但 ZFS 本身并不具备任何的缩写意涵，只是想阐述做为一个具备高扩展容量文件系统且还有支持许多延伸功能的一个产品。
它是一个 128 位的文件系统，也就是说它能存储 1800 亿亿（18.4×10^18）倍于当前 64 位文件系统的数据。ZFS 的设计如此超前以至于这个极限就当前现实可能永远无法遇到。

Jeff Bonwick 曾说过：

> 要填满一个 128 位的文件系统，将耗尽地球上所有存储设备。除非你拥有煮沸整个海洋的能量，不然你不可能将其填满。

并解释填满ZFS与煮沸海洋的关系：

> 尽管我们都希望摩尔定律永远延续，但是量子力学给定了任何物理设备上计算速率与信息量的理论极限。举例而言，一个质量为 1 公斤，体积为 1 升的物体，每秒至多在 10^31 位信息 上进行 10^51 次运算。一个完全的 128 位存储池将包含 2^128 个块 =2^137 字节 = 2^140 位；因此，保存这些数据位至少需要 (2^140 位) / (10^31 位 / 公斤) = 1360 亿公斤的物质。

但单纯的“大”并不能成为 ZFS 的代替其它文件系统的原因，因为如今有很多文件系统（如 XFS）也可做到这点，虽然不及 ZFS，但数据存储量也暂时不会成为其瓶颈。

ZFS 的优点远不止“大”，下面来讲 ZFS 的众多优秀特性。

***

# ZFS 的特性

### 虚拟设备（VDEV）、存储池（zpool）、数据集（dataset）、虚拟卷（zvol）

这些是 ZFS 的实现核心，是 ZFS 易用性的基础，ZFS 的绝大多数优点是在其上实现的。

我们在使用传统文件系统时，“分区”与“分区表”是相互独立的，基于不同的技术实现。ZFS 则不同，可以认为它同时承担了分区与分区表的角色。

#### 虚拟设备（VDEV）

VDEV 类似于 Linux 中的 Device Mapper 层，可以认为是 ZFS 架构中的最底层，它用来定义使用设备的类型。
为何叫做虚拟设备？因为一个 VDEV 并不需要是一个特定的独立设备（例如一块硬盘），它可以是一块硬盘、一个 RAID 阵列、RAID-Z（ZFS 实现的 RAID 方式，下文会详细介绍）、甚至是一个文件。

ZFS 支持以下 VDEV 类型：

- 文件（File）
- 一块磁盘或一个 RAID0 阵列（Stripe）
- 标准 RAID1 阵列（Mirror）
- RAID-Z（包括 RAID-Z1、RAID-Z2、RAID-Z3）
- 热备盘（Spare）
- L2ARC（Cache，用 ARC 算法实现的二级缓存，保存于高速存储设备上，通常使用 SSD）
- ZIL（Log，记录两次 transaction group 之间发生的 fsync，保证突发断电时的一致性）

#### 存储池（zpool）

存储池建立于 VDEV 之上，可以认为与传统文件系统中的“分区表”对应。它拥有类似于 LVM 的功能，能管理一个或多个 VDEV。类似于内存，存储池大小会是所有设备的大小总和。这意味着在一个存储池中，可以同时包含一块硬盘、一个 RAID1 阵列和 RAID0 阵列等不同部分！
LVM 不能作为最终文件系统，建立的 LV 需要格式化为最终文件系统（如 ext4、fat32、NTFS、F2FS 等）后才能使用。不同于 LVM，建立好的 ZFS 存储池不需要任何操作即可直接使用。

#### 数据集（dataset）、虚拟块（zvol）

将数据集与虚拟块放在一起介绍是因为它们是“平级”的，都建立于存储池之上，可以认为与传统文件系统中的“分区”对应。

虽然存储池可以直接使用，但是有时我们需要实现一些特殊的需求，例如：你将 ZFS 作为根系统挂载，在快照系统时不想将 /tmp 下的文件和目录加入快照，怎么办？这时你可以定义一个“tmp”数据集，并设置其属性为 `com.sun:auto-snapshot=false` 
细心的朋友可能会发现，即使在没有创建“tmp”数据集时，/tmp 仍然是存在的，因为在 ZFS 中，可以认为数据集是为设置权限，限额，快照，挂载点等高级特性才存在的。
在没有定义数据集大小的情况下，ZFS 会自动为其分配存储池中的空间。ZFS 也允许子数据集的存在，在没有特别设定的情况下子数据集将继承父数据集的属性。

虚拟块是 ZFS 提供的块设备方式，类似于数据集，虚拟块为 block 设备，可以被格式化，可以被 iSCSI 分享。举个例子，你想在一块被分入存储池的空间上划分出一个 F2FS 文件系统格式的“分区”，你需要使用的就是虚拟块。

由此可见，在ZFS下，操作一个“分区”就像是操作一个目录一样简单，并且“分区”是动态大小的！

### 写时复制（copy-on-write，CoW）

在绝大多数文件系统下，当我们覆写一个文件时，已覆写的部分会完全丢失，在覆写的中途想要取消是不可能的。
ZFS 不同，当你执行覆盖文件操作时，新数据会先存储在不同的区域，在写入完成后再将文件系统元数据信息指向新写入的区域。
这能极大地提升系统的稳定性与安全性。突然断电时，正在操作的文件也不会损坏。所以如果使用了 ZFS，就可以完全抛弃 fsck 了。

当然，这么做有一个显著的缺点：文件会散落在磁盘的各个区域，也就是我们常说的“磁盘碎片”。
但是，随着 SSD 的普及，磁盘碎片已经变得无伤大雅，而传统的机械硬盘也更多地成为了“长期存储设备”，例如用于 NAS，其上数据不会非常频繁地进行覆写操作。

### 数据完整性验证、自动修复

写入的数据时，ZFS 会创建数据的校验和，校验和是数据的256位散列，校验和功能的范围可以从简单快速的 fletcher4（默认）到强加密散列（如 SHA256）。
读取数据时，ZFS 会检查读到数据的校验和，如果与写入时的检验和不符，那么就说明遇到了错误，ZFS 会尝试自动修正错误。这能在很大程度上保证资料完整性。

### RAID-Z

上文在介绍 VDEV 时提到了 RAID-Z，这是 ZFS 实现的 RAID 方式，不需要任何其它硬件或软件。
现时 RAID-Z 有 3 种类型：RAID-Z1、RAID-Z2、RAID-Z3。

- RAID-Z1，类似于 RAID5，一重奇偶校验，至少需要三块硬盘；
- RAID-Z2，类似于 RAID6，双重奇偶校验，至少需要四块硬盘；
- RAID-Z3，三重奇偶校验，ZFS 独有，至少需要五块硬盘。

### 快照（Snapshot）

这时 ZFS 在文件系统层级实现的备份功能。
当 ZFS 写入新数据时，可以保留包含旧数据的块，因而能够维护文件系统的快照版本。而因为组合快照的所有数据都会被储存，且整个存储池通常每小时会进行几次快照，所以快照的创建速度非常快。任何未变动的数据会在文件系统及其快照之间进行共享，因此也具备空间高效性。快照本质上是只读的，确保在创建后快照不会被修改。快照可以被整个恢复，也可以恢复快照中的某些文件或目录。

ZFS 也可以创建可写快照：“克隆（Clone）”。“克隆”让两个独立的文件系统共享一组块。对克隆文件系统的修改都会创建新的数据块以反映这些更改。但是无论存在多少个克隆，未变动的块仍然会被共享。这是写入时复制原则的实施方式。

### 去重（Deduplication）

ZFS 提供文件级别（file-level）、块级别（block-level）、字节级别（byte-level）的去重。
文件级别去重通过比对校验和来发现重复，是一般使用的级别，对性能影响最小。
其它两个级别的去重可以用于某些特殊场合，能节省更多的存储空间。

### 透明压缩（Compression）

ZFS 原生提供透明压缩功能，也就是在读写文件时实时进行压缩与解压缩。ZFS 会尝试压缩文件的头部，如果压缩率不佳，会自动放弃压缩将原数据写入。
压缩可以在一定程度上“提高 IO”，但代价是占用CPU性能，要在考虑 IO 性能与 CPU 性能后再选择是否开启。
ZFS 支持以下压缩算法：

- lz4，默认且推荐的压缩算法，对系统性能影响小，存取数据的速度几乎与不采用压缩时一样；
- gzip，这个不用解释，ZFS 默认的 gzip 级别是 6。对性能有一定的要求，但如果存储的数据是文档类型，可以考虑使用 gzip；
- lzjb，与 lz4 类似，但更推荐 lz4；
- zle，简单来说就是“去 0”。

一般来说，不推荐同时开启ZFS的压缩与块级别以上的去重。

### 透明加密（ZFS Native Encryption）

这是 ZFS 支持的最新特性，能够加密存储池或数据集。
默认下，ZFS 使用 aes-128-ccm 作为加密算法，目前为止是绝对安全的，如果不放心，还可以选择更高级别的加密。
传统的 LUKS 需要为每一个加密分区提供密钥，而 ZFS 可以对存储池加密。
当然，开启加密必定会影响性能，要在考虑性能后再选择是否开启。

***

综上，不难发现 ZFS 不只是一个单纯的文件系统，它还提供了一系列工具来提高使用体验。

但是，拥有如此多的优点，
# 为什么 ZFS 没有在更大程度上流行？

回到 2005 年，最初的 ZFS 随着 OpenSolaris 的开源而进入了开源世界，但同时也因许可证问题与 Linux “天生不合”。
OpenSolaris 开源时采用[通用开发与散布许可证（CDDL）](https://web.archive.org/web/20090805063020/http://www.sun.com/cddl/)，从而 ZFS 也成为了 CDDL 协议下的开源软件。
Oracle 收购 Sun，终止 OpenSolaris 开发，让我们口中的 ZFS 变成了 OpenZFS，OpenZFS 项目因无法联系到所有 ZFS 的初期开发者而无法变更许可证。CDDL 协议与 GPL 协议不兼容，所以很多 Linuxer 无法接触到 ZFS，从而导致了 ZFS 无法在更大程度上流行。

但最近 ZFS 也在慢慢融入 Linux 家庭中，上文中提到的 [ZFS on Linux 项目](https://github.com/zfsonlinux)对此作出了巨大的贡献。
2019 年，Canonical 在 Ubuntu 19.10 中默认加入了 ZFS 的支持。这是 ZFS 融入 Linux 的历史性的一步！虽然有着许可证问题，但是更多人能逐渐理解并包容这一历史遗留问题。Canonical 目前为止还没有因 ZFS 的引入而被缠入法律纠纷。有了第一个“吃西红柿的人”，相信之后会有更多的发行版拥抱 ZFS。

今天，ext 在某些方面的缺点已经成为瓶颈，BtrFS 因开发缓慢且不够稳定而暂时无法成为生产环境下的文件系统，ZFS 应是最佳文件系统的候选之一。

多年以后，当 BtrFS 成为主流时，也请不要忘记 ZFS，不要忘记这个优秀的开源社区，不要忘记这个伟大的文件系统探路者。

***

**本文参考了：**
- [ZFS - Wikipedia](https://en.wikipedia.org/wiki/ZFS)
- [History and implementations of ZFS - Wikipedia](https://en.wikipedia.org/wiki/History_and_implementations_of_ZFS)
- [ZFS 分层架构设计 - Farseerfc的小窝](https://farseerfc.me/zhs/zfs-layered-architecture-design.html)
- [ZFS 手册 - Knowledge Base](https://wiki2.xbits.net:4430/storage:zfs:zfs%E6%89%8B%E5%86%8C)
- [Kernel/Reference/ZFS - Ubuntu Wiki](https://wiki.ubuntu.com/Kernel/Reference/ZFS)
- [Oracle Solaris ZFS 管理指南](https://docs.oracle.com/cd/E24847_01/html/819-7065/docinfo.html)
- [ZFS存储池介绍：Stripe、Mirror、RAIDZ和高速缓存 - 致远任重](https://www.xiangzhiren.com/archives/283)
- [为FreeNAS节约存储空间，是压缩还是重复数据删除? - 致远任重](https://www.xiangzhiren.com/archives/317)
- [How strong is ZFS encryption? - Super User](https://superuser.com/questions/616239/how-strong-is-zfs-encryption)
- [初学者指南：ZFS 是什么，为什么要使用 ZFS？ - 知乎](https://zhuanlan.zhihu.com/p/45137745)

如果你在文章中发现任何纰漏，请在评论中告诉我，谢谢！
