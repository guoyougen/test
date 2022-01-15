<div style='display: none'>
  Date: 2022-01-08 09:27:55
  LastEditors: gyg
  LastEditTime: 2022-01-08 18:50:27
  FilePath: \redhat_lesson\4_train.mm.md
</div>

# 2022_01_08_实训日记

## 虚拟化

### 分类

- 裸金属型
- 宿主型

### 内存虚拟化

- 大型操作系统（比如Linux）的内存管理的内容是很丰富的，而内存的虚拟化技术在OS内存管理的基础上又叠加了一层复杂性，比如我们常说的虚拟内存（virtual memory），如果使用虚拟内存的OS是运行在虚拟机中的，那么需要对虚拟内存再进行虚拟化，也就是vitualizing virtualized memory。

## Linux操作系统

### 主流操作系统

- 商用：红帽 RedHat RHEL8 Centos
- 民用：ubuntu kali(安全渗透) debian(开发) fedora

### windows和Linux区别

- windows民用居多，Linux商用居多
- windows闭源，Linux开源

#### windows是闭源的

- windows操作简单，有图形化界面
- windows系统空间占用大
- windows底层源码保密
- windows被留后门不安全

#### Linux系统是开源的

- Linux操作系统是属于开源软件基金会的
- 系统稳定
- [linux系统内核](git.kernel.org) `查看Linux源码`

### 操作系统的系统内核 掌握了整个系统的调度算法、指令集、资源控制、所有的系统功能

## REHCSA考试

- 上午: 3.5h 红帽系统管理员 基本操作，简单服务部署，shell编程，逻辑卷管理
- 下午：4.5h 红帽认证系统工程师 ansible 自动化运维 全脚本实现

## Linux 快照技术 虚拟化特有技术

- 快照一定要关机，开机快照会导致内存锁死

## Linux入门

### 常识

- 选择 not listed 输入root 超级管理员登录  
- root 拥有系统的一切
- 一般不会给用户使用
- 删库 rm -rf /*
- 虚拟机回物理机 ctrl+alt
- hostnamectl set-hostname 设置主机名  在企业中   都会有一个DNS服务器 会做域名和ip地址的绑定    IT管理员必须要给每一台设备一个固定的主机名和ip地址   使得我们可以通过主机名就可以访问服务器
hostname   查看服务器主机名


### linux 文件目录结构 起源是 /根 linux万物是文件

- / 根 类似于windows的盘符
- /bin 存放构建最小系统所需要的工具
- /root 存放root 用户的文件
- /dev 存放系统中所有的硬件文件
- /etc 存放系统配置文件
- /home 除root用户外的默认家目录
- /boot 存放系统引导
- /lib 库文件
- /mnt 挂载文件
- /sbin 用来存放内核所需要的工具
- /proc 内存映射，该目录不占用磁盘空间
- /tmp缓存目录，是所有用户生成文件都会放在该目录下，随时可以删除

### 登录企业服务器

1. 远程登录 ssh 远程连接 可以满足一切需求 命令操作
2. web登录 可视化操作 一般是监控，运维人员使用 简单配置

#### 如何远程登录linux服务器

1. 服务器要有网络，我们要知道这台服务器的ip地址
2. 网络可达
3. 服务器必须开启openssh 功能 允许被人远程访问，红帽系默认开启 ubuntu需手动开启
4. 必须知道该服务器的用户名的密码（不一定要root）
  如何查看服务器ip ip addr
  ip地址为 192.168.13.130/24 需远程工具连接

### 常用命令

- 查看历史命令：`history`
- 查看时间：`date`
- 查看时期：`cal`
- 当前命令：`pwd`
- 移动命令：`cd`
- 查看内容：`ls`，`ls -l`
- 创建目录：`mkdir`
- 创建文件：`touch`
- 复制文件：`cp`
- 删除命令：`rm`,`rm -f`,`rm -r`
- 移动文件（重命名）：`mv`
- 关机：`shutdown`,`shutdown 0`
- 尝试获取帮助：`--help`,`man + 内容`
- 重启：`reboot`
- 测试连接性：`ping`
- 终止进程：`ctrl+c`
- 切换用户：`su`
- 编辑：`vi` insert：`i` 命令行模式：`esc` 输入命令：`shift+:` 保存退出：`wq`

### 用户和组

- root超级管理员 
- 用户：任何一个用户都会分配一个id
- uid=0 标识了root用户
- 一般的用户uid从1000开始
- 0-1000是系统默认存在的用户，系统已经占用
- /etc/passwd   存储了所有用户的信息，是不可以随便编辑的

#### 组

- 用户必须属于一个组 创建用户的时候默认创建一个组，组名和用户名相同 每个组也都被分了一个组ID

- /etc/group  存放了组信息 可以将用户添加到某个组 同一个组的所有用户，是可以共享这个组的文件的
- redhat: x :1000:
字段1：组名称
字段2：密码占位符
字段3：组的id

##### wheel组

- 有个组叫wheel    如果某个普通用户   想执行某些只能root执行的命令，可以通过提权来完成这些命令 ，就要把这个用户 加入到 wheel这个组里面   
- wheel组  是系统管理员组


#### 什么是提权

- sudo  +  某些命令    就可以以root用户来执行这些命令
- 在第一次sudo的时候  需要提供用户密码确认

##### sudo  和   su  -  区别是什么?

- sudo的全称叫做： substitute user do    代替用户做   此时的用户还是非root
su  -  是直接切换到用户 root的命令

#### 用户和组的创建和修改:

``` redhat
useradd
-u：   uid
-g：   用户组的名称
-d：   家目录
-G：    附加组    （wheel）
-s：     指定shell文件

useradd admin  -u 1001   -g redhat  -s  /bin/bash -G wheel passwd  admin   
给密码  需要重复确认

groupadd   增加组 
-g    组id  
groupadd  -g 1002  admins

```
