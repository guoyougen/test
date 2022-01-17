<div style='display: none'>
  Date: 2022-01-17 19:00:32
  LastEditors: gyg
  LastEditTime: 2022-01-17 19:00:33
  FilePath: \test\1_15@linux存储设备.mm.md
</div>
linux设备命名方式
SATA 机械硬盘 /usb   |  /dev/sda  | /dev/sdb |  /dev/sdc
:-: | :-: | :-: |:-:
虚拟机里面           |   /dev/vda  | /dev/vdb|/dev/vdc
SSD 固态硬盘          | /dev/nvme1|/dev/nvme2|/dev/nvme3
逻辑卷|/dev/mapper1|/dev/mapper2|/dev/mapper3

识别文件系统的设备

分区：将硬盘的整体存储空间划分多个独立的区域

格式化：按照要求创建一个新的文件系统

 文件系统：NTFS FAT32 ext2 ext3 ext4 xfs
 光盘：ISO

 集群文件系统
 分布式文件系统
 网络文件系统 NFS

存储方式 三类

1. DAS：直连式存储 最传统 直接把磁盘连接在设备
2. NAS：网络附属存储 基于文件的存储，需要服务器对自己的存储进行分区格式化，挂载变成了一个目录 通过server把自己的文件夹共享出去 用户client 通过网络来访问server，共享文件夹
用户把文件信息发给server ，然后server替这个用户进行 读写操作
3. SAN：storage area network 存储区域网络 是基于数据块的存储 存储区域网络 可以理解为读取了一大块虚拟磁盘

虚拟磁盘是通过存储服务器，然后通过LUN ID 每一个LUN都有一个ID 可以被用户挂载

SCSI服务就是用来实现SAN存储网络的

nas是靠以太网传输的 TCP/IP
10G 通过NAS网络传输 需要分片 TCP/IP 最大传输单元 1500byte 再加上TCP服务头部 ip头部 mac头部 
nas传输文件 效率低下 


SAN有两种传输模式
1. 以太网
2. FC网络  FC交换机 FC网卡 光纤 FC网络支持巨型数据包 传输速度可以达到 每秒 8G/16G 通常把SAN 叫做FCSAN

