# Linux-命令
- 文件目录操作：pwd，cd，ls，file，cp，mv，mkdir，rm，ln
- 使用命令：type，help，man，apropos，info，whatis，alias
- 权限：id，chmod，umask，sudo，chown，chgrp，passwd，usermod，useradd，groupadd，userdel
- 进程管理：w，top，free，ps，kill，pkill，pstree，killall，jobs，bg，fg，shutdown
- 环境变量：set，printenv，export
- 存储媒介：mount，umount，fsck，fdisk，mkfs，dd，genisoimage，wodim，md5sum
- 网络系统：ifconfig，route，ping，netstat，ss，curl，telnet，traceroute，ftp，wget，ssh
- 文件系统：file，mount，umount，fsck，df，du，tar，gzip，scp，rsync
- 系统关机重启：shutdown，reboot
- 常用工具：ssh，clear，history，who，screen
- 文件查找：locate，find，whereis，which
- 文件内容查看：head，tail，less，more
- 文本处理：cat，sort，uniq，cut，paste，join，comm，diff，patch，tr
- 文本搜素：grep，sed，awk
- 格式化输出：nl，fold，fmt，pr，printf，groff
- 软件包管理：
- 编译程序：

## crontab
`crontab [-u user] file crontab [-u user] [ -e | -l | -r ]`

### crontab 的文件格式
分 时 日 月 周 要运行的命令

### 实例
`* * * * * myCommmand` 每分钟执行
`3,15 * * * * myCommand` 每小时的第3和第15分钟执行
`3,15 8-11 */2  *  * myCommand` 每隔两天的上午8点到11点的第3和第15分钟执行
`3,15 8-11 * * 1 myCommand` 每周一一上午8点到11点的第3和第15分钟执行
`* */1 * * * myCommand` 每一小时执行 

## 文件目录操作

### pwd 打印出当前工作目录名
- -P 当目录是链接时，显示出实际路径

### cd 切换目录

#### 参数
参数|描述
---|----
/ | 进入根目录
~ | 或者不填 进入当前用户主目录
- | 返回之前的目录

### ls 列出目录下的内容

#### 参数
参数|描述
---|----
-a | -all 列出目录下的所有文件，包括以 .  开头的隐藏文件
-d | 通常，如果指定了目录名，ls 命令会列出这个目录中的内容，而不是目录本身。 把这个选项与 -l 选项结合使用，可以看到所指定目录的详细信息，而不是目录中的内容。
-h | 显示文件详细信息时，以人们可读的格式，而不是以字节数来显示文件的大小。
-r | 以相反的顺序来显示结果。通常，ls 命令的输出结果按照字母升序排列
-i | 打印出每个文件后的 inode 号
-t | 以文件修改时间排序
-l | 显示文件详细信息
-S | 命令输出结果按照文件大小来排序。
-R | 同事列出所有子目录层

### file 确定文件类型
当调用 file 命令后，file 命令会打印出文件内容的简单描述

### 格式
`file filename`

### mkdir 创建文件夹

#### 参数
参数|描述
---|----
-m | --mode=模式，设定权限<模式> (类似 chmod)，而不是 rwxrwxrwx 减 umask
-p | --parents  可以是一个路径名称。此时若路径中的某些目录尚不存在,加上此选项后,系统将自动建立好那些尚不存在的目录,即一次可以建立多个目录; 
-v | --verbose  每次创建新目录都显示信息，--help 显示此帮助信息并退出，--version 输出版本信息并退出

### rm 删除文件或目录

#### 参数
参数|描述
---|----
-f --force | 忽略不存在的文件，不给出提示
-i --interactive | 进行交互式删除
-r -R --recursive | 递归删除子目录
-v --verbose | 详细显示进行的步骤，--help 显示此帮助信息并退出，--version 输出版本信息并退出

### rmdir 删除空目录

### mv 移动/重命名文件和目录 

#### 格式 
`mv [选项] 源文件或目录 目标文件或目录`

#### 参数
参数|描述
---|----
-b | 若需覆盖文件，则覆盖前先备份
-f | 强制，如果目标文件已经存在，不会询问是否覆盖
-i | 若目标文件已经存在，就会询问是否覆盖
-u | 若目标文件已经存在，且 source 比较新，才会更新(update)
-t | --target-directory=DIRECTORY move all SOURCE arguments into DIRECTORY，即指定mv的目标目录，该选项适用于移动多个源文件到一个目录的情况，此时目标目录在前，源文件在后。

### cp 复制文件或者目录

#### 格式
`cp [选项] [-t] 源 目的`

#### 参数
参数|描述
---|----
-a | 为每个已存在的目标文件创建备份
-f | 如果目标文件无法打开则将其移除并重试
-r | 复制目录及目录内的所有项目
-n | 不要覆盖已存在的文件

### touch 修改文件的时间戳，或者新建一个不存在的文件

#### 参数
参数|描述
---|----
-a | 改变档案的读取时间记录
-m | 改变档案的修改时间记录
-c | 加入母的档案不存在，不建立任何档案，与 --no-create 的效果一样
-d -t | 使用指定的日期时间，而非现在时间 touch -t 201911142234.50 log.log `[[CC]YY]MMDDhhmm[.SS] `   这里，CC为年数中的前两位，即”世纪数”；YY为年数的后两位，即某世纪中的年数．如果不给出CC的值，则touch   将把年数CCYY限定在1969--2068之内．MM为月数，DD为天将把年数CCYY限定在1969--2068之内．MM为月数，DD为天数，hh 为小时数(几点)，mm为分钟数，SS为秒数．此处秒的设定范围是0--61，这样可以处理闰秒．这些数字组成的时间是环境变量TZ指定的时区中的一个时 间．由于系统的限制，早于1970年1月1日的时间是错误的
-r | 使用参考文档的时间 touch -r log.log log2019.log

