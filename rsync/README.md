rsync 是一个 Unix 系统下的文件同步和传输工具

<br>
#### rsync 工作流程

###### 1.rsync 服务器会使用xinetd服务方式开启873端口监听,等待rsync客户端连接
###### 2.rsync 客户端发起连接
###### 3.rsync 服务器检查rsync客户端提交rsync服务器内建的户名和密码是否正确,通过认证检测,则开始文件传输
###### 4.rsync 传输的过程是按要求先比对文件的大小,属性,权限,MD5值等信息,如果两端文件信息不一致,则按要求同步文件的区别块




<br>

```shell
++++++++++++++++                +++++++++++++++
+rsync Client  +     <====      +rsync Server +
+/mnt          +     ====>      +   /test     +       
++++++++++++++++                +++++++++++++++
192.168.122.10/24               192.168.122.20/24
```

<br>

### 配置 rsync server
```shell
[root@localhost ~]# yum install rsync
[root@localhost ~]# chkconfig rsync on
[root@localhost ~]# vim /etc/rsyncd.conf
uid=nobody	# uid和gid是系统内建用户,必须存在于系统/etc/passwd 文件中
gid=nobody
use chroot=no	# 如果使用chroot,在传输文件之前,服务器守护程序在将chroot到文件系统中的目录中,这样做的好处是保护系统被安装漏洞侵袭的可能
max connections=4	# 客户端同时连接数,为了限制多线程同步的
pid file=/var/run/rsyncd.pid
lock file=/var/run/rsync.lock
log file=/var/log/rsyncd.log
motd file=/etc/rsyncd.motd	# 自定义服务器信息的,要自己写 rsyncd.motd 文件内容，Client访问Server时，会在Client端显示文件内容
hosts allow=127.0.0.1 192.168.122.0/255.255.255.0
auth users=BackupUser	# 没必要使用服务器上存在的真实的系统用户,它们是rsync服务端内建的用户,如果你想用多个用户,那就以","号隔开
secrets file=/etc/rsyncd.secrets	 # 密码文件 /etc/rsyncd.secrets 需要创建这个文件

[test]	# 设置备份目录区域,配置项到下一个 [] 组合截止
path=/test	# 定义其在系统中的真实路径
comment=ServerTest	# 显示说明
read only=no	# 访问方式   
list=yes	# rsync 客户端是否可以列出目录结构
```

```shell
[root@localhost ~]# vim /etc/rsyncd.motd
Hello !
Welcome to my host
This is a rsyncserver for Client
[root@localhost ~]# grub-md5-crypt
Password: 
Retype password: 
$1$3ZBCx0$oTKU2qYUd26cvk3b/aHv31
[root@localhost ~]# vim /etc/rsyncd.secrets 
# 服务器端密码文件 /etc/rsyncd.secrets 格式为： username:password
BackupUser:$1$3ZBCx0$oTKU2qYUd26cvk3b/aHv31 # 注：密码可使用grub-md5-crypt生成
[root@localhost ~]# chmod 600 /etc/rsyncd.*
[root@localhost ~]# service xinetd restart
[root@localhost ~]# netstat -tunpl | grep 873
[root@localhost ~]# mkdir /test
[root@localhost ~]# touch /test/file1
[root@localhost ~]# touch /test/file2
[root@localhost ~]# echo 123 > /test/file1
[root@localhost ~]# chmod 777 /test -R
```

<br>

### 配置 rsync Client
```shell
[root@localhost ~]# vim /root/secrets
# 客户端密码文件 /root/secrets 格式为：password 
$1$3ZBCx0$oTKU2qYUd26cvk3b/aHv31
[root@localhost ~]# chmod 600 /root/secrets
```

客户端同步文件
```shell
[root@localhost ~]# rsync -av BackupUser@192.168.122.20::test /mnt --password-file=/root/secrets
Hello !
Welcome to my host
This is a rsyncserver for Client
receiving file list ... done
./
file1
file2
sent 154 bytes received 272 bytes 284.00 bytes/sec
total size is 4 speedup is 0.01
```

在服务器上修改file1, Client再同步
```shell
[root@localhost ~]# rsync -Rav BackupUser@192.168.122.20::test /mnt -password-file=/root/secrets
This is a rsyncserver for Client
receiving file list ... done
file1
sent 132 bytes received 227 bytes 239.33 bytes/sec
total size is 7 speedup is 0.02
```

客户端也能将自己指定目录上面的文件上传到目标服务器端上卖的目录
```shell
[root@localhost ~]# rsync -avH --port=873 --progress /web/site01/ BackupUser@192.168.1.234::test --password-file=/root/secrets
......
```

在服务端上面去查看指定的目录会看到客户端已经将文件传递过来了
```shell
[root@localhost ~]# ls /test -l
total 52
drwxrwxrwx 5 nobody nobody 4096 Sep 12  2016 Application
-rwxrwxrwx 1 nobody nobody  377 May 25  2016 composer.json
-rwxrwxrwx 1 nobody nobody 9662 Mar 17  2017 favicon.ico
-rwxrwxrwx 1 root   root      0 Jul  5 16:44 file
-rwxrwxrwx 1 root   root      4 Jul  5 16:44 file1
-rwxrwxrwx 1 root   root      0 Jul  5 16:44 file2
-rwxrwxrwx 1 nobody nobody  369 Mar 15  2017 index.php
-rwxrwxrwx 1 nobody nobody  756 Sep  6  2016 ipn.log
drwxr-xr-x 2 nobody nobody 4096 Jul  5 16:44 mnt
-rwxrwxrwx 1 nobody nobody   45 Mar 15  2017 newdanye0315.tar.gz
drwxrwxrwx 7 nobody nobody 4096 Sep 12  2016 Public
-rwxrwxrwx 1 nobody nobody    2 Jan  2  2018 robots.txt
drwxrwxrwx 8 nobody nobody 4096 Jul 28  2017 ThinkPHP
```

rsync也可以当成scp,不用搭建rsync服务只要ssh配置好无密码登录直接使用以下命令就可以把目标文件内容同步到本地
```shell
[root@zhangyz ~]# rsync -av root@10.203.206.234:/etc /tmp
receiving incremental file list
etc/
etc/.pwd.lock
etc/DIR_COLORS
etc/DIR_COLORS.256color
etc/DIR_COLORS.lightbgcolor
......
```
