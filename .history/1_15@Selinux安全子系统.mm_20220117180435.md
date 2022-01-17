<div style='display: none'>
  Date: 2022-01-15 22:43:40
  LastEditors: gyg
  LastEditTime: 2022-01-17 18:04:35
  FilePath: \test\1_15@Selinux安全子系统.mm.md
</div>

# 什么是SELinux

>SELinux（Security-Enhanced Linux）是美国国家安全局在Linux开源社区的帮助下开发的一个强制访问控制（MAC，Mandatory Access Control）的安全子系统。RHEL 7系统使用SELinux技术的目的是为了让各个服务进程都受到约束，使其仅获取本应获取的资源。

selinux在Linux环境中有非常重要的用途，在我们Linux环境中，如果禁用selinux，是可以减少非常多的报错，但是在**生产环境中想当不推荐**

## selinux在linux当中的三个用途

1. 可以允许或者拒绝访问我们的文件和资源

2. 可以明确一个进程，服务，可以调用哪些端口

3. 可以对一个文件做更精确的限制，远远比我们的用户权限高很多，更精确。

## selinux 的三个模式

1. **强制模式 enforcing:**  selinux 会强制执行严格的访问控制，系统默认就是这个模式，它会拒绝其他服务访问违规端口，同时也限制这些服务的访问执行权限，而且但凡发现了违规行为，会记录在日志里面。

2. **许可模式 permissive:**  不执行严格的访问控制，但是会记录这些违规的警告，这个模式主要用于测试和故障排除。

3. **关闭模式 disabled:**  selinux功能完全关闭，不拒绝任何的违规行为，而且不予记录。

三种模式的查看和切换，**只是当前有效，关机重启后会变成原来的模式**

模式|解释
 :-: | :-: 
getenforce |     #查看当前模式
setenforce 0|    #设置为许可模式
setenforce 1 |   #设置为强制模式

关闭模式不能简单通过命令来实现，需要修改配置文件，同时修改配置文件也是永久修改默认模式的方法

`selinux的配置文件：/etc/selinux/config`

实例

```bash
[root@wentan ~]# getenforce 
Enforcing
[root@wentan ~]# setenforce 0
[root@wentan ~]# getenforce 
Permissive
[root@wentan ~]# setenforce 1
[root@wentan ~]# getenforce 
Enforcing

[root@wentan ~]# vim /etc/selinux/config
SELINUX=disabled    #修改为disable
[root@wentan ~]# reboot    #重启生效
[root@wentan ~]# getenforce 
Disabled

#最后再改回enforcing
```

## selinux 的三个规则

1. **targeted:** 默认规则，针对网络服务的限制比较多，对本机的限制比较少
2. **minimun:** 只会选择性的保护我们的系统模块 例如内核 device 
3. **mls:** 完整的selinux限制，对系统各方面都做限制

默认是targeted 模式，因为一般只需要对网络服务做限制

```bash
 #安装httpd服务
[root@wentan ~]# yum install -y httpd
#开启httpd服务
[root@wentan ~]# systemctl start httpd
#关闭防火墙
[root@wentan ~]# systemctl stop firewalld.service
```

## 安全上下文

>在启动selinux的时候，selinux会给系统中的资源设置一个安全上下文，保存在 /etc/selinux/targeted/contexts/files/file_contexts

```bash
#查询某文件上下文
[root@wentan ~]# ls -Zd /var/www/html/
system_u:object_r:httpd_sys_content_t:s0 /var/www/html/
#在文件上设置的selinux安全上下文，是由用户段，角色段，类型段等多个信息组成
```

字段信息|解释
 :-: | :-: 
system_u   |     #代表了系统进程的身份
object_r    |    #代表了文件目录的角色
httpd_sys_content_t|  #代表了网站服务的系统文件
S0 |  #sensitivity

## semanage管理安全策略

>使用getsebool命令查询并过滤出所有与HTTP协议相关的安全策略。其中，off为禁止状态，on为允许状态。

```bash
 #查看某服务的安全策略
[root@wentan ~]# getsebool -a | grep http
httpd_anon_write --> off
httpd_builtin_scripting --> on
httpd_can_check_spam --> off
httpd_can_connect_ftp --> off
httpd_can_connect_ldap --> off
httpd_can_connect_mythtv --> off
httpd_can_connect_zabbix --> off
httpd_can_network_connect --> off
httpd_can_network_connect_cobbler --> off
httpd_can_network_connect_db --> off
httpd_can_network_memcache --> off
httpd_can_network_relay --> off
httpd_can_sendmail --> off
httpd_dbus_avahi --> off
httpd_dbus_sssd --> off
httpd_dontaudit_search_dirs --> off
httpd_enable_cgi --> on
httpd_enable_ftp_server --> off
httpd_enable_homedirs --> off
httpd_execmem --> off
httpd_graceful_shutdown --> off
httpd_manage_ipa --> off
httpd_mod_auth_ntlm_winbind --> off
httpd_mod_auth_pam --> off
httpd_read_user_content --> off
httpd_run_ipa --> off
httpd_run_preupgrade --> off
httpd_run_stickshift --> off
httpd_serve_cobbler_files --> off
httpd_setrlimit --> off
httpd_ssi_exec --> off
httpd_sys_script_anon_write --> off
httpd_tmp_exec --> off
httpd_tty_comm --> off
httpd_unified --> off
httpd_use_cifs --> off
httpd_use_fusefs --> off
httpd_use_gpg --> off
httpd_use_nfs --> off
httpd_use_openstack --> off
httpd_use_sasl --> off
httpd_verify_dns --> off
mysql_connect_http --> off
named_tcp_bind_http_port --> off
prosody_bind_http_port --> off
```

面对如此多的SELinux域安全策略规则，实在没有必要逐个理解它们，我们只要能通过名字大致猜测出相关的策略用途就足够了。比如，想要开启httpd服务的个人用户主页功能，那么用到的SELinux域安全策略应该是httpd_enable_homedir

### semanage-port 管理安全上下文

参数|解释
 :-: | :-: 
-a         |#增加
-d|        #删除
-t |       #指定文件上下文类型

实验：修改httpd服务监听端口为82（默认是80）

```bash
[root@wentan ~]# vim /etc/httpd/conf/httpd.conf
#Listen 12.34.56.78:80
Listen 82         #需要修改配置文件为Listen 82端口
[root@wentan ~]# cat /etc/httpd/conf/httpd.conf | grep 82
Listen 82

[root@wentan ~]# journalctl -xe
[root@wentan ~]# semanage port -a -t http_port_t -p tcp 82
#重启服务
[root@wentan ~]# systemctl restart httpd

#浏览器访问本机ip地址的82端口
```
