## 文件系统都存放在内核空间当中

nfs网络文件系统 (network file system)

nfs服务器是借助于rpc所提供的服务器必须借助于网络工作才可以叫做nfs

NFS：一般不建议在互联网上使用

nfs在内网环境部署非常方便只能工作在unix和linux之间nfs类似于windows上的网上邻居

服务器端: nfs-utils

portmap在tcp的111和udp的111端口上

nfsd: 2049/tcp 2049/udp
mountd：端口
quotad：端口
半随机的端口


在nfs当中有两个进程同时写一个文件怎么办, 这个时候文件会把两者写的内容保存吗？

答: 文件一定会崩溃解决办法向nfs申请加锁 (分布式文件锁)        

定义nfs的共享文件目录                
/etc/exports        
/path/to/somedir    CLIENT_LIST

多个客户之间使用空白字符分割每个客户端后面必须跟一个小括号, 里面定义了此客户访问特性如访问权限等

172.16.0.0/16 (ro, async)  
192.16.0.0/24 (rw, sync)

showmount -e NFS_SERVER

mount -t nfs NFS_SERVER:/PATH/TO/SOME_EXPORT  /PATH/TO/SOMEWHERE

showmount -a NFS_SERVER     查看nfs 服务器被挂载的所有文件系统

showmount -d NFS_SERVER       用于显示nfs 服务器上所有的导出的被挂载的文件系统

exportfs 命令
-a： 跟-r 或 -u 选项同时使用，表示重新挂载所有文件系统或取消导出所有文件系统
-r：重新导出
-u：取消导出
-v：显示详细信息



文件系统导出属性
ro
rw
sync
async
root_squash  将root 用户映射为来宾账号
no_root_squash
anonuid， anongid  指定映射的来宾账号的UID和GID


让mountd 和 quotad 等进程监听在固定端口，请编辑配置文件 /etc/sysconfig/nfs
