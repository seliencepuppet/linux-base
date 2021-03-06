awk: 报告生成器

awk a k a. Aho. Kernighan and Weinberger

```shell
awk [options] 'script' file1, file2 ...
```

```shell
awk [options] 'pattern' '{action}' file1,file2,.....
```

print,printf

awk的基本处理机制        

awk一次从文件抽取出来一行文本按照字符串的文本进行切割

默认使用空白字符当做分隔符

```shell
[root@zhangyz ~]# echo "this is a test" > a.txt
```

在awk当中$0表示一整行文本


awk中也可以指定一个额外字符当做分隔符
* -F: (以冒号作为分隔符) 指定额外的其他分隔符
* OFS: 表示输出分隔符
```shell
[root@zhangyz ~]# awk 'BEGIN{OFS="#"}{print $1,$2}' test.tc
```
```shell
[root@zhangyz ~]# awk 'BEGIN{OFS=":"}{print $1,$2,$3,$4}' test.tc
```

awk的输出 print
print的使用格式
print item1，item2
```shell
[root@zhangyz ~]# awk 'BEGIN{OFS=":"}{print $1,"hello",$2,$3,$4}' test.tc 
```
```shell
[root@zhangyz ~]# awk 'BEGIN{ print "line one\nline two\nline three" }'
line one
line two
line three
```

要点
1) 各项目之间要使用逗号隔开而输出时则以空白字符分隔
2) 输出的item可以为字符串或数值当前记录的字段(如$1), 变量或awk的表达式, 数值会转化为字符串在输出
3) print命令后面的item可以省略, 此外其功能相当于print $0, 因此, 如果想输出空白行, 则需要使用 print ""
```shell
[root@zhangyz ~]# awk 'BEGIN{test="hello awk"; print test}'
```

```shell
[root@zhangyz ~]# awk 'BEGIN{ print "line one\nline two\nline three" }'
```

```shell
[root@zhangyz ~]# awk -F ':' '{print $1,$2}' /etc/passwd
```

awk变量之记录变量

* FS (field separator) 默认是空白字符读取文本时所使用字段分隔符 
```shell
FS=":" 表示输入分隔符
```
* RS (Record separator) 默认是换行符输入文本信息所使用的换行符
* OFS (output filed separator) 输出字段分隔符
```shell
OFS="#" 表示输出分隔符
```
* ORS (output row separator) 输出行分隔符

awk内置变量之数据变量
* NR (The number of input records) awk命令所处理的记录数如果有多个文件,这个项目会把行数统一计数
* NF (Number of filed) 当前记录的filed个数
* FNR (与NR不同的是) FNR用于记录正处理的行是当前这一文件中被总共处理的行数
* AROV 数组保存命令行本身这个字符串如 awk '{print $0}' a.txt b.txt 这个命令中
* AROV[0] 保存awk, ARGV[1]保存a.txt
* ARGC awk命令的参数的个数
* FILENAME awk命令所处理的文件的名称
* ENVIROM 当前shell环境变量及其值的关联数组

```shell
awk '{print NF}' test.tc            输出最后一个字段数的数字
awk '{print $NF}' test.tc           输出最后一个字段
awk '{print NR}' test.tc
```

printf要点
1) 其与print命令的最大不同是 printf需要制定format
2) format用于制定后面的每个item的输出格式
3) printf语句不会自动打印换行符: \n

format格式的指示符都已%开头后跟一个字符如下
* %c: 显示字符的ascll码
* %i: 十进制整数
* %e, %E, 科学计数法显示数值
* %f: 显示浮点数
* %g, %G: 以科学计数法显示数值
* %f: 显示浮点数
* %g, %G: 以科学计数法的格式或浮点数的格式显示数值
* %s: 显示字符串
* %u: 无符号整数
* %%: 显示%自身

```shell
awk '{printf "%10s\n", $1}'   test.txt
```

```shell
awk '{printf "%s\n", $1}'    test.txt
```

```shell
awk '{printf "%10s\n", $1}'   test.txt
```

```shell
awk '{printf  "%-10s%-10n", $1,%3}'     test.txt
```


修饰符：
N: 显示宽度
-: 左对齐
+: 显示数值符号


awk当中用户自定义变量
```shell
[root@zhangyz ~]# awk -v test="hello awk" 'BEGIN{print test}'
hello awk
```

```shell
[root@zhangyz ~]# awk 'BEGIN{test="hello awk";print test}'
hello awk
```

