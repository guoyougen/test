<div style='display: none'>
  Date: 2022-01-15 20:15:35
  LastEditors: gyg
  LastEditTime: 2022-01-15 20:27:40
  FilePath: \test\1_11@bashshell.mm.md
</div>

# 初识 bashshell

> 命令解释器 他能识别我们输入的各种命令 并传递给系统
类似于 Windows 当中的powershell 、cmd

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
