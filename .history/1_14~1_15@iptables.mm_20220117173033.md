<div style='display: none'>
  Date: 2022-01-15 22:42:46
  LastEditors: gyg
  LastEditTime: 2022-01-17 17:30:33
  FilePath: \test\1_14~1_15@iptables.mm.md
</div>

# iptables 服务

>其实iptables服务并不是真正的防火墙，只是用来定义防火墙功能的防火墙管理工具，将定义好的规则交给内核里面的netfilter功能，即网络过滤器来读取，从而真正实现防火墙功能。

## IPtables 抵挡封包的方式

1. 拒绝让Internet包进入Linux主机的某些port

2. 拒绝让某些来源ip封包进入

3. 拒绝让带有某些flag标记的包进入

4. 分析硬件地址mac来提供服务

## 五链

>iptables命令中设置了数据过滤或者是处理数据包的策略，叫做规则。将多个规则合成一个链，叫做规则链，规则链则依据数据包位置不同分成5类

|    名称     |                        解释                        |
| :---------: | :------------------------------------------------: |
| PREROUTING  |  #在进行路由判断之前所要进行的规则(DNAT/REDIRECT)  |
|    INPUT    |                 #处理入站的数据包                  |
|   OUTPUT    |                 #处理出站的数据包                  |
|   FORWARD   |                 #处理转发的数据包                  |
| POSTROUTING | #在进行路由判断之后所要进行的规则(SNAT/MASQUERADE) |

## 四表

>iptables中的规则表适用于容纳规则链，规则表默认是允许状态的，那么规则链就是设置被禁止的规则，而反之如果规则表是禁止状态的，那么规则链就是设置被允许的规则。

|  表名  |                功能                |
| :----: | :--------------------------------: |
|  raw   |   确定是否对该数据包进行状态跟踪   |
| mangle |    为数据包设置标记（较少使用）    |
|  nat   | 修改数据包中的源、目标IP地址或端口 |
| filter |    确定是否放行该数据包（过滤）    |