awk算数操作符
```shell
-x: 负值
+x: 转换为数值
x^y: 次方
x*y: 乘法
x/y: 除法
x-y: 减法
x%y: 取模
```

awk字符串操作符
* 只有一个而且不用写出来用于实现字符串连接

awk赋值操作符
```shell
=
+=
-=
*=
/=
%=
^=
**=
++
--
```

布尔值awk中任何非0值或非空字符串都为真反之就为假
```shell
x < y
x <= y
x > y
x >= y
x == y
x ! = y
x ~ y
s !~ y
```

subscript in array          

表达式间的逻辑关系符 && ||

条件表达式
```shell
selector ? if-true-exp; if-false-exp
```

```shell
if selector; then
    if-true-exp
else
    if-true-exp
fi
```

a=3
b=4
a>b?


常见的模型类型
1) regexp 正则表达式格式为/regular expression/
2) expression 表达式其值非0或为非空字符时满足条件如: $1 ~ /foo/ 或 $1 == "magedu", 用运算符 ~(匹配)和 ~!(不匹配)
3) Range 指定的匹配范围格式为 pat1, pat2
4) BEGIN/END 特殊模式, 仅在awk命令执行前运行一次或结束前运行一次
5) Empty(空模式) 匹配任意输入行

常见的Action
1) Expressions
2) Control statements
3) Compound statements
4) Input statements
5) Output statements


/正则表达式/: 使用通配符的扩展集

关系表达式: 可以用下面运算符表中的关系运算符进行操作, 可以是字符串或数字的比较如 $2 > $1 选择第二个字段比第一个字段长的行

模式匹配表达式

模式指定一个行的范围该语法不能包括 BEGIN 和 END 模式
* BEGIN 让用户指定在第一条输入记录被处理之前所发生的动作通常可以在这里设置全局变量
* END 让用户在最后一条输入记录被读取之后发生的动作

函数调用
function_name(para1,para2)

只显示以r开头的行
```shell
[root@zhangyz ~]# awk -F ":" '/^r/{print $1}' /etc/passwd
root
rpc
rtkit
rpcuser
redis
```

显示id号大于500的用户
```shell
[root@zhangyz ~]# awk -F ":" '/$3 >= 500/{print $1 $3}' /etc/passwd
```

```shell
awk -F '$3+1>=500{print $1 $3}'  /etc/passwd
```

```shell
[root@zhangyz ~]# awk -F ":" '$7~"bash$"{print $1,$7}' /etc/passwd
root /bin/bash
zhangyz /bin/bash
mysql /bin/bash
subversion /bin/bash
nginx /bin/bash
zabbix /bin/bash
git /bin/bash
YumWEi17 /bin/bash
jxy /bin/bash
```

```shell
awk -F ":" '$7 !~"bash$"{print $1,$7}' /etc/passwd
bin /sbin/nologin
daemon /sbin/nologin
adm /sbin/nologin
lp /sbin/nologin
sync /bin/sync
shutdown /sbin/shutdown
halt /sbin/halt
mail /sbin/nologin
uucp /sbin/nologin
operator /sbin/nologin
games /sbin/nologin
gopher /sbin/nologin
ftp /sbin/nologin
nobody /sbin/nologin
dbus /sbin/nologin
usbmuxd /sbin/nologin
rpc /sbin/nologin
rtkit /sbin/nologin
avahi-autoipd /sbin/nologin
vcsa /sbin/nologin
abrt /sbin/nologin
rpcuser /sbin/nologin
nfsnobody /sbin/nologin
haldaemon /sbin/nologin
ntp /sbin/nologin
apache /sbin/nologin
saslauth /sbin/nologin
postfix /sbin/nologin
gdm /sbin/nologin
pulse /sbin/nologin
sshd /sbin/nologin
tcpdump /sbin/nologin
dhcpd /sbin/nologin
redis /sbin/nologin
dockerroot /sbin/nologin
```

指定匹配的范围
1) 以第一个r开头的行到第一个以m开头的行
```shell
[root@zhangyz ~]# awk -F ":" '/^r/,/^m/{print $1,$7}' /etc/passwd
root /bin/bash
bin /sbin/nologin
daemon /sbin/nologin
adm /sbin/nologin
lp /sbin/nologin
sync /bin/sync
shutdown /sbin/shutdown
halt /sbin/halt
mail /sbin/nologin
rpc /sbin/nologin
rtkit /sbin/nologin
avahi-autoipd /sbin/nologin
vcsa /sbin/nologin
abrt /sbin/nologin
rpcuser /sbin/nologin
nfsnobody /sbin/nologin
haldaemon /sbin/nologin
ntp /sbin/nologin
apache /sbin/nologin
saslauth /sbin/nologin
postfix /sbin/nologin
gdm /sbin/nologin
pulse /sbin/nologin
sshd /sbin/nologin
tcpdump /sbin/nologin
zhangyz /bin/bash
mysql /bin/bash
redis /sbin/nologin
git /bin/bash
YumWEi17 /bin/bash
jxy /bin/bash
dockerroot /sbin/nologin
```

