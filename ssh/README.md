# ssh: secure shell

<br>

主机密钥分为两种
###### secret key: 私有密钥
###### public key: 公有密钥

<br>

OpenSSH client & server
###### 服务器: sshd	/etc/ssh/sshd_config
###### 客户端: ssh		/etc/ssh/ssh_config

<br>

安装openssh服务所需要的安装包
```shell
[root@zhangyz ~]# rpm -qa | grep ssh
openssh-server-5.3p1-117.el6.x86_64
openssh-clients-5.3p1-117.el6.x86_64
libssh2-1.4.2-2.el6_7.1.x86_64
openssh-5.3p1-117.el6.x86_64
```

<br>

刚才运行的命令有这些包是因为ssh服务已经被安装了如果没有安装需要运行以下命令
```shell
[root@zhangyz ~]# yum -y install openssh-server openssh-clients libssh2 openssh
Loaded plugins: fastestmirror, security
Setting up Reinstall Process
base                                                                                                                                                                                         | 3.7 kB     00:00     
base/primary_db                                                                                                                                                                              | 4.7 MB     00:08     
extras                                                                                                                                                                                       | 3.4 kB     00:00     
extras/primary_db                                                                                                                                                                            |  30 kB     00:00     
updates                                                                                                                                                                                      | 3.4 kB     00:00     
updates/primary_db                                                                                                                                                                           | 7.0 MB     00:13     
Installed package openssh-server-5.3p1-117.el6.x86_64 (from anaconda-CentOS-201605220104.x86_64) not available.
Installed package openssh-clients-5.3p1-117.el6.x86_64 (from anaconda-CentOS-201605220104.x86_64) not available.
Installed package openssh-5.3p1-117.el6.x86_64 (from anaconda-CentOS-201605220104.x86_64) not available.
Resolving Dependencies
--> Running transaction check
---> Package libssh2.x86_64 0:1.4.2-2.el6_7.1 will be reinstalled
--> Finished Dependency Resolution

Dependencies Resolved

==========================================================================================================================================================================================
Package                                Arch                                       Version                                             Repository                                      Size
==========================================================================================================================================================================================
installing:
libssh2                               x86_64                                  1.4.2-2.el6_7.1                                            base                                         123 k

Transaction Summary

Total download size: 123 k
Installed size: 318 k
Downloading Packages:
libssh2-1.4.2-2.el6_7.1.x86_64.rpm                                                                                                                                                           | 123 kB     00:00     
warning: rpmts_HdrFromFdno: Header V3 RSA/SHA1 Signature, key ID c105b9de: NOKEY
Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
Importing GPG key 0xC105B9DE:
Userid : CentOS-6 Key (CentOS 6 Official Signing Key) <centos-6-key@centos.org>
Package: centos-release-6-8.el6.centos.12.3.x86_64 (@anaconda-CentOS-201605220104.x86_64/6.8)
From   : /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
Running rpm_check_debug
Running Transaction Test
Transaction Test Succeeded
Running Transaction
Installing : libssh2-1.4.2-2.el6_7.1.x86_64                                                                                                                                                                   1/1 
Verifying  : libssh2-1.4.2-2.el6_7.1.x86_64                                                                                                                                                                   1/1 

Installed:
libssh2.x86_64 0:1.4.2-2.el6_7.1                                                                                                                                                                                  

Complete!
```


使用ssh-keygen生成公钥和私钥
```shell
[root@zhangyz ~]# ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
45:44:66:99:75:a3:0c:d1:16:e2:df:fe:f0:0f:43:6d root@tradeqq_user_database
The key's randomart image is:
+--[ RSA 2048]----+
|         oX*o.o  |
|         =o+oo . |
|          o.o    |
|         . . . . |
|        S   . o E|
|             o . |
|              =  |
|               * |
|                =|
+-----------------+
```

使用ssh登陆linux服务器正常途径登陆方法,在提示之后正确输入密码即可登陆
```shell
[root@zhangyz ~]# ssh root@10.203.206.234
The authenticity of host '10.203.206.234 (10.203.206.234)' can't be established.
RSA key fingerprint is 54:1e:c9:05:bc:57:a9:42:64:98:c5:5c:72:6c:aa:3e.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '10.203.206.234' (RSA) to the list of known hosts.
root@10.203.206.234's password: 
[root@web01 ~]#
```

以上命令输入完成之后会为用户自动生成公钥和私钥,生成完成之后在用户家目录下有一个 .ssh/ 的隐藏文件夹该文件夹就存放了ssh服务为用户生成的公钥和私钥
###### id_rsa: 私钥
###### id_rsa.pub: 公钥
###### known_hosts: 服务器所登陆的主机

查看方法如下
```shell
[root@zhangyz ~]# ls /root/.ssh/
id_rsa  id_rsa.pub  known_hosts
```

如果按照平时的方式使用ssh登陆的时候用的是输入密码的方式去登陆但是这种方式太麻烦而且繁琐有没有不输入密码就可以直接登陆的方式存在呢?

