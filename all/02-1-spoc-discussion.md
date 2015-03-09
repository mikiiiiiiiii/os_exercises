#lec 3 SPOC Discussion

## 第三讲 启动、中断、异常和系统调用-思考题

## 3.1 BIOS
 1. 比较UEFI和BIOS的区别。
UEFI在所有平台上一致的提供操作系统的启动服务。对磁盘上的引导记录进行可信任性检查，只有满足签名才读取引导记录。提高了安全性。
 1. 描述PXE的大致启动流程。
客户端个人电脑开机后， 在 TCP/IP Bootrom 获得控制权之前先做自我测试。
Bootprom 送出 BOOTP/DHCP 要求以取得 IP。
如果服务器收到个人电脑所送出的要求， 就会送回 BOOTP/DHCP 回应，内容包括
客户端的 IP 地址， 预设网关， 及开机映像文件。否则，服务器会忽略这个要求。
Bootprom 由 TFTP 通讯协议从服务器下载开机映像文件。
个人电脑通过这个开机映像文件开机， 这个开机文件可以只是单纯的开机程式也可以是操作系统。
开机映像文件将包含 kernel loader 及压缩过的 kernel，此 kernel 将支持NTFS root
系统。
远程客户端根据下载的文件启动机器。


## 3.2 系统启动流程
 1. 了解NTLDR的启动流程。
从引导扇区加载并初始化NTLDR文件
在初始引导加载器阶段中，NTLDR将把计算机的微处理器从实模式转换为32位平面内存模式，NTLDR执行适当的小型文件系统驱动程序，这时NTLDR可以识别每一个用NTFS或FAT格式的文件系统分区
如果选择了NT系统，那么NTLDR将会运行NTDETECT文件，否则NTLDR将加载BOOTSECT.DOS，然后将控制权交给BOOTSECT.DOS。
NTDETECT文件将会收集计算机中硬件信息列表，然后将列表返回到NTLDR，这样NTLDR将把这些硬件信息加载到注册表“HKEY_LOCAL_MACHINE”中的Hardware中。
NTLDR将加载NTOSKRNL.EXE内核程序，然后NTLDR将加载硬件抽象层(HAL.dll)，接着系统将加载注册表中的“HKEY_LOCAL_MACHINE\System”键值，这时NTLDR将读取“HKEY_LOCAL_MACHINE\System\select”键值来决定哪一个ControlSet将被加载。
NTLDR加载注册表“HKEY_LOCAL_MACHINE\System\service”下的start键值为0的底层设备驱动。当ControlSet的镜像CurrentControlSet被加载时，NTLDR将把控制权传递给NTOSKRNL.EXE

 1. 了解GRUB的启动流程。
当系统加电后，固化在BIOS中的程序首先对系统硬件进行自检，自检通过后，就加载启动磁盘上的MBR，并将控制权交给MBR中的程序(stage1)，stage1执行，判断自己是否GRUB，如果是且配置了stage1_5，则加载stage1_5，否则就转去加载启动扇区，接着，stage2被加载并执行，由stage2借助stage1_5驱动文件系统，并查找grub.conf，显示启动菜单供用户选择，然后根据用户的选择或默认配置加载操作系统内核，并将控制权交给操作系统内核，由内核完成操作系统的启动。

 1. 比较NTLDR和GRUB的功能有差异。

 1. 了解u-boot的功能。

## 3.3 中断、异常和系统调用比较
 1. 举例说明Linux中有哪些中断，哪些异常？
 1. Linux的系统调用有哪些？大致的功能分类有哪些？  (w2l1)
中断分为同步中断与异步中断。异常又分为故障和陷阱。中断有256种。
Linux系统调用
进程控制，文件系统控制（文件读写操作，文件系统操作），系统控制，内存管理，网络管理，socket控制，用户管理，进程间通信（信号，消息，管道，信号量，共享内存）

```
  + 采分点：说明了Linux的大致数量（上百个），说明了Linux系统调用的主要分类（文件操作，进程管理，内存管理等）
  - 答案没有涉及上述两个要点；（0分）
  - 答案对上述两个要点中的某一个要点进行了正确阐述（1分）
  - 答案对上述两个要点进行了正确阐述（2分）
  - 答案除了对上述两个要点都进行了正确阐述外，还进行了扩展和更丰富的说明（3分）
```

 
 1. 以ucore lab8的answer为例，uCore的系统调用有哪些？大致的功能分类有哪些？(w2l1)
 
 ```
  + 采分点：说明了ucore的大致数量（二十几个），说明了ucore系统调用的主要分类（文件操作，进程管理，内存管理等）
  - 答案没有涉及上述两个要点；（0分）
  - 答案对上述两个要点中的某一个要点进行了正确阐述（1分）
  - 答案对上述两个要点进行了正确阐述（2分）
  - 答案除了对上述两个要点都进行了正确阐述外，还进行了扩展和更丰富的说明（3分）
 ```