```shell
[root@zhangyz ~]# awk -F ":" 'BEGIN{print "Username ID Shell"}{printf "%-10s%-10s%-20s\n", $1, $3, $7}' /etc/passwd
Username ID Shell
root      0         /bin/bash           
bin       1         /sbin/nologin       
daemon    2         /sbin/nologin       
adm       3         /sbin/nologin       
lp        4         /sbin/nologin       
sync      5         /bin/sync           
shutdown  6         /sbin/shutdown      
halt      7         /sbin/halt          
mail      8         /sbin/nologin       
uucp      10        /sbin/nologin       
operator  11        /sbin/nologin       
games     12        /sbin/nologin       
gopher    13        /sbin/nologin       
ftp       14        /sbin/nologin       
nobody    99        /sbin/nologin       
dbus      81        /sbin/nologin       
usbmuxd   113       /sbin/nologin       
rpc       32        /sbin/nologin       
rtkit     499       /sbin/nologin       
avahi-autoipd170       /sbin/nologin       
vcsa      69        /sbin/nologin       
abrt      173       /sbin/nologin       
rpcuser   29        /sbin/nologin       
nfsnobody 65534     /sbin/nologin       
haldaemon 68        /sbin/nologin       
ntp       38        /sbin/nologin       
apache    48        /sbin/nologin       
saslauth  498       /sbin/nologin       
postfix   89        /sbin/nologin       
gdm       42        /sbin/nologin       
pulse     497       /sbin/nologin       
sshd      74        /sbin/nologin       
tcpdump   72        /sbin/nologin       
zhangyz   500       /bin/bash           
mysql     27        /bin/bash           
dhcpd     177       /sbin/nologin       
subversion496       /bin/bash           
nginx     502       /bin/bash           
zabbix    503       /bin/bash           
redis     495       /sbin/nologin       
git       494       /bin/bash           
YumWEi17  504       /bin/bash           
jxy       505       /bin/bash           
dockerroot493       /sbin/nologin       
```

参数说明：
* -F re 允许awk更改其字段分隔符
* -v var=$v 把v值赋值给var如果有多个变量要赋值那么就写多个 -v 每个变量赋值对应一个 -v
* e.g. 要打印文件a的第num行到num+num1行之间的行， 
* awk -v num=$num -v num1=$num1 'NR==num,NR==num+num1{print}' a 
* -f progfile 允许awk调用并执行progfile程序文件当然progfile必须是一个符合awk语法的程序文件


awk的输出重定向类似于shell的重定向的目标文件名必须用双引号引用起来
```shell
[root@zhangyz ~]# awk -F ":" '$4>=70 {print $1,$7 > "/root/.bash/zhang.txt"}' /etc/passwd
```
```shell
[root@zhangyz ~]# awk -F: '$4>=70 {print $1,$7 >> "/root/.bash/zhang.txt"}' /etc/passwd
```

```shell
| 点击这里 | 点击这里 |
| ARGC | 命令行参数的各个，即ARGV数组的长度 |
| ARGV | 存放命令行参数 |
| CONVFMT | 定义awk内部数值转换成字符串的格式，默认值为"%.6g" |
| OFMT | 定义输出时数值转换成字符串的格式，默认值为"%.6g" |
| ENVIRON | 存放系统环境变量的关联数组 |
| FILENAME | 当前被处理的文件名 |
| NR | 记录的总个数 |
| FNR | 当前文件中的记录的总个数 |
| FS | 字段分隔符，默认为空白 |
| NF | 每个记录中字段的个数 |
| RS | 记录的分隔符，默认为回车 |
| OFS | 输出时字段的分隔符，默认为空白 |
| ORS | 输出时记录的分隔符，默认为回车 |
| RLENGTH | 被match函数匹配的子串长度 |
| RSTART | 被match函数匹配的子串位于目标字符串的起始下标 |
```