当然有,就是使用密钥认证的方式进行登陆

配置方法如下

server端
```shell
[root@zhangyz ~]# ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
45:44:66:99:75:a3:0c:d1:16:e2:df:fe:f0:0f:43:6d root@tradeqq_user_database
The key's randomart image is:
+--[ RSA 2048]----+
|         oX*o.o  |
|         =o+oo . |
|          o.o    |
|         . . . . |
|        S   . o E|
|             o . |
|              =  |
|               * |
|                =|
+-----------------+
[root@zhangyz ~]# ls /root/.ssh/
id_rsa  id_rsa.pub  known_hosts
[root@zhangyz ~]# cd /root/.ssh/
[root@zhangyz .ssh]# ls
id_rsa  id_rsa.pub  known_hosts
[root@zhangyz .ssh]# cat id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAxort6bDWAv/peF/g5fkXrUBiP5SLAbqANLSIyOef9DhCm6YsmOZH7N3rMg5sJiXev/DLfJNZNwZF0iPb1G63Mv9IKm9kkeAQP38ZiaheeXreJNy6CTqvRApQn7KR16t6IlD5pMIPAGivvf06k7G7J/veBR5wTuKesVjiVo2ELathXe40Kh33KBL/fHNOsU1xwCdamkbguO3IvflpirVKveywbDyyUpp7OqvnspKCo9LN2u0qwXsQhjzw3O4I42ell+hb3+Xb8uaUh7QENnCybKebzAahb7PCE4PIvHddssqOJ1IscQ4ioaKP3L1fXd4Os3HRVHmp24G9ufLsPceSnw== root@zhangyz
```

将server端生成的公钥复制到client端的.ssh/ 文件夹当中并且创建一个叫 authorized_keys 的文件将server端公钥的内容拷贝进去

client端
```shell
[root@web ~]# cd /root/.ssh/
[root@web .ssh]# ls
id_rsa  id_rsa.pub  known_hosts
[root@web .ssh]# vim authorized_keys
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAxort6bDWAv/peF/g5fkXrUBiP5SLAbqANLSIyOef9DhCm6YsmOZH7N3rMg5sJiXev/DLfJNZNwZF0iPb1G63Mv9IKm9kkeAQP38ZiaheeXreJNy6CTqvRApQn7KR16t6IlD5pMIPAGivvf06k7G7J/veBR5wTuKesVjiVo2ELathXe40Kh33KBL/fHNOsU1xwCdamkbguO3IvflpirVKveywbDyyUpp7OqvnspKCo9LN2u0qwXsQhjzw3O4I42ell+hb3+Xb8uaUh7QENnCybKebzAahb7PCE4PIvHddssqOJ1IscQ4ioaKP3L1fXd4Os3HRVHmp24G9ufLsPceSnw== root@zhangyz
[root@web .ssh]# 
```

测试结果是否成功
```shell
[root@zhangyz ~]# ssh root@10.203.206.234
Last login: Wed May  9 19:30:29 2018 from 10.203.206.200
[root@web ~]# 
```

发现登陆成功了,这就是不要密码登陆的实现方式

<br>

这是一种方法当然还有另外一种方法可以将server端主机的密钥发送给client端实现无密码进行登陆

server端
```shell
[root@zhangyz ~]# ssh-copy-id -i root@10.203.206.234
[root@zhangyz ~]# ssh-copy-id -i root@10.203.206.234
Now try logging into the machine, with "ssh 'root@10.203.206.234'", and check in:

  .ssh/authorized_keys

  to make sure we haven't added extra keys that you weren't expecting.
[root@zhangyz ~]# 
```

测试结果是否成功

```shell
[root@zhangyz ~]# ssh root@10.203.206.234
Last login: Wed May  9 19:30:29 2018 from 10.203.206.200
[root@web ~]# 
```

<br>

使用这种方法一样也可以实现密钥认证自动登陆

当然ssh在安装package的时候会自动帮忙安装一个叫做scp的命令,这个命令可以用来实现远程文件拷贝功能

<br>

#### scp可以在两台主机之间复制数据
> ###### -r: 递归复制整个目录  
-a

#### ssh命令同样也可以对远程对目标主机发送要执行的命令,命令执行的结果会返回到当前的终端上面来

example: ssh username@host 'command'

