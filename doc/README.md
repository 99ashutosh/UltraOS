# UltraOS project documentation

This directory contains UltraOS design, development and debugging documents.


#### Contents

The UltraOS document will describe the architecture design of UltraOS from a macro perspective, leaving aside the specific implementation of the code as much as possible. The other major Markdown files will be triggered from the perspective of details to describe the specific implementation, including the development process, code design, data structure, some iterative process thinking, learning experience, etc.

Therefore, if you want to have an overview of the design of UltraOS, it is recommended to read the UltraOS document.pdf. If you just want to learn from it or find a specific implementation, you need to read the Markdown file and the corresponding specific code. The code also provides the necessary comments for reference.

#### Structure

Basically, we organize files in different modules, and we usually add detailed documents of each part under important folders to introduce the modules in detail.

- 测试.md：UltraOS为支持测试程序所作出的努力和思考。
- 调试.md + 问题.md：UltraOS所遇到的非常严重或者非常隐蔽的问题。
- 设备管理.md：UltraOS的I/O框架设计以及rCore-Tutorial-v3的学习笔记以及相关概念和资料的整合。
- 系统调用支持.md：部分值得记录的系统调用简单实现说明。
- ConsistencyModel.md：RISC-V的内存弱一致性相关的问题和UltraOS的相关处理。
- FAT32.md：UltraOS为构建FAT32文件系统所作出的努力和思考以及设计和优化方法。
- fs_kernel.md：UltraOS文件系统对内核提供的服务以及系统调用支持。
- Lazy_Alloc.md：UltraOS关于Lazy的性能表现测试。
- Mointor.md：UltraOS混合调试工具Monitor的说明。
- MultiCore.md：UltraOS为支持多核运行所作出的努力和思考，以及遇到的问题和解决方案。
- Optimization.md：UltraOS为提升性能性能所作出的努力和思考，以及遇到的问题和解决方案。
- Shell.md：UltraOS用户程序Shell支持。
- Signal.md：UltraOS信号机制。
- **UltraOS文档.pdf（重要）**：UltraOS的设计需求、理念、架构、特点、展望等全方位详细文档。
- UltraOS性能测试表.xlsx：UltraOS开发过程中的性能测试。
- UserSupportThinking.md：UltraOS对于用户态支持所作的努力和思考。

- Test.md: The efforts and thinking made by UltraOS to support the test program.
- debug.md + problem.md: Very serious or very hidden problems encountered by UltraOS.
- Device Management.md: UltraOS I/O framework design and rCore-Tutorial-v3 study notes and integration of related concepts and materials.
- System call support.md: some simple implementation descriptions of system calls worth recording.
- ConsistencyModel.md: Issues related to weak memory consistency of RISC-V and related processing of UltraOS.
- FAT32.md: UltraOS' efforts and thinking on building the FAT32 file system, as well as design and optimization methods.
- fs_kernel.md: The services and system calls provided by the UltraOS file system to the kernel.
- Lazy_Alloc.md: Lazy performance test of UltraOS.
- Mointor.md: Description of the UltraOS hybrid debugging tool Monitor.
- MultiCore.md: The efforts and thinking made by UltraOS to support multi-core operation, as well as the problems encountered and solutions.
- Optimization.md: The efforts and thinking made by UltraOS to improve performance, as well as the problems and solutions encountered.
- Shell.md: UltraOS user program Shell support.
- Signal.md: UltraOS signal mechanism.
- **UltraOS document.pdf (important)**: UltraOS design requirements, concepts, architecture, features, prospects and other comprehensive and detailed documents.
- UltraOS performance test table.xlsx: Performance test during UltraOS development.
- UserSupportThinking.md: UltraOS' efforts and thinking on user mode support.
