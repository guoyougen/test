[//]: # (哈哈我是注释，不会在浏览器中显示。
  Date: 2022-01-17 19:00:32
  LastEditors: gyg
  LastEditTime: 2022-01-19 20:32:17
  FilePath: \test\1_15@linux存储方式.mm.md
)

# linux存储方式

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [linux存储方式](#linux存储方式)
  - [linux存储设备命名方式](#linux存储设备命名方式)
  - [存储方式 三类](#存储方式-三类)
    - [DAS 直连式存储](#das-直连式存储)
    - [NAS 网络附属存储](#nas-网络附属存储)
    - [SAN storage are network](#san-storage-are-network)
      - [SAN 有两种传输模式](#san-有两种传输模式)
        - [以太网](#以太网)
        - [FC网络（FC交换机 FC网卡 光纤 FC网络支持巨型数据包）](#fc网络fc交换机-fc网卡-光纤-fc网络支持巨型数据包)
        - [ISCSI 实验 (1_19)](#iscsi-实验-1_19)
          - [服务端配置](#服务端配置)
          - [客户端配置](#客户端配置)

<!-- /code_chunk_output -->

## linux存储设备命名方式

>我们在计算机中保存的文件大都是保存在电脑硬盘中，部分同学还会将自己的文件存储在网盘中，网盘中的文件只能说是用来做备份，并不能像本地硬盘上的数据那样随时使用，我们在学习存储管理的时候主要学习的就是如何对自己计算机上的硬盘进行管理，目前我们主流的硬盘主要有两种形态，一种是**机械硬盘(HDD)**，一种是**固态硬盘(SSD)**

|        设备        |    第一块    |    第二块    |    第三块    |
| :----------------: | :----------: | :----------: | :----------: |
| SATA 机械硬盘 /usb |   /dev/sda   |   /dev/sdb   |   /dev/sdc   |
|     虚拟机里面     |   /dev/vda   |   /dev/vdb   |   /dev/vdc   |
|    SSD 固态硬盘    | /dev/nvme0n1 | /dev/nvme0n2 | /dev/nvme0n3 |
|       逻辑卷       | /dev/mapper1 | /dev/mapper2 | /dev/mapper3 |

## 存储方式 三类

### DAS 直连式存储

>最传统 直接把磁盘连接在设备

### NAS 网络附属存储

>NAS是基于文件的存储，需要服务器对自己的存储进行分区、格式化，挂载就变成了一个目录。通过server把自己的文件夹共享出去
用户client通过网络来访问server，共享文件夹
用户把文件信息发给server，然后server替这个用户进行读写操作
**注意：用户不能直接对server进行磁盘操作**

nas更贴近用户，用户会多一个公共文件夹，通常这个技术用来做共享文件夹
nas是靠以太网传输的 TCP/IP协议
10G 通过nas网络传播 ，需要分片 TCP/IP协议，最大传输单元 1500byte
再加上 TCP 头部 ip头部 mac头部
**nas网络 来传播文件 效率非常低下**

### SAN storage are network

>san是基于数据块的存储

存储区域网络 客户端可以理解为读取了一大块虚拟磁盘

虚拟磁盘是属于存储服务器上的 然后通过LUN ID
每一个LUN 都有一个id 可以被用户挂载
**SCSI 服务就是用来实现 SAN存储网络的**

#### SAN 有两种传输模式

##### 以太网

##### FC网络（FC交换机 FC网卡 光纤 FC网络支持巨型数据包）

>传输速度可以达到 每秒 8G/16G

通常我们**把SAN 叫做 FCSAN**

##### ISCSI 实验 (1_19)

首先需要两台虚拟机，恢复初始化状态

一台命名为SAN-server 一台命名为SAN-client

安装yum 源，关闭防火墙，同时ISCSI需要额外安装一块硬盘

###### 服务端配置

```bash
#服务端
[root@wentan ~]# hostnamectl set-hostname SAN-server
[root@wentan ~]# bash
#接下来需要安装ISCSI的工具
[root@SAN-server ~]# yum install -y targetcli
[root@SAN-server ~]# systemctl enable target
Created symlink /etc/systemd/system/multi-user.target.wants/target.service → /usr/lib/systemd/system/target.service.
[root@SAN-server ~]# systemctl start target

#分区选择一块共享给SAN客户
[root@SAN-server ~]# fdisk /dev/nvme0n2

Welcome to fdisk (util-linux 2.32.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0x31fc34c5.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-41943039, default 2048): 
Last sector, +sectors or +size{K,M,G,T,P} (2048-41943039, default 41943039): +10G

Created a new partition 1 of type 'Linux' and of size 10 GiB.

Command (m for help): p
Disk /dev/nvme0n2: 20 GiB, 21474836480 bytes, 41943040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x31fc34c5

Device         Boot Start      End  Sectors Size Id Type
/dev/nvme0n2p1       2048 20973567 20971520  10G 83 Linux

Command (m for help): m

Help:

  DOS (MBR)
   a   toggle a bootable flag
   b   edit nested BSD disklabel
   c   toggle the dos compatibility flag

  Generic
   d   delete a partition
   F   list free unpartitioned space
   l   list known partition types
   n   add a new partition
   p   print the partition table
   t   change a partition type
   v   verify the partition table
   i   print information about a partition

  Misc
   m   print this menu
   u   change display/entry units
   x   extra functionality (experts only)

  Script
   I   load disk layout from sfdisk script file
   O   dump disk layout to sfdisk script file

  Save & Exit
   w   write table to disk and exit
   q   quit without saving changes

  Create a new label
   g   create a new empty GPT partition table
   G   create a new empty SGI (IRIX) partition table
   o   create a new empty DOS partition table
   s   create a new empty Sun partition table

Command (m for help): t
Selected partition 1
Hex code (type L to list all codes): l

 0  Empty           24  NEC DOS         81  Minix / old Lin bf  Solaris        
 1  FAT12           27  Hidden NTFS Win 82  Linux swap / So c1  DRDOS/sec (FAT-
 2  XENIX root      39  Plan 9          83  Linux           c4  DRDOS/sec (FAT-
 3  XENIX usr       3c  PartitionMagic  84  OS/2 hidden or  c6  DRDOS/sec (FAT-
 4  FAT16 <32M      40  Venix 80286     85  Linux extended  c7  Syrinx         
 5  Extended        41  PPC PReP Boot   86  NTFS volume set da  Non-FS data    
 6  FAT16           42  SFS             87  NTFS volume set db  CP/M / CTOS / .
 7  HPFS/NTFS/exFAT 4d  QNX4.x          88  Linux plaintext de  Dell Utility   
 8  AIX             4e  QNX4.x 2nd part 8e  Linux LVM       df  BootIt         
 9  AIX bootable    4f  QNX4.x 3rd part 93  Amoeba          e1  DOS access     
 a  OS/2 Boot Manag 50  OnTrack DM      94  Amoeba BBT      e3  DOS R/O        
 b  W95 FAT32       51  OnTrack DM6 Aux 9f  BSD/OS          e4  SpeedStor      
 c  W95 FAT32 (LBA) 52  CP/M            a0  IBM Thinkpad hi ea  Rufus alignment
 e  W95 FAT16 (LBA) 53  OnTrack DM6 Aux a5  FreeBSD         eb  BeOS fs        
 f  W95 Ext'd (LBA) 54  OnTrackDM6      a6  OpenBSD         ee  GPT            
10  OPUS            55  EZ-Drive        a7  NeXTSTEP        ef  EFI (FAT-12/16/
11  Hidden FAT12    56  Golden Bow      a8  Darwin UFS      f0  Linux/PA-RISC b
12  Compaq diagnost 5c  Priam Edisk     a9  NetBSD          f1  SpeedStor      
14  Hidden FAT16 <3 61  SpeedStor       ab  Darwin boot     f4  SpeedStor      
16  Hidden FAT16    63  GNU HURD or Sys af  HFS / HFS+      f2  DOS secondary  
17  Hidden HPFS/NTF 64  Novell Netware  b7  BSDI fs         fb  VMware VMFS    
18  AST SmartSleep  65  Novell Netware  b8  BSDI swap       fc  VMware VMKCORE 
1b  Hidden W95 FAT3 70  DiskSecure Mult bb  Boot Wizard hid fd  Linux raid auto
1c  Hidden W95 FAT3 75  PC/IX           bc  Acronis FAT32 L fe  LANstep        
1e  Hidden W95 FAT1 80  Old Minix       be  Solaris boot    ff  BBT            

Hex code (type L to list all codes): 8e
Changed type of partition 'Linux' to 'Linux LVM'.

Command (m for help): p
Disk /dev/nvme0n2: 20 GiB, 21474836480 bytes, 41943040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x31fc34c5

Device         Boot Start      End  Sectors Size Id Type
/dev/nvme0n2p1       2048 20973567 20971520  10G 8e Linux LVM

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

[root@SAN-server ~]# pvcreate /dev/nvme0n2p1
  Physical volume "/dev/nvme0n2p1" successfully created.
[root@SAN-server ~]# vgcreate iscsi_vg /dev/nvme0n2p1 
  Volume group "iscsi_vg" successfully created
[root@SAN-server ~]# lvcreate -n iscsi_store -l 100%VG iscsi_vg
  Logical volume "iscsi_store" created.

#接下来通过targetcli 工具来做SAN网络共享
[root@SAN-server ~]# targetcli
Warning: Could not load preferences file /root/.targetcli/prefs.bin.
targetcli shell version 2.1.53
Copyright 2011-2013 by Datera, Inc and others.
For help on commands, type 'help'.

/> backstores/block create name=iscsi_store dev=/dev/iscsi_vg/iscsi_store 
Created block storage object iscsi_store using /dev/iscsi_vg/iscsi_store.

/> iscsi/ create iqn.2022-01.com.example:iscsi.server
Created target iqn.2022-01.com.example:iscsi.server.
Created TPG 1.
Global pref auto_add_default_portal=true
Created default portal listening on all IPs (0.0.0.0), port 3260.

/> cd iscsi/iqn.2022-01.com.example:iscsi.server/
/iscsi/iqn.20...:iscsi.server> cd tpg1/
/iscsi/iqn.20...i.server/tpg1> luns/ create /backstores/block/iscsi_store 
Created LUN 0.

/iscsi/iqn.20...i.server/tpg1> acls/ create iqn.2022-01.com.example:iscsi.client
Created Node ACL for iqn.2022-01.com.example:iscsi.client
Created mapped LUN 0.

/iscsi/iqn.20...i.server/tpg1> portals/ create 192.168.253.128 3260
Using default IP port 3260
Could not create NetworkPortal in configFS

/iscsi/iqn.20...i.server/tpg1> set attribute authentication=0
Parameter authentication is now '0'.
/iscsi/iqn.20...i.server/tpg1> set attribute generate_node_acls=0
Parameter generate_node_acls is now '0'.

/iscsi/iqn.20...i.server/tpg1> cd ..
/iscsi/iqn.20...:iscsi.server> cd ..
/iscsi> cd ..
/> saveconfig 
Configuration saved to /etc/target/saveconfig.json
/> exit
Global pref auto_save_on_exit=true
Last 10 configs saved in /etc/target/backup/.
Configuration saved to /etc/target/saveconfig.json

[root@SAN-server ~]# targetcli
targetcli shell version 2.1.53
Copyright 2011-2013 by Datera, Inc and others.
For help on commands, type 'help'.

/> ls
o- / ........................................................................ [...]
  o- backstores ............................................................. [...]
  | o- block ................................................. [Storage Objects: 1]
  | | o- iscsi_store ... [/dev/iscsi_vg/iscsi_store (10.0GiB) write-thru activated]
  | |   o- alua .................................................. [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ...................... [ALUA state: Active/optimized]
  | o- fileio ................................................ [Storage Objects: 0]
  | o- pscsi ................................................. [Storage Objects: 0]
  | o- ramdisk ............................................... [Storage Objects: 0]
  o- iscsi ........................................................... [Targets: 1]
  | o- iqn.2022-01.com.example:iscsi.server ............................. [TPGs: 1]
  |   o- tpg1 .............................................. [no-gen-acls, no-auth]
  |     o- acls ......................................................... [ACLs: 1]
  |     | o- iqn.2022-01.com.example:iscsi.client ................ [Mapped LUNs: 1]
  |     |   o- mapped_lun0 .......................... [lun0 block/iscsi_store (rw)]
  |     o- luns ......................................................... [LUNs: 1]
  |     | o- lun0  [block/iscsi_store (/dev/iscsi_vg/iscsi_store) (default_tg_pt_gp)]
  |     o- portals ................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 .................................................... [OK]
  o- loopback ........................................................ [Targets: 0]
/> exit
```

###### 客户端配置

```bash
#客户端
[root@wentan ~]# hostnamectl set-hostname SAN-client
[root@wentan ~]# bash
#安装iscsi看客户端初始化工具
[root@SAN-client ~]# yum install -y iscsi-initiator-utils

[root@SAN-client ~]# vim /etc/iscsi/initiatorname.iscsi 
InitiatorName=iqn.2022-01.com.example:iscsi.client

#接下来允许iscsi服务,开启iscsi服务
[root@SAN-client ~]# systemctl enable iscsi iscsid.service 
Created symlink /etc/systemd/system/remote-fs.target.wants/iscsi.service → /usr/lib/systemd/system/iscsi.service.
Created symlink /etc/systemd/system/multi-user.target.wants/iscsid.service → /usr/lib/systemd/system/iscsid.service.
[root@SAN-client ~]# systemctl start iscsi iscsid.service 

#接下来通过iscsi的发现机制  来发现对方ip地址是否有共享给我的存储
[root@SAN-client ~]# iscsiadm -m discovery -t st -p 192.168.253.128
192.168.253.128:3260,1 iqn.2022-01.com.example:iscsi.server

#选择自动的挂上SAN
[root@SAN-client ~]# iscsiadm -m node -l
Logging in to [iface: default, target: iqn.2022-01.com.example:iscsi.server, portal: 192.168.253.128,3260]
Login to [iface: default, target: iqn.2022-01.com.example:iscsi.server, portal: 192.168.253.128,3260] successful.

#接下来发现这块设备,发现名字为sda  大小为10G
[root@SAN-client ~]# lsblk 
NAME          MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda             8:0    0   10G  0 disk 
sr0            11:0    1 1024M  0 rom  
nvme0n1       259:0    0   25G  0 disk 
├─nvme0n1p1   259:1    0    1G  0 part /boot
└─nvme0n1p2   259:2    0   24G  0 part 
  ├─rhel-root 253:0    0   22G  0 lvm  /
  └─rhel-swap 253:1    0    2G  0 lvm  [SWAP]

#我们需要对这块硬盘做分区
[root@SAN-client ~]# fdisk /dev/sda 

Welcome to fdisk (util-linux 2.32.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0x80e3c182.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-20963327, default 2048): 
Last sector, +sectors or +size{K,M,G,T,P} (2048-20963327, default 20963327): +5G

Created a new partition 1 of type 'Linux' and of size 5 GiB.

Command (m for help): p
Disk /dev/sda: 10 GiB, 10733223936 bytes, 20963328 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 1048576 bytes
Disklabel type: dos
Disk identifier: 0x80e3c182

Device     Boot Start      End  Sectors Size Id Type
/dev/sda1        2048 10487807 10485760   5G 83 Linux

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

#把分区做成文件系统 ext4格式
[root@SAN-client ~]# mkfs.ext4 /dev/sda1 
mke2fs 1.44.3 (10-July-2018)
Creating filesystem with 1310720 4k blocks and 327680 inodes
Filesystem UUID: ac62797b-da6c-4bf3-a09b-7808b6081ebf
Superblock backups stored on blocks: 
    32768, 98304, 163840, 229376, 294912, 819200, 884736

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done

#编辑自动挂载列表
[root@SAN-client ~]# vim /etc/fstab
/dev/sda1       /SAN    ext4    defaults        0 0
[root@SAN-client ~]# mkdir /SAN
[root@SAN-client ~]# mount -a
[root@SAN-client ~]# df -hT
Filesystem            Type      Size  Used Avail Use% Mounted on
devtmpfs              devtmpfs  1.9G     0  1.9G   0% /dev
tmpfs                 tmpfs     1.9G     0  1.9G   0% /dev/shm
tmpfs                 tmpfs     1.9G   10M  1.9G   1% /run
tmpfs                 tmpfs     1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/mapper/rhel-root xfs        22G  4.2G   18G  19% /
/dev/nvme0n1p1        xfs      1014M  169M  846M  17% /boot
tmpfs                 tmpfs     376M   16K  376M   1% /run/user/42
tmpfs                 tmpfs     376M  4.6M  372M   2% /run/user/1000
tmpfs                 tmpfs     376M  4.0K  376M   1% /run/user/0
/dev/sda1             ext4      4.9G   20M  4.6G   1% /SAN

#到这里就全都成功了
```
