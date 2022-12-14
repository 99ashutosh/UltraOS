# UltraOS

---

**Note**: Project needs to update. Currently `cargo` fails to build due to version issues (`features`, `edition2021`). This has a dirty fix by adding the `cargo-features = ["edition2021"]` to the `zero-0.1.3` package. Will fix this soon.

---

UltraOS: A **RISC-V multicore operating system** that is written by Rust language in qemu and k210 platform.

Currently, due to compatibility issues, we have updated the version management of the project and **locked** the toolchain and important external libraries. 

It should be noted that **partial errors** will appear in the final display results. This is normal, because the technical level of the project personnel is insufficient, and more advanced functions are supported, but no regression test is performed to ensure all aspects of functions. correct. But the known issues are easy to fix and have no impact on the architecture and its implementation.


### Contact Us

Li Chenghao：[loancold@qq.com](mailto:loancold@qq.com)

Gong Haochen：[1527198893@qq.com](mailto:1527198893@qq.com)

Ren Xiangyu：[lewaysfca73@gmail.com](mailto:lewaysfca73@gmail.com)


### Quick Start

Make sure you have installed `qemu-system-riscv64` and `Rust` components. 

Clone the repo

```shell
git clone https://github.com/xiyurain/UltraOS
cd UltraOS
```

In the root directory, enter the following commands:

- Prep the environment. If it succeeds, you don't need to do it again.

```shell
make env
```
- Compile and Run in `qemu`

```shell
make run
```

If `make` succeeds the you should see shell:

```shell
root@UltraOS: / >>
```

---

**Note**: 

If `OpenSBI` is not installed on your computer, there may be a problem that the `opensbi-riscv64-virt-fw_jump.bin` file cannot be found. At this point you can modify `codes/os/Makefile` at line 25-27 to select other SBIs to use.

But the functionality won't work because older versions of RustSBI don't have floating point enabled and fail many tests, and newer versions have compatibility issues. 

However, this situation does not exist on the `k210`, so you can use it with confidence.

``` Makefile
	BOOTLOADER := default
#	BOOTLOADER := ../bootloader/$(SBI)-$(BOARD).bin # If you have no OpenSBI, try RustSBI.
#	BOOTLOADER := ../bootloader/$(SBI)-$(BOARD)-new.bin # If you want to use new RustSBI, try this.
```

---

If you want to modify the shell, see `codes/user/src/bin/user_shell.rs`.
If you want to modify the kernel, see `codes/os/src/main.rs`.

### Running the OS

Makefile provides two make command under the root directory.

1. This command will generate a complete binary kernel file, which can be burned to the k210 development board. This command will compile the kernel
file, and after splicing with SBI, a naked running file k210.bin will be generated and placed in the root directory.

```shell
make all
```

2. This command will run UltraOS directly on `qemu`. First it will compile the test code we have prepared, and then burn its program and the official test file given by the operating system competition we participated in to the file image we prepared, and then start compiling the kernel file and splicing it with SBI Afterwards, a naked running file is generated and run through `qemu`. Note that although we merged with RustSBI, we did not use it in the end, but replaced it with OpenSBI, because RustSBI did not enable floating-point operations under qemu, and continued to use RustSBI (UltraOS customized version) on the k210 platform.

```shell
make run
```

This command will generate a kernel file and run UltraOS directly on the k210. However, in order to be able to run more programs, **please insert an sd card into the k210, and place preliminary files and programs in it**, and UltraOS will use it as an external memory controlled by the file system. At the same time, it should be noted that in order to support the game, our UltraOS will run on its own

```shell
# Insert an sdcard, check if it shows up as /dev/sdb1, if not modify the script mkfs.sh and run it.
make run BOARD=k210
```


### Monitoring the OS

The same `Makefile` provides debugging options.

Run this two command seperately in two shell, then the GDB debugging can be start.

```shell
# in the first terminal
make gdb
# in the second terminal
make monitor
```

### Project Staff

From Harbin Institute of Technology (Shenzhen)

Li Chenghao (Captain): Mainly responsible for multi-core support, process management and memory consistency management, user program support, development and test environment construction.

Gong Haochen: Mainly responsible for FAT and EXT2-like virtual file systems and their kernel support, joint debugging, k210 development board and SBI support, multi-core support.

Ren Xiangyu: Mainly responsible for memory management and optimization, trap design.


### Features

- Rust language
- Multi-core operating system
- Support 59 system calls
- High performance optimization: memory weak consistency optimization, lazy and CoW, file system double file block cache and other optimization mechanisms
- Signal mechanism: process supports process signal soft interrupt.
- Support C language program and Rust language user program writing and running (provide regression testing basis)
- FAT32 virtual file system
- Hybrid debugging tool: Monitor (combined with static macro printing and dynamic gdb features)
- Detailed project documentation, development process support, and comprehension-friendly code constructs and comments.

### Files and Instructions

- doc: project documentation
- codes: development codes
  - bootloader: Use developer Luo Jia's K210 and qemu version of RustSBI binary files (the source code has been changed in this project, and the generated binary files are not official versions)
  - dependency: local library dependency
  - fat32-fuse: build fat32 file system
  - os: kernel code
  - riscv-syscalls-testing: official evaluation program
  - simple_fat32: fat32 file system, which belongs to the kernel
  - user: user program related
  
  
### Thanks

This project uses the [RustSBI](https://github.com/rustsbi/rustsbi) version 2021.03.26 of Luo Jia and other developers, and the [rCoreTutorial-v3](https://github.com /rcore-os/rCore-Tutorial-v3) 2021.03.26 version.

At the same time, I would like to thank Li Gengzhi from Harbin Institute of Technology (Shenzhen) for his help and inspiration, as well as Mr. Xia Wen and Mr. Jiang Zhongming for their progress tracking.

This project uses the GPL3.0 agreement, and developers are welcome to use this project for learning.