### xargs 
给命令传递参数的一个过滤器，一般与管道一起使用。
在使用 find命令的-exec选项处理匹配到的文件时， find命令将所有匹配到的文件一起传递给exec执行。但有些系统对能够传递给exec的命令长度有限制，这样在find命令运行几分钟之后，就会出现溢出错误。错误信息通常是“参数列太长”或“参数列溢出”。这就是xargs命令的用处所在，特别是与find命令一起使用。  

find命令把匹配到的文件传递给xargs命令，而xargs命令每次只获取一部分文件而不是全部，不像-exec选项那样。这样它可以先处理最先获取的一部分文件，然后是下一批，并如此继续下去。  

#### 实例
- find / -name "core" -print | xargs echo "" >/tmp/core.log 在整个系统中查找内存信息转储文件(core dump) ，然后把结果保存到/tmp/core.log 文件中
- find . -type f -print | xargs grep "hostname" 用grep命令在所有的普通文件中搜索hostname这个词

## 使用命令
命令可以是以下四种形式之一

1. 是一个可执行程序
1. 是一个内建于 shell 自身的命令
1. 是一个 shell 函数
1. 是一个命令别名

### type 显示命令的类型

#### 格式 
`type command`

### help 得到 shell 内建命令的帮助文档
bash 有一个内建的帮助工具，可供每一个 shell **内建命令** 使用。

### --help 显示用法信息
许多 **可执行程序** 支持一个 --help 选项，这个选项是显示命令所支持的语法和选项说明。

### man 显示程序手册页
许多希望被命令行使用的 **可执行程序**，提供了一个正式的文档，叫做手册或手册页(man page)。一个特殊的叫做 man 的分页程序，可用来浏览他们。

### apropos 显示适当的命令
也有可能搜索参考手册列表，基于某个关键字的匹配项。

输出结果每行的第一个字段是手册页的名字，第二个字段展示章节。注意，man 命令加上”-k”选项， 和 apropos 完成一样的功能。

### whatis 显示非常简洁的命令说明
whatis 程序显示匹配特定关键字的手册页的名字和一行命令说明：

### info 显示程序 Info 条目
GNU 项目提供了一个命令程序手册页的替代物，称为”info”。info 内容可通过 info 阅读器 程序读取。info 页是超级链接形式的，和网页很相似。

### alias 创建命令别名
- `alias` 查看别名
- `alias name='command'` 创建别名 
- `unalias name` 删除别名

## 重定向
I/O 重定向允许我们更改输出地点和输入来源。一般地，输出送到屏幕，输入来自键盘， 但是通过 I/O 重定向，我们可以做出改变,将标准输出重定向到除屏幕以外的另一个文件。

### > 标准输出重定向
`ls > output.txt` 结果输出到了 output.txt，使用‘>’重定向符，目标文件总是从头开始被重写，如果需要清空一个文件内容，可以`> output.txt`

### >> 追加输出结果到目标文件

### 2> 标准错误重定向
标准错误重定向没有专用的重定向操作符。为了重定向标准错误，我们必须参考其文件描述符。 一个程序可以在几个编号的文件流中的任一个上产生输出。虽然我们已经将这些文件流的前 三个称作标准输入、输出和错误，shell 内部分别将其称为文件描述符0、1和2。

### 重定向标准输出和错误到同一个文件
- 方法一：`ls -l /bin/usr > ls-output.txt 2>&1`使用这种方法，我们完成两个重定向。首先重定向标准输出到文件 ls-output.txt，然后 重定向文件描述符2（标准错误）到文件描述符1（标准输出）使用表示法2>&1。注意重定向的顺序安排非常重要。标准错误的重定向必须总是出现在标准输出 重定向之后，要不然它不起作用。
- 方法二：`ls -l /bin/usr &> ls-output.txt`

### 处理不需要的输出
系统提供了一个叫做”/dev/null”的特殊文件， 这个文件是系统设备，叫做位存储桶，它可以接受输入，并且对输入不做任何处理。`ls -l /bin/usr 2> /dev/null`

### 管道
命令从标准输入读取数据并输送到标准输出的能力被一个称为管道线的 shell 特性所利用。 使用管道操作符”|”（竖杠）
- `ls -l /usr/bin | less` 使用 less 来显示输出

### tee 从 Stdin 读取数据，并同时输出到 Stdout 和文件
Linux 提供了一个叫做 tee 的命令，这个命令制造了 一个”tee”，安装到我们的管道上。tee 程序从标准输入读入数据，并且同时复制数据 到标准输出（允许数据继续随着管道线流动）和一个或多个文件。当在某个中间处理 阶段来捕捉一个管道线的内容时很有用
- `ls /usr/bin | tee ls.txt | grep zip` 在 grep 过滤管道线的内容之前，来捕捉整个目录列表到文件 ls.txt

## 展开

### echo 它将它的文本参数打印到标准输出中
- `echo this is a test` 输出 this is a test

