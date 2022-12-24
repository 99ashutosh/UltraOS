#### System race calls

|      | 系统调用号                | 描述                     | 完成情况 |
| ---- | ------------------------- | ------------------------ | -------- |
| 1    | #define SYS_getcwd 17     |                          | fin      |
| 2    | #define SYS_pipe2 59      |                          | fin      |
| 3    | #define SYS_dup 23        |                          | fin      |
| 4    | #define SYS_dup3 24       |                          | fin      |
| 5    | #define SYS_chdir 49      |                          | fin      |
| 6    | #define SYS_openat 56     |                          | fin      |
|      | 测试点：open              | 使用当前目录下的相对路径 | fin      |
| 7    | #define SYS_close 57      |                          | fin      |
| 8    | #define SYS_getdents64 61 |                          | fin      |
| 9    | #define SYS_read 63       |                          | fin      |
| 10   | #define SYS_write 64      |                          | fin      |
| 13   | #define SYS_mkdirat 34    |                          | fin      |
| 14   | #define SYS_umount2 39    |                          | fin      |
| 15   | #define SYS_mount 40      |                          | fin      |
| 16   | #define SYS_fstat 80      |                          | fin      |

#### The design of the current working path

In rCore, the string of the current path is stored in the process control block and operated in the form of a stack

Such a method can adapt to different file systems, and is also beneficial to return the current working path. If the process control block stores the virtual file pointer of the current path, then the user must backtrack to obtain the path. However, .. does not point to the directory entry of the upper-level directory, but directly points to the cluster of the directory, so the upper-level file name cannot be obtained , which greatly reduces the efficiency.

Therefore, we also decided to adopt this approach, storing strings in the TCB.

#### Design of fd table

Previously used Rust's dyn to store references that implement the File feature in fd_table

open_at obtains the path based on fd, but the File feature does not have such an interface. Therefore, it is necessary to change the storage object of the TCB.

A file enumeration class is designed for this purpose, and its structure is as follows:

```rust
pub enum FileClass {
    File (Arc<OSInode>), // 指向真实的文件
    Abstr (Arc<dyn File + Send + Sync>),  // 指向抽象文件，比如stdio
}
```

Rust also adopts a similar design, but their stdio is also a file, and the file object is a serial device (probably

We are not consistent with the File interface of ordinary files and abstract implementations, so they are treated separately

In order to support device files, we have extended the File trait and added the ioctl method

#### Implementation of some system calls

Most system calls of the file system are easy to implement, and the difficulty lies in obtaining file information (fstat, getdent) and mounting.

##### UserBuffer

Many filesystem syscalls pass user buffers. The difficulty is that the buffer may span pages, so you cannot just force the buffer pointer and then operate it. For this we define the UserBuffer structure:

```Rust
pub struct UserBuffer {
    pub buffers: Vec<&'static mut [u8]>,
}
impl UserBuffer {
    pub fn new(buffers: Vec<&'static mut [u8]>) -> Self;
    pub fn len(&self) -> usize;
    pub fn write(&mut self, buff: &[u8])->usize;
    pub fn read(&self, buff:&mut [u8])->usize;
}
```

It is a vector referenced by the u8 array, so that buffers that exist in different pages can be stored separately during kernel operations.

This structure is used by system calls such as read/write.

##### getdent/fstat

​ These two system calls are similar and have common difficulties. For these two system calls, the user will pass in a buffer pointer, and the kernel needs to fill in the required information in the form of a structure. The difficulty is that the buffer may span pages, so it cannot be filled directly through page table translation, but UserBuffer must be used like read/write.

​ Another problem is that these two system calls use structures, which are shared with user programs implemented in C language. Fortunately, Rust provides the #[repr(C)] tag to support C-style structs.

##### mount

​Our operating system does not support device management for now. In fact, it only supports SD Card at present. All we do so far is maintain the mount table.