![851704262675c3a76491391b6539b86e](https://s2.loli.net/2022/01/17/FrcJuPvyD3GlKk9.png)

- 规则表的先后顺序:raw→mangle→nat→filter
- 规则链的先后顺序:
  1. 入站顺序 PREROUTING→INPUT
  2. 出站顺序 OUTPUT→POSTROUTING
  3. 转发顺序 PREROUTING→FORWARD→POSTROUTING

![5eaf219f7e0e72d14ed1d6a0e934e985](https://s2.loli.net/2022/01/17/r1UajPqH9QS4Ktz.png)

## iptables命令中常见的控制类型

类型|	功能
 :-: | :-: 
ACCEPT|	允许通过
LOG	|记录日志，传递给下一条规则
REJECT|	拒绝通过，可以给提示
DROP	|直接丢弃，不给回应

## 用法格式

### iptables的用法格式

`iptables [-t 表名] 选项 [链名] [条件] [-j 控制类型]`
`iptables –[A|I 链] [-i|o 网络接口] [-p 协议] [-s 来源ip/网域] [-d 目标ip/网域] –j[ACCEPT|DROP]`
参数|	作用
 :-: | :-: 
-P	|设置默认策略:`iptables -P INPUT (DROP
-F	|清空规则链
-L	|查看规则链
-A	|在规则链的末尾加入新规则
-I num|	在规则链的头部加入新规则
-D num|	删除某一条规则
-s	|匹配来源地址IP/MASK，加叹号"!"表示除这个IP外。
-d	|匹配目标地址
-i |网卡名称	匹配从这块网卡流入的数据
-o |网卡名称	匹配从这块网卡流出的数据
-p	|匹配协议,如tcp,udp,icmp
--dport num|	匹配目标端口号
--sport num	|匹配来源端口号 

### 查看规则

`[root@wentan ~]# iptables [-t tables] [-L] [-nv]`
参数| 解释
 :-: | :-: 
-t   |#后面接table,例如nat或filter，如果省略，默认显示filter
-L|  #列出目前的table的规则
-n | #不进行IP与主机名的反查，显示信息的速度会快很多
-v  |#列出更多的信息，包括封包数，相关网络接口等

### 删除规则

`[root@wentan ~]# iptables [-t tables] [-FXZ]`
参数|解释
 :-: | :-: 
-F   #清除所有的规则
-X  #清除所有自定义规则
-Z  #将计数与流量统计清零
2.5.4 默认策略修改
[root@wentan ~]#  iptables [-t tables] -P [链名] [ACCEPT/DROP]
2.5.5 实例
实例1

# 所有的来自 lo 这个网口的封包，都予以接受
[root@wentan ~]# iptables -A INPUT -i lo -j ACCEPT

# 目标来自 192.168.1.200 这个 IP 的封包都予以接受
[root@wentan ~]# iptables -A INPUT -s 192.168.1.200 -j ACCEPT

# 192.168.1.0/24 可接受，但 192.168.1.10 丢弃
[root@wentan ~]# iptables -A INPUT -s 
192.168.1.0/24 -j ACCEPT
[root@wentan ~]# iptables -A INPUT -s 192.168.1.10 -j DROP

[root@wentan ~]# iptables -L -n --line-numbers 
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination         
1    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           
2    ACCEPT     all  --  192.168.1.200        0.0.0.0/0           
3    ACCEPT     all  --  192.168.1.0/24       0.0.0.0/0           
4    DROP       all  --  192.168.1.10         0.0.0.0/0 
#删除第三条规则
[root@wentan ~]# iptables -D INPUT 3
[root@wentan ~]# iptables -L -n --line-numbers 
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination         
1    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           
2    ACCEPT     all  --  192.168.1.200        0.0.0.0/0           
3    DROP       all  --  192.168.1.10         0.0.0.0/0
案例2 Iptables 开放tcp、udp端口

 #开放samba端口（udp137，138；tcp139,445）
[root@wentan ~]# iptables -A INPUT -i ens160 -p udp --dport 137:138 -j ACCEPT
[root@wentan ~]# iptables -A INPUT -i ens160 -p tcp --dport 139 -j ACCEPT 
[root@wentan ~]# iptables -A INPUT -i ens160 -p tcp --dport 445 -j ACCEPT

实例3 匹配ICMP端口和ICMP类型

iptables –A INPUT –p icmp --icmp-type 类型 –j REJECT
 #参数：--icmp-type ：后面必须要接 ICMP 的封包类型，也可以使用代号，例如 8 代表 echo request 的意思。（可自查询ICMP-type对应表）
[root@wentan ~]# iptables -A INPUT -p icmp --icmp-type 8 -j REJECT
ICMP类型

TYPE	CODE	Description
0	0	Echo Reply——回显应答（Ping应答）
3	0	Network Unreachable——网络不可达
3	1	Host Unreachable——主机不可达
3	2	Protocol Unreachable——协议不可达
3	3	Port Unreachable——端口不可达
3	4	Fragmentation needed but no frag. bit set——需要进行分片但设置不分片比特
3	5	Source routing failed——源站选路失败
3	6	Destination network unknown——目的网络未知
3	7	Destination host unknown——目的主机未知
3	8	Source host isolated (obsolete)——源主机被隔离（作废不用）
3	9	Destination network administratively prohibited——目的网络被强制禁止
3	10	Destination host administratively prohibited——目的主机被强制禁止
3	11	Network unreachable for TOS——由于服务类型TOS，网络不可达
3	12	Host unreachable for TOS——由于服务类型TOS，主机不可达
3	13	Communication administratively prohibited by filtering——由于过滤，通信被强制禁止
3	14	Host precedence violation——主机越权
3	15	Precedence cutoff in effect——优先中止生效
4	0	Source quench——源端被关闭（基本流控制）
5	0	Redirect for network——对网络重定向
5	1	Redirect for host——对主机重定向
5	2	Redirect for TOS and network——对服务类型和网络重定向
5	3	Redirect for TOS and host——对服务类型和主机重定向
8	0	Echo request——回显请求（Ping请求）
9	0	Router advertisement——路由器通告
10	0	Route solicitation——路由器请求
11	0	TTL equals 0 during transit——传输期间生存时间为0
11	1	TTL equals 0 during reassembly——在数据报组装期间生存时间为0
12	0	IP header bad (catchall error)——坏的IP首部（包括各种差错）
12	1	Required options missing——缺少必需的选项
13	0	Timestamp request (obsolete)——时间戳请求（作废不用）
14	
Timestamp reply (obsolete)——时间戳应答（作废不用）
15	0	Information request (obsolete)——信息请求（作废不用）
16	0	Information reply (obsolete)——信息应答（作废不用）
17	0	Address mask request——地址掩码请求
18	0	Address mask reply——地址掩码应答
实例4 iptables --syn 的处理方式

指定TCP匹配扩展

使用 --tcp-flags 选项可以根据tcp包

 #打标记
[root@wentan ~]# iptables -A INPUT -p tcp --tcp-flags SYN,FIN,ACK SYN
[root@wentan ~]# iptables -A FORWARD -p tcp --tcp-flags ALL SYN,ACK
#上实例中第一个表示SYN、ACK、FIN的标志都检查，但是只有SYN匹配。第二个表示ALL（SYN，ACK，FIN，RST，URG，PSH）的标志都检查，但是只有设置了SYN和ACK的匹配

[root@wentan ~]# iptables -A FORWARD -p tcp --syn
#选项--syn相当于"--tcp-flags SYN,RST,ACK SYN"的简写
实例5

[root@wentan ~] # iptables -A INPUT -m 模块名 --state 状态参数：
-m    iptables的模块        state: 状态检查        mac:网卡硬件地址
--state     连接追踪中的状态：
    NEW: 新建立一个会话        
    ESTABLISHED：已建立的连接        
    RELATED: 有关联关系的连接        
    INVALID: 无法识别的连接
# 放行ssh的首次连接状态
[root@wentan ~]# iptables -A INPUT -p tcp --dport 22 -m state --state NEW -j ACCEPT
# 对局域网内mac地址为00:0C:29:56:A6:A2主机开放其联机
[root@wentan ~]# iptables -A INPUT -m mac --mac-source 01:00:5E:C6:49:A2 -j ACCEPT
实例6 本地端口转发

[root@wentan ~] # iptables -t nat -A PREROUTING -p tcp --dport 6666 -j REDIRECT  --to-port 22
实例7 iptables 保存和恢复

 #保存
[root@wentan ~]# iptables-save > /etc/sysconfig/iptables
#恢复
[root@wentan ~]# iptables-restore /etc/sysconfig/iptables
在Linux系统中，firewalld服务和iptables服务是冲突的，所以二者只有一个能运行，如果开启了iptables，那么必须永久关闭防火墙，iptables也会有默认的基本配置，保证网络安全。

[root@wentan ~] # iptables stop firewall
[root@wentan ~]# iptables disable firewall
这样才能用iptables

 #可以删掉所有的规则
[root@wentan ~]# iptables -F
#保存当前配置
[root@wentan ~]# iptables-save