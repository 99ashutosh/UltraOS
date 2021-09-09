# UltraOS

拥有友好代码和详细文档的Rust编写的基于RISC-V64的多核操作系统UltraOS，支持qemu和k210平台运行。

UltraOS: A **RISC-V multicore operating system** that is written by Rust language in qemu and k210 platform.
Currently I'm working on the English Version of the documents.


#### 运行
------------------------------------------------------------------------
由于Github的文件大小限制原因，项目中原来的文件系统镜像 
> ./codes/fat32-fuse/fat32.img

被暂时移除，因此如果要拉取一份完整可运行的代码，请移步到 https://gitee.com/LoanCold/ultraos_backup 拉取完整的代码，并且在那个gitee仓库里保存有我们开发的全过程commits和21个不同的分支，感谢支持！

Due to the file size restriction, the virtual file system image was removed, and the complete code repository is at https://gitee.com/LoanCold/ultraos_backup . 

------------------------------------------------------------------------

根目录下Makefile提供了两个命令。

Makefile provides two make command under the root directory.

> make all

该命令将生成完整的二进制内核文件，可烧录至k210开发板上。该命令会编译内核文件，与SBI拼接后生成裸运行文件k210.bin放置在根目录。

This would build the kernel binary file, which can run on the Kendryte K210 embedded SoC.

> make run

该命令将直接在qemu上运行UltraOS。该命令会首先编译我们准备好的测试代码，之后将其程序以及我们所参加的操作系统大赛给出的官方测试文件一同烧录到我们准备的文件镜像中，然后开始编译内核文件，与SBI拼接后生成裸运行文件，通过qemu运行。注意，我们虽然与RustSBI进行合并，但是最后我们并没有使用它，而是换成了OpenSBI，因为RustSBI在qemu下没有开启浮点运算，在k210平台则继续使用了RustSBI（UltraOS定制版本）。

This would run the OS kernel directly on Qemu.

> make run BOARD=k210

该命令将生成内核文件，并且直接在k210上运行UltraOS。但是，为了能够运行更多的程序，请在k210上插入sd卡，并在里面放置预备文件和程序，UltraOS会将其作为文件系统控制的外存进行使用。同时，需要注意的是，为了支持比赛的进行，我们的UltraOS会自行运行

This command will build the kernel binary file and run it directly on the Kendryte K210 embedded SoC.

> make gdb

> make monitor

这一组命令分别在两个窗口运行，即可启动gdb调试。

Run this two command seperately in two shell, then the GDB debugging can be start.

#### 联系我们 Contact Us

[loancold@qq.com](mailto:loancold@qq.com)
[lewaysfca73@gmail.com](mailto:lewaysfca73@gmail.com)

#### 项目人员

来自哈尔滨工业大学（深圳）

李程浩（队长）：主要负责多核支持，进程管理以及内存一致性管理，用户程序支持，开发和测试环境搭建。

宫浩辰：主要负责FAT和EXT2-like虚拟文件系统及其内核支持，联合调试，k210开发板以及SBI支持，多核支持。

任翔宇：主要负责内存管理以及优化，trap设计。

#### 项目特征

- Rust语言
- 多核操作系统
- 支持59条系统调用
- 高性能优化：内存弱一致性优化、lazy与CoW、文件系统双文件块缓存等优化等机制
- 信号机制：进程支持进程信号软中断。
- 支持C语言程序和Rust语言用户程序编写和运行（提供回归测试基础）
- FAT32虚拟文件系统
- 混合调试工具：Monitor（结合静态宏打印以及动态gdb特性）
- 详细项目文档、开发过程支持以及理解友好型代码构造和注释

#### 文件包阅读说明


- doc：项目文档
- codes：开发代码
  - bootloader：使用开发者洛佳的K210和qemu版本的RustSBI二进制文件（本项目对源码进行了改动，生成的二进制文件不为官方版本）
  - dependency：本地库依赖
  - fat32-fuse：构建fat32文件系统
  - os：kernel代码
  - riscv-syscalls-testing：官方评测程序
  - simple_fat32：fat32文件系统，隶属于kernel
  - user：用户程序相关

#### 感谢与声明

本项目使用了洛佳等开发者的[RustSBI](https://github.com/rustsbi/rustsbi) 2021.03.26版本，以及吴一凡等开发者的[rCoreTutorial-v3](https://github.com/rcore-os/rCore-Tutorial-v3) 2021.03.26版本。

同时感谢哈尔滨工业大学（深圳）的黎庚祉同学给予的帮助和灵感，以及夏文老师和江仲鸣老师在进度上的跟踪。

本项目使用GPL3.0协议，欢迎开发者使用该项目进行学习。