有22个syscall， 
进程控制
    [SYS_exit]              sys_exit,
    [SYS_fork]              sys_fork,
    [SYS_wait]              sys_wait,
    [SYS_exec]              sys_exec,
    [SYS_yield]             sys_yield,
    [SYS_kill]              sys_kill,
内存管理
    [SYS_getpid]            sys_getpid,
    [SYS_putc]              sys_putc,
    [SYS_pgdir]             sys_pgdir,
    [SYS_gettime]           sys_gettime,
    [SYS_lab6_set_priority] sys_lab6_set_priority,
    [SYS_sleep]             sys_sleep,
文件操作
    [SYS_open]              sys_open,
    [SYS_close]             sys_close,
    [SYS_read]              sys_read,
    [SYS_write]             sys_write,
    [SYS_seek]              sys_seek,
    [SYS_fstat]             sys_fstat,
    [SYS_fsync]             sys_fsync,
    [SYS_getcwd]            sys_getcwd,
    [SYS_getdirentry]       sys_getdirentry,
    [SYS_dup]               sys_dup,

 
## 3.4 linux系统调用分析
 1. 通过分析[lab1_ex0](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab1/lab1-ex0.md)了解Linux应用的系统调用编写和含义。(w2l1)

objdump -S 尽可能反汇编出源代码，尤其当编译的时候指定了-g这种调试参数时，效果比较明显。隐含了-d参数。 
nm用来列出目标文件的符号清单。
file辨明文件类型
strace常用来跟踪进程执行时的系统调用和所接收的信号。 -f 跟踪由fork子进程系统调用
系统调用是应用调用系统的接口。

 ```
  + 采分点：说明了objdump，nm，file的大致用途，说明了系统调用的具体含义
  - 答案没有涉及上述两个要点；（0分）
  - 答案对上述两个要点中的某一个要点进行了正确阐述（1分）
  - 答案对上述两个要点进行了正确阐述（2分）
  - 答案除了对上述两个要点都进行了正确阐述外，还进行了扩展和更丰富的说明（3分）
 
 ```
 
 1. 通过调试[lab1_ex1](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab1/lab1-ex1.md)了解Linux应用的系统调用执行过程。(w2l1)
strace常用来跟踪进程执行时的系统调用和所接收的信号。-c统计每种系统调用所执行的时间，调用次数，出错次数。

运行可执行文件
确定文件可存取性
映射虚拟内存页
确定文件可存取性
打开文件
取文件状态信息
映射虚拟内存页
关闭文件描述字
确定文件可存取性
打开文件
读文件
取文件状态信息
映射虚拟内存页
设置内存映像保护页
映射虚拟内存页
映射虚拟内存页
关闭文件描述字
映射虚拟内存页
映射虚拟内存页
设置内存映像保护页
设置内存映像保护页
设置内存映像保护页
去除内存页映射
取文件状态信息
映射虚拟内存页
写文件
终止进程

 ```
  + 采分点：说明了strace的大致用途，说明了系统调用的具体执行过程（包括应用，CPU硬件，操作系统的执行过程）
  - 答案没有涉及上述两个要点；（0分）
  - 答案对上述两个要点中的某一个要点进行了正确阐述（1分）
  - 答案对上述两个要点进行了正确阐述（2分）
  - 答案除了对上述两个要点都进行了正确阐述外，还进行了扩展和更丰富的说明（3分）
 ```
 
## 3.5 ucore系统调用分析
 1. ucore的系统调用中参数传递代码分析。
 1. ucore的系统调用中返回结果的传递代码分析。
 1. 以ucore lab8的answer为例，分析ucore 应用的系统调用编写和含义。
 1. 以ucore lab8的answer为例，尝试修改并运行ucore OS kernel代码，使其具有类似Linux应用工具`strace`的功能，即能够显示出应用程序发出的系统调用，从而可以分析ucore应用的系统调用执行过程。
 
## 3.6 请分析函数调用和系统调用的区别
 1. 请从代码编写和执行过程来说明。
   1. 说明`int`、`iret`、`call`和`ret`的指令准确功能
 