### 路径名展开
通配符所依赖的工作机制叫做路径名展开。
- `echo D*` 显示当前目录下以 D 开头的文件
- `echo /usr/*/share`
- 路径名展开不会显示隐藏文件，使用 `echo .*` 会显示出父目录，使用 `.[!.]?*` 可以展开成所有以圆点开头，第二个字符不包含圆点，再包含至少一个字符， 并且这个字符之后紧接着任意多个字符的文件名。

### 波浪线展开
当它用在 一个单词的开头时，它会展开成指定用户的家目录名，如果没有指定用户名，则展开成当前用户的家目录
- `echo ~` 显示 /home/me
- `echo ~foo` 显示 /home/foo

### 算术表达式展开
格式：$((expression))  只支持整数
- `echo $((2 + 2))` 

### 花括号展开
花括号展开模式可能包含一个开头部分叫做报头，一个结尾部分叫做附言。花括号表达式本身可 能包含一个由逗号分开的字符串列表，或者一个整数区间，或者单个的字符的区间。这种模式不能 嵌入空白字符。
- `echo Front-{A,B,C}-Back` Front-A-Back Front-B-Back Front-C-Back
- `echo {1..5}` 1 2 3 4 5
- `echo {Z..A}` Z Y X W V U T S R Q P O N M L K J I H G F E D C B A
- 嵌套 `echo a{A{1,2},B{3,4}}b` aA1b aA2b aB3b aB4b

### 参数展开
- `echo $USER` 输出用户名
- 可以用 `printenv | less` 来查看有效的变量名

### 命令替换
命令替换允许我们把一个命令的输出作为一个展开模式来使用：
- `ls -l $(which cp)` 把 which cp 的执行结果作为一个参数传递给 ls 命令，因此可以在不知道 cp 命令 完整路径名的情况下得到它的文件属性列表。

