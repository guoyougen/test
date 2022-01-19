[//]: # (哈哈我是注释，不会在浏览器中显示。
  Date: 2022-01-18 22:09:13
  LastEditors: gyg
  LastEditTime: 2022-01-19 20:12:12
  FilePath: \test\1_16@linux开机.mm.md
)

# linux开机

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [linux开机](#linux开机)
  - [Linux开机流程](#linux开机流程)
  - [强制破解Linux服务器开机密码实验](#强制破解linux服务器开机密码实验)

<!-- /code_chunk_output -->

## Linux开机流程

1. 首先会从硬盘中读取BootLoader，然后将整个系统控制权交给BootLoader，红帽系统的BootLoader 叫做GRUB
2. GRUB 会加载 /boot/grub2/grub.cfg 配置文件会选择启动内核
3. GRUB会加载内核，并且放到内存里面，然后启动硬件模块初始化脚本
4. BootLoader 会把控制权交给内核，内核开始找各种驱动，并且初始化这些硬件
5. 在RHEL系统中，会启动systemd执行所有的目标单元
6. 还会将root文件系统中的挂载 /etc/fstab 配置运行
7. 内核根据我们的文件系统，切换到 /sysroot 的根文件系统
8. systemd 会使用硬盘中安装的system来重新执行
9. systemd 最终会查找默认的启动目标 target，最后启动一个基于文化或者图形化的界面

![20170416112554766](https://s2.loli.net/2022/01/18/nBKirZRswPGxgL2.jpg)

## 强制破解Linux服务器开机密码实验

1. 重启系统

2. 将光标移动到要启动的内核

3. 按e来编辑

4. 在linux开头的行的末尾输入rd.break

5. ctrl+x继续启动，自动进入swith_root

```bash
重置密码：

1.重启以读写的方式挂载/sysroot

命令：mount -o remount,rw /sysroot

2.切换到真正操作系统的根/sysroot

命令：chroot /sysroot

3.重置密码：

命令：echo 123456 |passwd --stdin root

4.打标签：

命令：touch /.autorelabel

exit退出两次

此时root密码已被修改为123456
```
