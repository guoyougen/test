[//]: # (哈哈我是注释，不会在浏览器中显示。
  Date: 2022-01-17 19:00:32
  LastEditors: gyg
  LastEditTime: 2022-01-18 20:22:20
  FilePath: \test\1_15@linux存储方式.mm.md
)


# linux存储方式

## linux存储设备命名方式

>我们在计算机中保存的文件大都是保存在电脑硬盘中，部分同学还会将自己的文件存储在网盘中，网盘中的文件只能说是用来做备份，并不能像本地硬盘上的数据那样随时使用，我们在学习存储管理的时候主要学习的就是如何对自己计算机上的硬盘进行管理，目前我们主流的硬盘主要有两种形态，一种是**机械硬盘(HDD)**，一种是**固态硬盘(SSD)**

设备|第一块|第二块|第三块
 :-: | :-: | :-: | :-: 
SATA 机械硬盘 /usb   |  /dev/sda  | /dev/sdb |  /dev/sdc
虚拟机里面           |   /dev/vda  | /dev/vdb|/dev/vdc
SSD 固态硬盘          | /dev/nvme0n1|/dev/nvme0n2|/dev/nvme0n3
逻辑卷|/dev/mapper1|/dev/mapper2|/dev/mapper3

## 存储方式 三类

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