### 双引号
如果你把文本放在双引号中， shell 使用的特殊字符，都失去它们的特殊含义，被当作普通字符来看待。 有几个例外： $，\ (反斜杠），和 `（倒引号）。这意味着单词分割、路径名展开、 波浪线展开和花括号展开都将失效，然而参数展开、算术展开和命令替换 仍然执行。
- ls -l "two words.txt" 当文件名含有空格时使用双引号

### 单引号
禁止所有的展开

## 权限

### id 显示用户身份号 

### chmod 改变文件或目录的访问权限

#### 参数
参数|描述
---|----
-c | 当发生改变时，报告处理信息
-f | 错误信息不输出
-R | 处理指定目录以及其子目录下的所有文件
-v | 运行时显示详细处理信息

#### 权限范围
a|b
--|---
u | 目录或者文件的当前的用户
g | 目录或者文件的当前的群组
o | 除了目录或者文件的当前用户或群组之外的用户或者群组
a | 所有的用户及群组

#### 权限代号

代号|权限
----|---
r | 读权限，用数字4表示
w | 写权限，用数字2表示
x | 执行权限，用数字1表示
- | 删除权限，用数字0表示
s | 特殊权限 

#### 格式
1. 文字设定法：`chmod ［who］ ［+ | - | =］ ［mode］ 文件名` chmod a+x a.txt
2. 数字设定法：`chmod ［mode］ 文件名` r=4，w=2，x=1

### chgrp 变更文件与目录所属群组

#### 参数同 chmod
- --reference=<文件或者目录> 根据指定文件改变文件的群组属性

#### 实例
- `chgrp -R 100 test6` 通过群组识别码改变文件群组属性，100为users群组的识别码，具体群组和群组识别码可以去/etc/group文件中查看

### chown 改变文件的拥有者和群组

#### 格式
`chown [选项] [所有者][:[组]] 文件 `

#### 必要参数
参数|描述
---|----
-c | 显示更改的部分的信息
-f | 忽略错误信息
-h | 修复符号链接
-R | 处理指定目录以及其子目录下的所有文件
-v | 显示详细的处理信息
-deference | 作用于符号链接的指向，而不是链接文件本身

#### 选择参数
参数|描述
---|----
--reference=<目录或文件> | 把指定的目录/文件作为参考，把操作的文件/目录设置成参考文件/目录相同拥有者和群组
--from=<当前用户：当前群组> | 只有当前用户和群组跟指定的用户和群组相同时才进行改变
--help | 显示帮助信息
--version | 显示版本信息

#### 实例
- `chown mail:mail log2012.log` 改变拥有者和群组
- `chown root: log2012.log` 改变文件拥有者和群组
- `chown :mail log2012.log` 改变文件群组

### umask 设置默认权限
当创建一个文件时，umask 命令控制着文件的默认权限。umask 命令使用八进制表示法来表达 从文件模式属性中删除一个位掩码。

### su 以其他用户身份和组 ID 运行一个 shell
输入 exit 能返回原来的 shell

### sudo 以另一个用户身份执行命令
sudo 命令不要求超级用户的密码。使用 sudo 命令时，用户使用他/她自己的密码来认证。su 和 sudo 之间的一个重要区别是 sudo 不会重新启动一个 shell，也不会加载另一个 用户的 shell 运行环境。

### passwd 更改用户密码
- `passwd [user]`

## 进程管理
当系统启动的时候，内核先把一些它自己的活动初始化为进程，然后运行一个叫做 init 的程序。init， 依次地，再运行一系列的称为 init 脚本的 shell 脚本（位于/etc），它们可以启动所有的系统服务。 其中许多系统服务以守护（daemon）程序的形式实现，守护程序仅在后台运行，没有任何用户接口(User Interface)。 这样，即使我们没有登录系统，至少系统也在忙于执行一些例行事务。

在进程方案中，一个程序可以发动另一个程序被表述为一个父进程可以产生一个子进程。

内核维护每个进程的信息，以此来保持事情有序。例如，系统分配给每个进程一个数字，这个数字叫做 进程(process) ID 或 PID。PID 号按升序分配，init 进程的 PID 总是1。内核也对分配给每个进程的内存和就绪状态进行跟踪以便继续执行这个进程。 像文件一样，进程也有所有者和用户 ID，有效用户 ID，等等。

## top 显示当前系统正在执行的进程的相关信息
- 查询一般不带参数的，具体信息通过grep筛选
- 交互模式下键入M进程列表按内存使用大小降序排列，键入P进程列表按CPU使用大小降序排列
- %id表示CPU空闲率，过低表示可能存在CPU存在瓶颈
- %wa表示等待I/O的CPU时间百分比，过高则I/O存在瓶颈 > 用iostat进一步分析
### 输出信息
各进程（任务）的状态监控，项目列信息说明如下：
信息|说明
----|---
PID | 进程描述符
USER | 进程所有者
PR | 进程优先级
NI | nice值。负值表示高优先级，正值表示低优先级
VIRT | 进程使用的虚拟内存总量，单位kb。VIRT=SWAP+RES
RES | 常驻内存，单位kb。RES=CODE+DATA
SHR（SHARE） | 共享内存大小，单位kb
S（STAT） | 进程状态。D=不可中断的睡眠状态 R=运行 S=睡眠 T=跟踪/停止 Z=僵尸进程
%CPU | CPU 使用率
%MEM | 进程使用的物理内存百分比
TIME+ | 进程使用的CPU时间总计，单位1/100秒
COMMAND | 进程名称（命令名/命令行）

### & 把一个进程放到后台执行
- `xlogo &` 在程序命令之后加上 & 字符，让他在后台运行

### jobs 列出从终端中启动了的任务
返回任务号和这个任务的命令

### 停止一个进程
输入 Ctrl+z 可以停止进程

### fg 恢复程序到前台运行

### bg 把程序移到后台
- `bg %1`

### free 显示 Linux 系统中空闲的内存情况

#### 参数
参数|描述
---|----
-b | 以 Byte 为单位显示
-k | 以 KB 为单位显示
-m | 以 MB 为单位显示
-g | 以 GB 为单位显示
-o | 不显示缓冲区调节列
-s | 持续观察内存使用状况
-t | 显示内存总和列
-V | 显示版本信息

#### 输出
输出|描述
---|----
total | 总计物理内存大小
used | 已使用多大
free | 可用有多少
shared | 多个进程共享内存总额
buffers/cached | 磁盘缓存的大小

## ps 查看进程

### linux上进程有5种状态: 
1. 运行(正在运行或在运行队列中等待) 
2. 中断(休眠中, 受阻, 在等待某个条件的形成或接受到信号) 
3. 不可中断(收到信号不唤醒和不可运行, 进程必须等待直到有中断发生) 
4. 僵死(进程已终止, 但进程描述符存在, 直到父进程调用wait4()系统调用后释放) 
5. 停止(进程收到SIGSTOP, SIGSTP, SIGTIN, SIGTOU信号后停止运行运行)

### 实例
1. 显示所有进程信息 ps -A
1. 显式指定用户信息 ps -u root
1. 显示所有进程信息，连同命令行 ps -ef
1. 显示属于每个用户的进程信息 ps aux
1. 以完整格式显示所有进程 ps -ajx

## pstree 	输出一个树型结构的进程列表(processtree)，这个列表展示了进程间父/子关系。

### kill 给进程发送信号

编号|名字|含义
----|----|----
1|HUP|挂起
2|INT|中断
9|KILL|杀死
15|TERM|终止
18|CONT|继续
19|STOP|停止

#### 格式
`kill [参数][进程号]`

#### 实例
- `kill -9 1`
- `kill -l` 得到完整信号列表

### killall 给匹配特定程序或用户名的多个进程发送信号
killall 杀死指定 **名字** 的进程（kill processes by name）。kill 命令可以杀死指定进程 **PID** 的进程。

#### 格式
`killall [-u user][-signal] name...`

## 环境变量
shell 在环境中存储了两种基本类型的数据，虽然 bash 几乎无法分辨这些数据的类型。 它们是环境变量和 shell 变量。

### set 显示 shell 或环境变量

### printenv 显示环境变量
- `printenv` 显示所有环境变量
- `printenv name` 显示特定变量的值

## 存储媒介

### mount 挂载一个文件系统

### umount 卸载一个文件系统

### fask 检查和修复一个文件系统

### fdisk 分区表控制器

### mkfs 创建文件系统

### fdformat 格式化一张软盘

### dd 把面向块的数据直接写入设备

### genisoimage (mkisofs) 创建一个 ISO 9660的映像文件

### wodim (cdrecord) 把数据写入光存储媒介

### md5sum  计算 MD5检验码

# 网络系统

## ifconfig 
- ifconfig 显示已激活的网络设备信息
- ifconfig eth0 up 启动或者关闭指定网卡
- ifconfig eth0 192.168.1.1 配置 IP 地址
- ifconfig eth0 mtu 1500 设置最大传输单元
- ifconfig eth0 arp 开启 arp，关闭是 -arp

### ping 发送 ICMP ECHO_REQUEST 数据包到网络主机

### traceroute 打印到一台网络主机的路由数据包
显示从本地到指定主机要经过的所有“跳数”的网路流量列表

对于那些提供标识信息的路由器，能看到主机名，IP地址和性能数据，对于没有提供标识的路由器，会看到几个星号

### netstat 显示与IP、TCP、UDP和ICMP协议相关的统计数据
- `netstat -ie` 查看系统中的网络接口
- `netstat -r` 显示路由表信息
- `netstat -a` 列出所有端口
- `netstat -nu` 显示当前 UDP 连接状况
- `netstat -apu` 显示 UDP 端口号的使用情况
- `netstat -i` 显示网卡列表
- `netstat -s` 显示网络统计信息
- `netstat -l` 显示监听的套接口
- `netstat -at` 列出所有 tcp 端口
- `netstat -a | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'` 统计机器中网络连接各个状态个数
- `netstat -nat |awk '{print $6}'|sort|uniq -c` 把状态全都取出来后使用uniq -c统计后再进行排序
- `netstat -nat | grep "192.168.120.20:16067" |awk '{print $5}'|awk -F: '{print $4}'|sort|uniq -c|sort -nr|head -20` 查看连接某服务端口最多的的IP地址
- `netstat -ap | grep ssh` 找出程序运行的端口
- `netstat -pt` 在 netstat 输出中显示 PID 和进程名称
- `netstat -anpt | grep ':16064'` 找出运行在指定端口的进程

## route 查看路由表

## tcpdump 抓包分析
- tcpdump -i eth0 port 3000

### ftp 因特网文件传输程序

### wget 非交互式网络下载器

### ssh OpenSSH SSH 客户端（远程登陆程序）

## 文件系统

### file 显示文件详细信息

#### 格式
`file [filename]`

### df 查看磁盘剩余空间的数量

### tar 打包

#### 格式
`tar [必要参数] [选择参数] [打包后文件名] [文件或目录]`

#### 必要参数
- -A 新增压缩文件到已存在的压缩
- -B 设置区块大小
- -c 建立新的压缩文件
- -d 记录文件的差别
- -r 添加文件到已经压缩的文件
- -u 添加改变了和现有的文件到已经存在的压缩文件
- -x 从压缩文件中提取文件
- -t 显示压缩文件的内容
- -z 支持 gzip 解压文件
- -j 支持 bzip2 解压文件
- -z 支持 compress 解压文件
- -v 显示操作过程
- -l 文件系统边界设置
- -k 保留原有文件不被覆盖
- -m 保留五年间不被覆盖
- -W 确认压缩文件的正确性

#### 可选参数
- -b 设置区块数目
- -C 切换到指定目录
- -f 指定压缩文件

#### 常见解压、压缩命令

##### tar 
解包：tar xvf FileName.tar
打包：tar cvf FileName.tar DirName

##### .gz
解压1：gunzip FileName.gz
解压2：gzip -d FileName.gz
压缩：gzip FileName

##### .tar.gz 和 .tgz
解压：tar zxvf FileName.tar.gz
压缩：tar zcvf FileName.tar.gz DirName

##### .bz2
解压1：bzip2 -d FileName.bz2
解压2：bunzip2 FileName.bz2
压缩： bzip2 -z FileName

##### .tar.bz2
解压：tar jxvf FileName.tar.bz2
压缩：tar jcvf FileName.tar.bz2 DirName

##### .bz
解压1：bzip2 -d FileName.bz
解压2：bunzip2 FileName.bz

##### .tar.bz
解压：tar jxvf FileName.tar.bz

##### .Z
解压：uncompress FileName.Z
压缩：compress FileName

##### .tar.Z
解压：tar Zxvf FileName.tar.Z
压缩：tar Zcvf FileName.tar.Z DirName

##### .zip
解压：unzip FileName.zip
压缩：zip FileName.zip DirName

##### .rar
解压：rar x FileName.rar
压缩：rar a FileName.rar DirName 

### gzip
- -a或--ascii 　使用ASCII文字模式。 
- -c或--stdout或--to-stdout 　把压缩后的文件输出到标准输出设备，不去更动原始文件。 
- -d或--decompress或----uncompress 　**解压** 文件。 
- -f或--force 　强行压缩文件。不理会文件名称或硬连接是否存在以及该文件是否为符号连接。 
- -h或--help 　在线帮助。 
- -l或--list 　列出压缩文件的相关信息。 
- -L或--license 　显示版本与版权信息。 
- -n或--no-name 　压缩文件时，不保存原来的文件名称及时间戳记。 
- -N或--name 　压缩文件时，保存原来的文件名称及时间戳记。 
- -q或--quiet 　不显示警告信息。 
- -r或--recursive 　递归处理，将指定目录下的所有文件及子目录一并处理。 
- -S<压缩字尾字符串>或----suffix<压缩字尾字符串> 　更改压缩字尾字符串。 
- -t或--test 　测试压缩文件是否正确无误。 
- -v或--verbose 　显示指令执行过程。 
- -V或--version 　显示版本信息。 

### scp

#### 上传文件到远程服务器
- `scp /path/filename  username@serverIp:/path`

#### 从远程服务器下载文件
- `scp username@serverIp:/path/filename ~/local_dir`

### df 检查文件系统的磁盘空间占用情况

#### 参数
参数|描述
----|---
-a | 全部文件系统列表
-h | 方便阅读方式显示
-i | 显示 inode 信息
-T | 文件系统类型
-t<文件系统类型> | 只显示选定文件系统的磁盘信息
-x<文件系统类型> | 不显示选定文件系统的磁盘信息

## 常用工具

### [键盘高级操作技巧](http://billie66.github.io/TLCL/book/chap09.html)

### history 查看历史命令
- `history | less` 搜索历史命令

### clear 清空屏幕

## 文件查找

### which 显示一个可执行程序的位置
只对 **可执行程序** 有效，不包括 **内建命令** 和 **命令别名**，which 指令会在 PATH 变量指定的路径中，搜索某个系统命令的位置，并且返回第一个搜索结果

### locate 通过名字来查找文件
和 find -name 一样，但是速度更快。
locate 命令可以在搜寻数据库时快速找到档案，数据库由 updatedb 程序来更新，updatedb 是由cron daemon 周期性建立的，locate命令在搜寻数据库时比由整个由硬盘资料来搜寻资料来得快，但较差劲的是locate所找到的档案若是最近才建立或 刚更名的，可能会找不到，在内定值中，updatedb 每天会跑一次，可以由修改 crontab 来更新设定值。(etc/crontab)

#### 格式
`locate [选择参数][样式]`

#### 参数
- -e 将排除在寻找范围之外
- -o 指定资料库存的名称
- -d 指定资料库的路径

#### 实例
1. `locate /etc/sh` 搜索 etc 目录下所有以 sh 开头的文件
1. `locate pwd` 查找和 pwd 相关的所有文件
1. `locate /etc/sh` 搜索 etc 目录下所有以 sh 开头的文件

### whereis 查看文件的位置
whereis 命令只能用于 **程序名** 的搜索，而且只搜索二进制文件（参数-b）、man 说明文件（参数-m）和源代码文件（参数-s）。如果省略参数，则返回所有信息。使用和 locate 相同的数据库。

#### 格式
`whereis [-bmsu] [BMS 目录名 -f ] 文件名`

#### 实例
where nginx 将和 nginx 相关的文件都查找出来

### find 在一个目录层次结构中搜索文件

#### 格式
`find pathname -options [-print -exec -ok ...]`

#### 参数
参数 | 描述
-----|----
pathname | 用来查找的目录路径。.表示当前目录
-print | 将匹配的文件输出到标准输出
-exec | find命令对匹配的文件执行该参数所给出的shell命令。相应命令的形式为'command' {} \;，注意{}和\;之间的空格。{} 代表前面查找出来的文件名
-ok | 和 -exec 作用相同，在执行每个命令前让用户确定是否执行

#### 选项
选项 | 描述
----|----
-name | 按照文件名查找
-perm | 按照文件权限查找
-user | 按照文件属主查找
-group | 按照文件所属的组查找
-mtime -n +n | 按照文件更改的时间来查找，-n 表示文件更改时间距现在 n 天以内，+n 表示文件更改时间距现在 n 天以前
-nogroup | 查找无有效所属组的文件，即该文件所属的组在/etc/groups中不存在。
-nouser  | 查找无有效属主的文件，即该文件的属主在/etc/passwd中不存在。
-newer file1 ! file2 | 查找更改时间比文件file1新但比文件file2旧的文件。
-type | 查找某一类型的文件，b - 块设备文件、d - 目录、c - 字符设备文件、p - 管道文件、l - 符号链接文件、f - 普通文件。
-follow | 如果 find 命令遇到符号链接文件，就跟踪链接所指向的文件 

#### 实例
1. `find . -name "*.log"` 根据关键词查找
1. `find . type d|sort` 查找当前所有目录并排序
1. `find . type -exec ls -l {} \;` 匹配当前目录下的所有普通文件，并在-exec选项中使用ls -l命令将它们列出。
1. `find . type f -mtime +14 -exec rm {} \;` 在目录中查找更改时间在n日以前的文件并删除它们
1. `find . \( -nmae "*.txt" -or -name "*.pdf" \)` 打印目录下所有以 .txt 和 .pdf 结尾的文件名
1. `find . ! -name "*.txt"` 打印当前目录下所有不以 .txt 结尾的文件名

## 文件内容查看

### more 逐页读取文件（启动时加载整个文件）
- +n      从笫n行开始显示
- -n       定义屏幕大小为n行
- +/pattern 在每个档案显示前搜寻该字串（pattern），然后从该字串前两行之后开始显示  
- -c       从顶部清屏，然后显示

#### 实例
ls -l | more -5 使用 more 分页显示

### less （more 的改进版）
可以向前或者向后浏览和搜索文件，而且不会加载整个文件

#### 参数
参数|实例
----|----
-b | <缓冲区大小> 设置缓冲区的大小
-e | 当文件显示结束后，自动离开
-f | 强迫打开特殊文件，例如外围设备代号、目录和二进制文件
-g | 只标志最后搜索的关键词
-i | 忽略搜索时的大小写
-m | 显示类似more命令的百分比
-N | 显示每行的行号
-o | <文件名> 将less 输出的内容在指定文件中保存起来
-Q | 不使用警告音
-s | 显示连续空行为一行
-S | 行过长时间将超出部分舍弃
-x | <数字> 将“tab”键显示为规定的数字空格
/字符串 | 向下搜索“字符串”的功能
?字符串 | 向上搜索“字符串”的功能
n | 重复前一个搜索（与 / 或 ? 有关）
N | 反向重复前一个搜索（与 / 或 ? 有关）
b | 向后翻一页
d | 向后翻半页
h | 显示帮助界面
Q | 退出less 命令
u | 向前滚动半页
y | 向前滚动一行
空格键 | 滚动一行
回车键 | 滚动一页
`[pagedown]` | 向下翻动一页
`[pageup]` | 向上翻动一页

### head 显示档案的开头

#### 参数
参数|描述
---|----
-q | 隐藏文件名
-v | 显示文件名
-c | 显示的字节数
-n | 显示的行数 

#### 实例
head -n -6 log2014.log 输出文件除了最后n行的全部内容

### tail 查看文件最后几行

#### 参数
参数|描述
---|----
-f | 不断刷新，循环读取
-q | 不显示处理信息
-v | 显示详细的处理信息
-c | 显示的字节数
-n | 显示的行数
--pid=PID | 与 -f 合用，表示在进程 PID 死掉后结束
-s | 与 -f 合用，表示在每次反复的间隔休眠 s 秒

### uniq 报道或忽略重复行

### wc 打印行数、字数和字节数
- `wc filename`

## 文本处理

### cat 连接文件并且打印到标准输出

#### 功能
- 一次显示整个文件 cat filename
- 从键盘创建一个文件 cat > filename,按下 Ctrl+d 结束
- 讲几个文件合并为一个文件 cat file1 file2 > file

#### 参数
参数|描述
---|----
-A | --show-all 等价于 -vET
-b | 对非空输出行编号
-e | 等价于 -vE
-E | 在每行结束出显示 $
-n | 对所有输出行编号
-s | 有连续两次以上的空白行，就代换为一行的空白行
-t | 与 -vT 等价
-T | 将跳格字符显示为 ^I
-v --show-nonprinting | 使用 ^ 和 M- 引用，除了 LFD 和 TAB 之外

#### 实例
- `cat -A foo.txt` 输出 ^IThe quick brown fox jumped over the lazy dog.       $ 。tab 用 ^I 表示，$ 著现在文本行真正的结尾处，表明文本包含末尾的空格。

#### tac 和cat相反，从最后一行开始显示

### sort 给文本行排序
能够接受命令行中的多个文件作为参数，吧多个文件合并成一个有序的文件。

#### 参数
参数|描述
---|----
-f | 不区分大小写
-n | 基于字符串的数值来排序
-r | 按相反顺序排序，按照降序排列
-k | `--key=field1[,field2]` 对从 field1 到 field2 之间的字符排序，而不是整个文本行。
-o | 把排好序的输出结果发送到文件，而不是标准输出
-t | 定义分割字符

#### 实例
- `sort file1.txt file2.txt file3.txt > final_sorted_list.txt`
- `du -s /usr/share/* | sort -nr | head` 显示十个最大的空间消费者
- `ls -l /usr/bin | sort -nr -k 5 | head` 按照文件大小（第五个字段）排序
- `sort --key=1,1 --key=2n distros.txt` 我们指定了一个字段区域。因为我们只想对第一个字段排序，我们指定了 1,1， 意味着“始于并且结束于第一个字段。”在第二个实例中，我们指定了 2n，意味着第二个字段是排序的键值， 并且按照数值排序

### uniq 报告或者省略重复行
只会删除相邻的重复行

#### 参数
参数|描述
---|----
-c | 输出所有的重复行，每行开头显示重复的次数
-d | 只输出重复行
-f n | 忽略开头的 n 个字段，字段之间由空格分隔

### cut 从每行中删除文本区域

#### 参数
参数|描述
----|----
-b | 以字节为单位进行分割
-c | 以字符为单位进行分割
-d | 自定义分隔符，默认为制表符
-f | 自定义字段
--complement | 抽取整个文本行，除了那些由 -c 或 -f 选项指定的文本

#### 实例
- `cut -f 1,3 -d ' ' student.txt` 取出 student.txt 文件中的第一列和第三列
- `cut -f 1-3 -d ' ' student.txt` 取出 student.txt 文件的前三列

### paste 合并文件文本行

#### 参数
参数|描述
----|---
-s | 将每个文件合并成行而不是按行粘贴
-d | 自定义分隔符，默认为制表符

#### 实例
- `paste distros-dates.txt distros-versions.txt` 将这两个文件按列拼接

### join 基于某个共享字段来联合两个文件的文本行

#### 参数
参数|描述
----|---
-j FIELD | 等同于 -1 FIELD -2 FIELD,-j 指定一个域作为匹配字段
-1 FIELD | 以 file1 中 FIELD 字段进行匹配
-2 FIELD | 以 file2 中 FIELD 字段进行匹配
-t | 自定义分隔符，默认为制表符

### comm 逐行比较两个有序的文件

### diff 逐行比较文件
支持 许多输出格式，并且一次能处理许多文本文件。软件开发员经常使用 diff 程序来检查不同程序源码 版本之间的更改，diff 能够递归地检查源码目录，经常称之为源码树。

### patch 给原始文件打补丁

### tr 转换字符
tr 命令操作标准输入，并把结果输出到标准输出。tr 命令接受两个参数：要被转换的字符集以及相对应的转换后的字符集。字符集可以用三种方式来表示：
1. 一个枚举列表。例如， ABCDEFGHIJKLMNOPQRSTUVWXYZ
2. 一个字符域。例如，A-Z 。注意这种方法有时候面临与其它命令相同的问题，归因于 语系的排序规则，因此应该谨慎使用。
3. POSIX 字符类。例如，[:upper:]

#### 实例
- `echo "lowercase letters" | tr a-z A-Z` 将小写转换为大写
- `echo "lowercase letters" | tr [:lower:] [:upper:]`

### aspell 交互式拼写检查器

## 文本搜素

### grep 打印匹配行

#### 格式
`grep [option] regex [file...]`

#### 参数
参数|描述
---|----
-i | 忽略大小写
-v | 只打印不匹配的行
-c | 打印匹配的数量

#### 实例
1. 从文件中读取关键词进行搜索 cat text.txt|grep -f test2.txt 输出test.txt文件中含有从test2.txt文件中读取出的关键词的内容行
1. 显示当前目录下面以.txt 结尾的文件中的所有包含每个字符串至少有7个连续小写字符的字符串的行 `grep '[a-z]\{7\}' *.txt`
1. 从文件中查找关键词 grep 'linux' test.txt

### sed 用于筛选和转换文本的流编辑器

#### 参数
参数|描述
---|----
n | 行号，n 是一个正整数。
$ | 最后一行。
/regexp/ | 所有匹配一个 POSIX 基本正则表达式的文本行。注意正则表达式通过 斜杠字符界定。选择性地，这个正则表达式可能由一个备用字符界定，通过\cregexpc 来 指定表达式，这里 c 就是一个备用的字符。
addr1,addr2 | 从 addr1 到 addr2 范围内的文本行，包含地址 addr2 在内。地址可能是上述任意 单独的地址形式。
first~step | 匹配由数字 first 代表的文本行，然后随后的每个在 step 间隔处的文本行。例如 1~2 是指每个位于偶数行号的文本行，5~5 则指第五行和之后每五行位置的文本行。
addr1,+n | 匹配地址 addr1 和随后的 n 个文本行。
addr! | 匹配所有的文本行，除了 addr 之外，addr 可能是上述任意的地址形式。

### awk

## 格式化输出

### nl 添加行号
相当于 `cat -n`

#### 参数
参数|描述
---|----
-b a | 无论是否空行都输出行号
-b t | 对非空行输出行号
-n ln | 行号在萤幕的最左方显示
-n rn | 行号在自己栏位的最右方显示，且不加 0
-n rz | 行号在自己栏位的最右方显示，且加 0
-w | 行号宽度设置，默认为六

### 实例
- `nl -b a -n rz -w 3 log2014.log` 让行号前面自动补上0，行号为3位

### fold 限制文件列宽
- `echo "The quick brown fox jumped over the lazy dog." | fold -w 12` 限制行宽为 12 个字符，默认 80
- 加上 -s 选项可以考虑单词边界

### fmt 简单的文本格式转换器

### pr 

### printf 格式化数据并打印

### groff 文件格式化系统

## 软件包管理
Debian|Red Hat|作用
------|-------|---
apt-get update;apt-cache search search_string | yum search search_string | 查找资源库中的软件包
apt-get update; apt-get install package_name | yum install package_name | 从资源库中安装一个软件包
dpkg --install package_file | rpm -i package_file | 通过软件包文件来安装软件
apt-get remove package_name | yum erase package_name | 卸载软件
apt-get update; apt-get upgrade | yum update | 经过资源库来更新软件包
dpkg --install package_file | rpm -U package_file | 经过软件包文件来升级软件
dpkg --list | rpm -qa | 列出所安装的软件包
dpkg --status package_name | rpm -q package_name | 确定是否安装了一个软件包
apt-cache show package_name | yum info package_name | 显示所安装软件包的信息
dpkg --search file_name | rpm -qf file_name | 查找安装了某个文件的软件包

## 其他内容 

### /etc/group 文件
Linux /etc/group文件与/etc/passwd和/etc/shadow文件都是有关于系统管理员对用户和用户组管理时相关的文件。linux /etc/group文件是有关于系统管理员对用户和用户组管理的文件,linux用户组的所有信息都存放在/etc/group文件中。具有某种共同特征的用户集合起来就是用户组（Group）。用户组（Group）配置文件主要有 /etc/group和/etc/gshadow，其中/etc/gshadow是/etc/group的加密信息文件。

用户组的所有信息都存放在/etc/group文件中。此文件的格式是由冒号(:)隔开若干个字段，这些字段具体如下：
- 组名:口令:组标识号:组内用户列表

#### 组名：
组名是用户组的名称，由字母或数字构成。与/etc/passwd中的登录名一样，组名不应重复。

#### 口令：
口令字段存放的是用户组加密后的口令字。一般Linux系统的用户组都没有口令，即这个字段一般为空，或者是*。

#### 组标识号：
组标识号与用户标识号类似，也是一个整数，被系统内部用来标识组。别称GID.

#### 组内用户列表：
是属于这个组的所有用户的列表，不同用户之间用逗号(,)分隔。这个用户组可能是用户的主组，也可能是附加组。

### date 显示系统当前时间和日期

### cal 显示当前月份的日历

# 参考资料
- [每天一个 linux 命令](https://www.cnblogs.com/peida/archive/2012/12/05/2803591.html)
- [The Linux Command Line](http://billie66.github.io/TLCL/book/index.html)