1) ARGV与ARGC
* ARGV与ARGC的意思比较好理解就像C语言main(int argc, char **argv) ARGV数组的下标从0开始到ARGC-1 它存放的是命令行参数并且排除命令行选项(例如-v/-f)以及program部分, 因此事实上ARGV只是存储argument的部分即文件名(file)以及命令行变量赋值两部分的内容通过下面的例子可以大概了解ARGC与ARGV的用法：
```shell
[kodango@devops awk_temp]$  awk 'BEGIN {
> for (i = 0; i < ARGC; i++)
>     print ARGV[i]
> }' inventory-shipped BBS-list
```

awk inventory-shipped BBS-listARGV的用法不仅限于此它是可以修改的可以更改数组元素的值可以增加数组元素或者删除数组元素
```shell
awk '{ if(NR>19&&NR<31) print $0 }'  ett.txt
```

```shell
awk '{ if(NR==19) print $0 }'  ett.txt 
```

awk当中if
```shell
[root@zhangyz ~]# awk -F ":" '{
> if ($1=="root") 
>    print $1, "Admin"; 
> else 
>    print $1, "Common User";
> }' /etc/passwd
root Admin
bin Common User
daemon Common User
adm Common User
lp Common User
sync Common User
shutdown Common User
halt Common User
mail Common User
uucp Common User
operator Common User
games Common User
gopher Common User
ftp Common User
nobody Common User
dbus Common User
usbmuxd Common User
rpc Common User
rtkit Common User
avahi-autoipd Common User
vcsa Common User
abrt Common User
rpcuser Common User
nfsnobody Common User
haldaemon Common User
ntp Common User
apache Common User
saslauth Common User
postfix Common User
gdm Common User
pulse Common User
sshd Common User
tcpdump Common User
zhangyz Common User
mysql Common User
dhcpd Common User
subversion Common User
nginx Common User
zabbix Common User
redis Common User
git Common User
YumWEi17 Common User
jxy Common User
dockerroot Common User
```

统计/etc/passwd每个用户的shell次数
```shell
[root@zhangyz ~]# awk -F ":" '{shell[$NF]++}
> END{ 
> for(A in shell) {
>     print A, shell[A]
> }
> }' /etc/passwd
/sbin/shutdown 1
/bin/bash 9
/sbin/nologin 32
/sbin/halt 1
/bin/sync 1
```

统计tcp连接建立的状态
```shell
[root@zhangyz ~]# netstat -tan | awk '/^tcp/{STATE[$NF]++} END{for (S in STATE) {print S, STATE[S]}}'
TIME_WAIT 1
CLOSE_WAIT 2
ESTABLISHED 13
LISTEN 22
```

把客户端的IP地址作为下标
```shell
[root@zhangyz ~]# awk '{count[$1]++} END{for (IP in count) {printf "%-20s: %d\n", IP,count[IP]}}' access_log
101.226.79.232      : 3
61.151.226.67       : 15
101.226.79.239      : 1
120.204.200.82      : 3
61.151.178.164      : 1
61.151.178.165      : 1
101.226.93.191      : 16
61.151.178.166      : 1
180.163.220.95      : 4
180.163.220.96      : 3
180.163.220.97      : 4
101.226.93.198      : 1
221.177.252.24      : 11
101.227.131.207     : 1
101.227.131.220     : 14
101.227.143.150     : 1
101.199.121.223     : 33
180.163.220.124     : 3
61.151.225.155      : 1
180.163.220.125     : 2
180.163.220.126     : 4
117.185.117.61      : 1
58.32.9.146         : 1970
101.226.227.170     : 1
101.226.227.171     : 1
117.135.173.78      : 15
106.120.160.109     : 9
207.35.196.243      : 11
101.199.110.108     : 21
101.199.110.109     : 21
101.199.108.119     : 28
47.90.4.143         : 60195
221.177.253.24      : 11
101.226.86.154      : 2
180.163.28.123      : 1
61.151.226.151      : 1
220.181.132.195     : 26
61.151.226.156      : 1
180.163.220.41      : 1
180.163.220.42      : 2
171.13.14.134       : 1
180.163.220.45      : 3
180.163.220.46      : 3
101.226.79.223      : 1
180.163.220.47      : 2
180.163.220.60      : 1
180.163.220.61      : 1
221.177.252.30      : 22
101.199.110.112     : 27
69.70.101.221       : 11
61.151.225.189      : 1
58.64.185.54        : 1
61.151.226.167      : 2
220.181.132.200     : 21
61.151.226.62       : 1
61.151.225.242      : 3
61.151.226.63       : 1
101.226.113.53      : 1
```

awk 当中的控制语句的使用

