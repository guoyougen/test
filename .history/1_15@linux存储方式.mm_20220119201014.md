[//]: # (哈哈我是注释，不会在浏览器中显示。
  Date: 2022-01-17 19:00:32
  LastEditors: gyg
  LastEditTime: 2022-01-19 20:10:14
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
