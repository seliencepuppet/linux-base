samba服务是一种高级文件共享服务，支持的操作系统平台分别是windows和linux系统

samba存在的目的就是实现跨平台共享

samba服务开启的端口分别是139和445端口

```shell
137/udp (betbios)
138/udp
139/tcp
445/tcp
```

### samba本身受selinux控制为了保证samba正常运行需要先关闭selinux

<br/>

系统用户samba密码

安全级别: user

share

samba 可以关联另外一台服务器

Windows 访问：
UNC

\\IP\software
共享权限
文件系统权限

```shell
D:\share\tools
Software
E:\test\web
```

宏：变量

```shell
[shared_name]

comment = 
path = 
browseble =
public = 
read only = 
writable = 
write_list = user1, user2, .... 
@group, +group

valid_users = 
invaild_users = 

/share/test

[tools]

[root@test .python]# testparm
Load smb config files from /etc/samba/smb.conf
rlimit_max: increasing rlimit_max (1024) to minimum Windows limit (16384)
Processing section "[homes]"
Processing section "[printers]"
Processing section "[tools]"
Loaded services file OK.
Server role: ROLE_STANDALONE
Press enter to see a dump of your service definitions


[root@test .python]# man smbpasswd
[root@test .python]# smbpasswd -a eclips
New SMB password:
Retype new SMB password:
Added user eclips.
```

### smbclinet 客户端

```shell
smbclient 
-L Netbios Name
-U USERNAME
-T
```
<br/>

linux系统访问smb服务的方法如下

```shell
[root@zhangyz ~]# mount -t cifs //192.168.1.130/tools /mnt -o username=eclips                
```

windows系统访问smb服务的方法如下

```shell
//192.168.231.130/tools /mnt   cifs    credential=/etc/samba/cred.passwd 0 0
```



新建一个共享: tools开放给mygrp中的所有用户吗具有读写权限其它用户只有读权限

```shell
[root@zhangyz ~]# vim /etc/samba/smb.conf
[share_name]
public = yes
write list = @mygrp

[tools]
comment = 
path = 
guest os = yes
write list = @mygrp
```

samba: 基于ip的访问控制
	
```shell
samba
hosts allow = 192.168.21. 127.       # 表示允许网段中的主机进行访问
```

	yum -y install samba-3x-swat                    非独立进程


	守护进程
	standalone
	transient， 短暂

	xinetd                  901                 临时启动 ------- 转交请求

	service xinerd start




tcp_wrap 
ldd `which vsftpd` | grep wrap 
tcp_wrap=YES

/etc/vsftpd/user_list
user1
user2
------------------
user_list=YES 
userlist_deny=YES  黑名单
/etc/vsftpd/user_list


user_list=YES
userlist_deny=NO   白名单
/etc/vsftpd/user_list

	--------------------------
/etc/vsftpd/ftpusers(黑名单)
---------------------------
iptables 
-------------------------------

/etc/init.d/vsftpd status
netstat -tunpl | grep vsftpd
tcp        0      0 0.0.0.0:21                  0.0.0.0:*    

---------------------------------------------
1.user_config_dir=/etc/vsftpd/userconf 全局(/etc/vsftpd.)
(vim /etc/vsftpd/vsftpd.conf)
2.mkdir /etc/vsftpd/userconf
3.cd /etc/vsftpd/userconf
4.touch zhangsan   
local_max_rate=80000
-----------------------------
白名单
chroot_local_user=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd/chroot_list(用户名)

黑名单
chroot_local_user=NO
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd/chroot_list
----------------------------------------------------
man vsftpd.conf

ssh 提供安全的远程连接（telnet）
sftp 提供ftp功能
scp  (filea )client   root@192.168.1.2:/home/  把本地copy到server 
scp  root@192.168.1.2:/home/a.txt    /tmp/(local) 把server上的文件copy到本地
ssh-key-gen  公钥id_rsa.pub 公钥给谁 连接谁不要密码   
私钥id_rsa     私钥给自己 
ssh-copy-id -i /root/.ssh/id_rsa.pub root@192.168.1.2 把公钥传给server 

------------------------------------------------------
samba (smb)

rhel5 
smbd  数据传输 共享 打印 139 445 
nmbd  名称解析

rhel6 smbd 139 445 

-------------------
samba 文件服务器

www.baidu.com
(AD) 
1.满足灵活的权限管理
2.隔离windows的病毒

1.安装软件包
```shell
[root@zhangyz ~]# yum install samba samba-client -y
```

2.启动脚本
```shell
[root@zhangyz ~]# /etc/rc.d/init.d/smb  start
```

