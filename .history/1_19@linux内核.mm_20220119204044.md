[//]: # (哈哈我是注释，不会在浏览器中显示。
  Date: 2022-01-19 20:39:23
  LastEditors: gyg
  LastEditTime: 2022-01-19 20:40:35
  FilePath: \test\1_19@linux内核.mm.md
)

# Linux内核

>内核就是操作系统的核心部分

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [Linux内核](#linux内核)
  - [redhat内核的功能](#redhat内核的功能)
  - [内核的组成](#内核的组成)

<!-- /code_chunk_output -->

## redhat内核的功能

1. 系统初始化：检测硬件资源并启动系统

2. 进程调度 ：决定进程什么时候运行，以及运行多久

3. 内存管理：给运行的进程分配内存

4. 安全：支持权限，支持selinux ，支持防火墙 支持iptables

5. 支持标准网络，文件系统

具体可以参考kernel-doc

## 内核的组成

多个内核版本可以共存的

内核的组成如下

`/boot/vmlinuz-4.18.0-80.el8.x86_64` 启动时用到的内核

`/boot/initramfs-4.18.0-80.el8.x86_64.img` 启动时提供必要模块的内核

`/lib/modules/版本/内核模块`

5.3 虚拟的文件系统/proc
进程信息 /proc/PID(数字)

cpu信息 /proc/cpuinfo

内存资料 /proc/meminfo

磁盘信息 /proc/partition

5.4 sysctl 配置内核参数
当前生效的内核配置参数都在 /pro/sys/

内核参数配置文件 /etc/sysctl.conf 系统启动时读取的参数

sysctl -a 显示所有的内核模块

一定要结合grep来使用

5.5 内核模块
内核模块： /lib/modules/内核版本

内核模块的相关命令：

lsmod 查看内核已经加载的模块

modinfo 模块名 查看这个模块的详细信息