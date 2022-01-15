<div style='display: none'>
  Date: 2022-01-08 09:27:55
  LastEditors: gyg
  LastEditTime: 2022-01-15 14:30:44
  FilePath: \test\4_train.mm_20220109152326.md
</div>

# 2022_01_08_实训日记


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