3.客户端命令工具
```shell
[root@zhangyz ~]# rpm -ql samba-client
/usr/bin/findsmb
/usr/bin/nmblookup
/usr/bin/rpcclient
/usr/bin/sharesec
/usr/bin/smbcacls
/usr/bin/smbclient
/usr/bin/smbget
/usr/bin/smbprint
/usr/bin/smbspool
/usr/bin/smbta-util
/usr/bin/smbtar
/usr/bin/smbtree
/usr/share/man/man1/findsmb.1.gz
/usr/share/man/man1/nmblookup.1.gz
/usr/share/man/man1/rpcclient.1.gz
/usr/share/man/man1/sharesec.1.gz
/usr/share/man/man1/smbcacls.1.gz
/usr/share/man/man1/smbclient.1.gz
/usr/share/man/man1/smbget.1.gz
/usr/share/man/man1/smbtar.1.gz
/usr/share/man/man1/smbtree.1.gz
/usr/share/man/man8/smbspool.8.gz
/usr/share/man/man8/smbta-util.8.gz
```

主配文件
```shell
[root@zhangyz ~]# rpm -qc samba-common
/etc/samba/lmhosts
/etc/samba/smb.conf
/etc/sysconfig/samba 
```

testparm语法测试
```shell
[root@zhangyz ~]# vim /etc/samba/smb.conf

SELINUX NOTES:

Global Settings（全局设置 当全局和局部配置有冲突以局部为准）
workgroup = MYGROUP                     定义工作组的名称
server string = Samba Server Version %v 显示samba的版本
interfaces = lo eth0 192.168.12.2/24 192.168.13.2/24 监听的网卡
hosts allow = 127. 192.168.1. 192.168.1.1  白名单
log file = /var/log/samba/log.%m  每个机器一个日志
max log size = 50 日志轮转的大小（kb）超过50kb重新产生新的日志文件
security = user             认证模式
passdb backend = tdbsam     数据库后台
```

认证模式
* share最低级别所有人可以访问
* user基于用户认证用户密码
* server需要一台专门的用户密码认证的server
* domain AD域作为AD域成员机器
* ADS AD活动目录有了AD域的管理员

认证模式
```shell
security = share
```

表示注释 #
```shell
[root@zhangyz ~]# mkdir /share
[root@zhangyz ~]# chmod 777 /share/ -R
[root@zhangyz ~]# vim /etc/samba/smb.conf
[share] # 共享名称
comment = file-server # 描述
path = /share # 共享路径
writable = yes # 可写如果=no表示只读 
guest ok = yes  
browseable = # 是否可浏览
printable = yes # 指定为打印机
read_only = yes # 只读
```

重启samba服务
```shell
[root@zhangyz ~]# /etc/init.d/smb restart
Shutting down SMB services:                                [  OK  ]
Starting SMB services:                                     [  OK  ]
```


windows访问samba服务器 \\ip --- share --- 右键 --- 映射网络启动器
```cmd
\\192.168.1.2\share
```

linux客户端查看资源
```shell
[root@zhangyz ~]# smbclient -L //192.168.1.2
Enter root's password: 
Domain=[MYGROUP] OS=[Unix] Server=[Samba 3.6.9-151.el6]
Server requested LANMAN password (share-level security) but 'client lanman auth = no' or 'client ntlmv2 auth = yes'
tree connect failed: NT_STATUS_ACCESS_DENIED
```


cifs微软开发  

基于用户认证
```shell
[root@zhangyz ~]# vim /etc/samba/smb.conf
security = user
```

创建samba用户 (添加的用户需要在本地存在)
```shell
[root@zhangyz ~]# smbpasswd -a user1
```

linux客户端访问资源
```shell
[root@zhangyz ~]# smbclient //192.168.1.2/test -U user1
```

提供一个ftp模式的访问
```shell
[root@zhangyz ~]# smbclient //192.168.1.2/share -U user1
Enter user1's password: 
Domain=[MYGROUP] OS=[Unix] Server=[Samba 3.6.9-151.el6]
tree connect failed: NT_STATUS_BAD_NETWORK_NAME
```

共享名称错误

<br>

客户端挂载
```shell
[root@zhangyz ~]# mount -t cifs //192.168.1.2/test -o username=user1 /mnt/ 
```

```shell
[root@zhangyz ~]# mount -t cifs //192.168.1.2/test -o username=user1,password=123456 /mnt/
```

```shell
[root@zhangyz ~]# mount -t cifs //192.168.1.2/test -o username=user1%123456 /mnt/
```