```shell
[root@zhangyz ~]# ssh hadoop@192.168.1.1 "ls -l"
total 76
drwxr-xr-x  14 root   root   4096 Oct 31  2017 apache2
drwxr-xr-x.  2 root   root   4096 Oct 18  2017 bin
drwxr-xr-x   6 root   root   4096 Sep  1  2017 cmake
drwxr-xr-x.  2 root   root   4096 Sep 23  2011 etc
drwxr-xr-x.  2 root   root   4096 Sep 23  2011 games
drwxr-xr-x.  2 root   root   4096 Sep 23  2011 include
drwxr-xr-x.  7 root   root   4096 Nov 16 11:00 lib
drwxr-xr-x.  2 root   root   4096 Sep 23  2011 lib64
drwxr-xr-x.  2 root   root   4096 Sep 23  2011 libexec
drwxr-xr-x  17 root   root   4096 Jan  8 18:36 mysql
drwxr-xr-x  17 root   root   4096 Oct 11  2017 mysql1
drwxr-xr-x  13 root   root   4096 Sep 13  2017 nginx
drwxr-xr-x  10 root   root   4096 Sep  1  2017 php5
drwxr-xr-x   6 root   root   4096 Nov  2  2017 python34
drwxr-xr-x   6 root   root   4096 Nov  2  2017 python35
drwxr-xr-x.  2 root   root   4096 Sep 23  2011 sbin
drwxr-xr-x.  5 root   root   4096 Jun 29  2017 share
drwxr-xr-x.  2 root   root   4096 Sep 23  2011 src
drwxr-xr-x   7 zabbix zabbix 4096 Sep  1  2017 zabbix
```

#### ssh-keygen的多种用法介绍
```shell
~/.ssh/id_rsa
~/.ssh/id_rsa.pub
```
> ###### -t: 指定密钥的类型 {rsa/dsa}
> ###### -f: 指定密钥文件的路径 /path/to/keyfile
> ###### -P '': 为当前的密钥设置密码'password'
> ###### -N: 为当前的密钥设置密码'password'

```shell
Last login: Wed May  9 21:37:51 2018 from 10.203.206.250
[root@zhangyz ~]# ssh-keygen -t rsa -f /root/.ssh/id_rsa -P 'redhat'
Generating public/private rsa key pair.
/root/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
f6:9c:ce:dd:f0:a5:b3:80:9a:cd:55:a8:2b:7e:2a:90 root@bdn
The key's randomart image is:
+--[ RSA 2048]----+
|                 |
|                 |
|                 |
|             .   |
|     .  S   . .  |
|    E  . o + .   |
|     .    * +   .|
|      . .B.+ =.o |
|       o=+* . =o |
+-----------------+
[root@zhangyz ~]# 
[root@zhangyz ~]# ssh-keygen -t rsa -f /root/.ssh/id_rsa -N 'cisco'
Generating public/private rsa key pair.
/root/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
6a:ef:64:81:aa:b0:62:45:28:5f:b3:bb:20:c4:1a:7d root@bdn
The key's randomart image is:
+--[ RSA 2048]----+
|                 |
|                 |
|  .              |
|oo .o  .         |
|o+o.Eo. S        |
|o..o.. . .       |
|+ o ..o o        |
|.= o.. +         |
|+ . .. .o        |
+-----------------+
[root@zhangyz ~]# 
[root@zhangyz ~]# 
[root@zhangyz ~]# ssh-copy-id -i root@10.203.206.235
The authenticity of host '10.203.206.235 (10.203.206.235)' can't be established.
RSA key fingerprint is 84:19:18:b3:c7:a9:a8:2d:37:8a:08:e4:62:44:36:f0.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '10.203.206.235' (RSA) to the list of known hosts.
root@10.203.206.235's password: 
Now try logging into the machine, with "ssh 'root@10.203.206.235'", and check in:

 .ssh/authorized_keys

 to make sure we haven't added extra keys that you weren't expecting.

[root@zhangyz ~]# ssh root@10.203.206.235
Enter passphrase for key '/root/.ssh/id_rsa': 
Enter passphrase for key '/root/.ssh/id_rsa': 
Last login: Mon May 14 15:57:58 2018 from 10.203.206.200
[root@web ~]# 
```


sftp

总结
1.密码应该经常换，且足够复杂
2.使用非默认的端口
3.限制登陆客户地址
4.禁止管理直接登陆
5.仅允许有限制的用户登陆
6.仅用基于密钥的认证
7.禁止使用版本1


嵌入式系统专用的ssh软件  dropbear

<br>

## 端口转发

#### SSH端口转发技术
在ssh加密的通道上传输其它协议数据, 即其它协议的数据通过ssh端口进行转发, ssh连接作为其它协议传输的通道(隧道), 这种方式也叫做ssh端口转发或ssh隧道

#### SSH端口转发技术的作用
加密数据传输
突破防火墙限制

#### SSH端口转发的分类
本地转发(local forwarding)
远程转发(remote forwarding)
动态转发(dynamic forwarding)


#### SSH本地端口转发
本地转发中的本地是指将本地的某个端口转发到其他主机的某个端口, 这样当我们的程序连接本地的这个端口时, 其实间接连上了其它主机的某个端口, 当我们发数据包到这个端口时数据包就自动转发到了那个远程端口上

-L [bind_address:]port:host:hostport
-N: 不执行命令, 和-f配合使用
-f: 在后台执行, 不用登陆到远程主机

###### ssh -L [bind_address:]port:host:hostport server_address

```shell
[root@zhangyz ~]# ssh -Nf -L 10.10.10.1:7788:10.10.10.2:80 10.10.10.3
root@10.10.10.3's password:
```