if~else
语法: if(condition){then-body} else {[ else-body ]}
例子:
```shell
[root@zhangyz ~]# awk -F ":" '{
> if ($1=="root") 
>     print $1, "Admin"; 
> else 
>     print $1, "Common User"; 
> }' /etc/passwd
```

```shell
[root@zhangyz ~]# awk -F ":" '{
> if ($1=="root“) 
>     printf "%-15s:  %s\n", $1, "Admin";   
> else   
>     printf "%-15s:  %s\n", $1, "Common User";
> }' /etc/passwd
```

```shell
[root@zhangyz ~]# awk -F ":" -v sum=0 '{
> if ($3>500)
>     sum++
> } END { print sum }' /etc/passwd
```

while语句使用方法

```shell
while (condition){
    statement1;  
    statement2; 
    ...
}
```

for语法
```shell
for (variable assignment; condition; iteration process){ 
    statement1,statement2,...
}
```

```shell
[root@zhangyz ~]# awk -F ":" '{
> for(i=1; i<=3; i++)  
>     print $i
> }' /etc/passwd
```

for循环还可以用来遍历数组元素
```shell
[root@zhangyz ~]# awk -F ":" '$NF!~/^$/{BASH[$NF]++}END{
> for(A in BASH){
>     printf "%15s: %i\n", A,BASH[A]
> }
> }' /etc/passwd
 /sbin/shutdown: 1
      /bin/bash: 9
  /sbin/nologin: 32
     /sbin/halt: 1
      /bin/sync: 1
```

case语句
```shell
switch(expression){
    case VALUE or /REGEXP/:  
        statement1, statement2, ...
    default:
        statement1, ...
}
```

break和continue常用于循环或case语句中

next提前结束对本行文本的处理并接着处理下一行例如: 下面的命令将显示其ID号为奇数的用户
```shell
[root@zhangyz ~]# awk -F ":" '{
> if($3%2==0) 
>     next;
> print $1,$3;
> }' /etc/passwd
bin 1
adm 3
sync 5
halt 7
operator 11
gopher 13
nobody 99
dbus 81
usbmuxd 113
rtkit 499
vcsa 69
abrt 173
rpcuser 29
postfix 89
pulse 497
mysql 27
dhcpd 177
zabbix 503
redis 495
jxy 505
dockerroot 493
```

awk当中使用数组但是awk当中的数组下标是从1开始的并且可以使用任何字符串当做下标
netstat -tan |  awk '/^tcp/ {++S[$NF]} END{for {a in S} {print a, S[a]}}'

每出现一个被 /^tcp/ 模式匹配到的行，数组 S[$NF] 就加1， NF 为当前匹配到的行的最后一个字段，此处用其值作为数组s的元素索引

用法与上一个例子相同，用于统计某日志文件中IP地址的访问量
```shell
[root@zhangyz ~]# awk '{counts[$1]++}; END{for(url in counts) print counts[url], url}'  /var/log/httpd/access_log
1 84.210.149.8
1 151.243.6.198
1 89.232.34.178
1 81.218.130.234
1 31.184.194.109
8 119.23.91.163
1 119.23.91.164
4 119.23.91.165
3 119.23.91.166
1 119.23.91.167
4 119.23.91.168
4 119.23.91.169
5 59.62.120.31
1 185.97.122.252
1 120.78.231.236
2 127.0.0.1
1 101.226.79.225
2 183.60.228.168
1 157.55.39.50
1 183.60.228.169
1 101.254.149.133
1 41.32.219.2
2 122.226.77.230
1 103.26.212.229
3 106.15.218.3
2 106.15.218.4
7 106.15.218.5
11 106.15.218.9
1 179.178.74.95
1 139.162.88.63
1 13.228.6.247
1 31.135.166.157
```

```shell
[root@zhangyz ~]# awk '{count[$1]++}END{
> for (ip in count) {
>     printf "%-20s:%d\n", ip, count[ip]
> }
> }' /var/log/httpd/access_log
187.139.11.35       :1
140.205.132.22      :16
140.205.132.64      :1
140.205.132.23      :11
140.205.132.24      :5
140.205.132.65      :6
140.205.132.66      :5
140.205.132.25      :9
220.181.51.109      :1
140.205.132.67      :11
140.205.132.26      :2
140.205.132.28      :6
140.205.132.29      :7
106.75.92.187       :2
134.119.212.87      :2
```

删除数组变量从关系数组中删除数组索引需要使用delete 命令使用格式为
```shell
delete array[index]
```
