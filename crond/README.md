#### 任务计划
  * ###### 指定的时间点执行一次特定的任务 at/batch
  * ###### 周期性的执行某个任务 crond：服务

<br>

<br>

```shell
at TIME
at>

ctrl+d

at 12：00
at有队列，每个队列用单独的英文字母
```

<br>

<br>

#### at 命令使用
  * ###### -q：指定队列
  * ###### -l：查看等待执行的任务相当于atq
  * ###### -d：删除某等待执行的任务，相当于atrm

```shell
at -f /path/to/file  TIME
```

<br>

<br>

###### 很多时候我们将系统执行的任务写成了脚本但是我们想让它们在特定的时间进行执行于是就需要我们使用任务计划来实现此需求

> ###### 1.在未来的某个时间点执行一次某任务
```shell
at
batch

at：时间
at>COMMAND
at>Ctrl+d

指定时间：
绝对时间：HH:MM      DD.MM.YY        MM/DD/YY        

相对时间：now+#
        单位：minutes，hours，days，weeks
模糊时间：noon，midnight，teatime

命令的执行 ，将 以邮件的形式发送给安排任务的用户      
        
* 10： 20结果
* now+3           表示从这一刻开始往后的3分钟以后
      
at now+3minutes            3分钟以后执行     
        
at -l                               可以显示自己的作业列表
                                   
at -l = atq

at -d                               表示删除任务
atrm                           同上

at 时间
at> COMMAND
at> Ctrl+d

指定时间：
10： 20
now+3m
绝对时间：   HH:MM       DD,MM,YY        MM/DD/YY
相对时间：   now+#
单位：  minutes，hours，days，weeks
模糊时间：   noon，midnight，teatime

命令的执行结果：    将以邮件的形式发送给安排任务的用户     

at -l                
```

<br>

<br>
      
> ###### 2.周期性地执行某任务
  * ###### 以服务的方式来进行监控
  * ###### cron：自身是一个不间断运行的服务
  * ###### anacron：cron的补充，能够实现让cron因为各种原因在过去的时间点该执行的但是却没有执行的服务，在恢复正常后执行一次
  * ###### cron的环境变量：cron执行所有命都去PATH环境变量指定的路径下去找 PATH /bin /sbin  

```shell    
cron：
    系统cron任务:
        /etc/crontab                
        分钟   小时    天   月   周     用户     任务                
    用户cron任务:
         /var/spool/cron/USERNAME
        分钟   小时    天   月   周     用户     任务   
        
    时间的有效取值：
        分钟：0 - 59
        小时：0 - 23
        天： 1 -31
        月： 1-12
        周： 0 - 7,0和7都表示周日
    
    时间统配表示：
        *：对应的所有有效取值

        3  *　*　*　*
        3  *    *     *    7
        13  12  *   *   5
        ,：离散时间点
        10,40 * * * *  2,5             表示半小时任务
        —：表示连续时间点
        10 02 * * 1-5
        /：对应取值范围内每多久一次
        */3 * * * *                    每3分钟一次的任务
                            
                                 
	每2小时执行一次：
    	08 */2 * * *                   表示每2小时执行一次
    	10 04 */2 * *                  每2天执行一次
        
```

<br>

<br>
        
#### 执行结果将以邮件形式发送给管理员：
  > ###### */3 * * * *  cat /etc/fstab &> /dev/null

<br>

<br>

#### 用户任务的管理 crontab
  * ###### -l： 列出当前用户的所有任务
  * ###### -e：编辑
  * ###### -r：移除所有任务
  * ###### -u：USERNAME   管理其它用户的cron任务

```shell                              
crontab -e
*/3 * * * *  /bin/echo "how are you?"
```       

<br>

<br>

> ###### 系统cron -- /etc/crontab 每一行用于定义一个任务，7个字段 1 1 1 * * root /bin/echo  "hello world" 分钟：0-59

> ###### 用户cron -- /var/spool/cron/USERNAME

<br>

<br>

#### 时间点的通配符
  * ###### *：表示全部有效值
  * ###### 10 2 * * *

<br>

<br>

#### crontab 
  * ###### -l: 列出所有的任务
  * ###### -e: 编辑
  * ###### -r: 移除所有任务
  * ###### -u: 用户名

<br>

<br>

> ###### crontab -u hadoop -e    // 为hadoop用户编辑任务

<br>

<br>

```shell
，：离散时间点
10,40 02 * * 2,5
-： 连续时间点
10 02 * * 1-5
/#：对应取值范围内每多久一次
*/3 * * * *

每两小时执行一次
08 */2 * * *
每两天执行一次
10 04 */2 * *

执行结果将以邮件形式发送给管理员：
*/3 * * * * /bin/cat  /etc/fstab

脚本如果放在cron当中，一定要加上环境变量去执行        
#!/bin/bash
export PATH=$PATH

vim /etc/crontab

```

<br>

<br>

#### 用户任务的管理: crontab
  * ###### -l: 列出当前用户的所有cron 任务
  * ###### -e: 编辑
  * ###### -r: 移除所有的任务 

```shell
root@ansible:~# crontab -e  # 默认用户是root
02 */2 * * * /bin/cat /etc/fstab

编辑完成之后，会在 /var/spool/cron 目录下生成一个以编辑用户所生成的文件
root@ansible:~# ls /var/spool/cron/
root

root@ansible:~# crontab -u hadoop -e 
```
