[//]: # (哈哈我是注释，不会在浏览器中显示。
  Date: 2022-01-15 20:15:35
  LastEditors: gyg
  LastEditTime: 2022-01-19 19:39:21
  FilePath: \test\1_11@bashshell.mm.md
)

# bashshell

> 命令解释器 他能识别我们输入的各种命令 并传递给系统
类似于 Windows 当中的powershell 、cmd

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [bashshell](#bashshell)
  - [bashshell是什么](#bashshell是什么)
  - [文件通配符](#文件通配符)
  - [如何变量赋值](#如何变量赋值)
  - [命令别名](#命令别名)
    - [如何永久写入别名](#如何永久写入别名)

<!-- /code_chunk_output -->
## bashshell是什么

在Linux当中 shell 既是用户交互界面 又是控制系统的语言

在winodws当中 图形化是用户交互界面 shell是控制系统的语言

shell是系统的用户界面，提供了用户与内核进行交互操作的一种接口。它接收用户输入的命令并把它送入内核去执行。实际上shell是一个命令解释器，它解释用户输入的命令并且把用户的意图传达给内核。(可以理解为用户与内核之间的翻译官角色)

![ee2616861b400449c0fdbd920cb42b99](https://s2.loli.net/2022/01/15/GeakCXvFSxTYQPi.png)

## 文件通配符

|  参数  |                解释                |
| :----: | :--------------------------------: |
|   *    | #匹配0次或无数次 所有    cp   ./*  |
|   ?    |         #匹配任意单个字符          |
| [0-9]  |             #匹配数字              |
| [abc]  |             #匹配字母              |
| [^abc] |     #匹配列表内除外的所有字符      |
|   ~    |          #指向你的家目录           |
|   $    |             #表示参数              |
|   ‘’   | #输入一个命令 作为另一个命令的参数 |

## 如何变量赋值

```bash
[root@wentan ~]# username=dwt
[root@wentan ~]# echo $username            //引用这个变量
dwt
#临时变量仅在当前shell中有效

#系统变量，永久有效
[root@wentan ~]# echo $PATH        //用户可执行文件
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
[root@wentan ~]# echo $HOME        //用户家目录位置
/root
[root@wentan ~]# echo $UID        //用户的UID
0
```

## 命令别名

```bash
alias                 #别名
which   +  名称       #可以查看这个名称的别名
#别名只是当前有限  而且只是本次shell登录有效  
alias dir=‘ls  -al’    讲ls -al  别名写成  dir  这样 dir就会执行ls -al的作用
```

```bash
实例

[root@wentan ~]# which ll
alias ll='ls -l --color=auto'
    /usr/bin/ls

[root@wentan ~]# alias showip='ip addr | grep ens160'
[root@wentan ~]# showip
2: ens160: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    inet 192.168.200.128/24 brd 192.168.200.255 scope global dynamic noprefixroute ens160
[root@wentan ~]# which showip
alias showip='ip addr | grep ens160'
    /usr/sbin/ip
    /usr/bin/grep
```

### 如何永久写入别名

```bash
编辑 /etc/bashrc
在文件最低端写入别名 alias showip=’ip addr’
bash  刷新 
which dir 验证是否成功
which showip
```

```bash
实例

[root@wentan ~]# vim /etc/bashrc 
alias showip='ip addr | grep ens160'
[root@wentan ~]# bash
[root@wentan ~]# which dir
/usr/bin/dir
[root@wentan ~]# which showip
alias showip='ip addr | grep ens160'
    /usr/sbin/ip
    /usr/bin/grep
```

注意：**`/etc/bashrc` 非常重要 关系到每次开机的bash环境**