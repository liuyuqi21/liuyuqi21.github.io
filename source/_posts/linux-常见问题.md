---
title: linux 常见问题
date: 2019-07-18 08:28:18
tags: 
    - Linux
    - 题目
category: 基础
---
# 进程相关

## 如何让一个进程在后台运行
- 使用 &
    - 输入命令 `./a.out &` 会在终端显示进程号，结果输出到终端
    - 键入 Ctrl + C，发出 SIGINT 信号，程序会继续运行
    - 如果关闭窗口（SIGHUP），进程也会关闭
- 使用 nohup
    - 结果默认会输出到 nohup.out
    - 键入 Ctrl + C（SIGINT），程序直接关闭
    - 如果关闭窗口（SIGHUP），进程不会关闭，可以通过 killall 关闭
一般会使用 nohup 配合 & 来启动程序，同时免疫 SIGINT 和 SIGHUP 信号

## 如何查看日志
- `tail error.log` 查看文件末尾
- `tail -f error.log` 实时输出最新写入的数据

## 查询线程数
- `ps -elf|ec -l`
- `pstree -p|wa -l`

## 查最耗 cpu 的进程
- `ps H -eo pid,pcpu | sort -nk2 | tail`
- `top -c` 键入 P （进程按照 CPU 使用率排序） 

## 查最耗 cpu 的线程
- `top -Hp 10765(进程 ID)` 显示一个进程的线程运行信息列表。键入 P。

## 查看堆栈
- 首先将线程 PID 转化为 16 进制：`printf "%x\n" 10804(线程ID)`
- 查看堆栈：`jstack 10765 | grep '0x2a34' -C5`

## 查出进程号对应的服务名
- `ps aux | fgrep pid`
- `ll /proc/pid` 可以打印出全路径

## 查看某个端口的连接情况
- `netstat -lap | fgrep 22022`
- `/user/sbin/lsof -i:22022`

# 文本处理

## awk
- 原理：逐行处理文件中的数据
- 语法 `awk 'pattern + {action}'`
    - '' 是为了和 shell 命令区分开
    - {} 表示一个命令分组
    - pattern 是一个过滤器，正则使用 /pattern/
    - action 是处理动作，默认是 print
    - 使用 # 作为注释
- 内置变量
    - NR 当前行数，从1 开始
    - NF 当前记录字段个数
    - $0 当前记录
    - $1 ~ $N 当前记录第 n 个字段
- 实例：
    - `cat hello.txt | awk 'NR==3, NR==5{print $1,$NF}'` 显示 hello.txt 中的第三行至第五行的第一列与最后一列
    - `cat hello.txt | awk 'length($0)>80{print NR}'` 显示 hello.txt 中，长度大于 100 的行号

## sed
- 参数：
    - -n sed 会在处理一行文本前，将待处理的文本打印出来，-n 参数关闭了这个功能
    - p 命令表示打印当前行
- 基本命令
    - a\ 在匹配行后面加入一条文本
    - i\ 在匹配行前面加入一行文本
    - c\ 将匹配行替换为目的行
    - d 将匹配行删除
    - s 将匹配行替换 `s/被替换的串/替换成这个串/g` g 代表全部替换
- `sed -n '2,$'p hello.txt` 打印第二行到最后一行，$ 表示最后一行
- `sed -n '/100/'p hello.txt` 打印 hello.txt 中 正则匹配 100 的行
- `sed '/100/'i\ "new line" hello.txt`

## grep

## 统计
sort 和 uniq 经常配对使用。
sort 可以使用 -t 指定分隔符，使用 -k 指定要排序的列。

下面这个命令输出 nginx 日志的 ip 和每个 ip 的 pv，pv 最高的前10
```shell
#2019-06-26T10:01:57+08:00|nginx001.server.ops.pro.dc|100.116.222.80|10.31.150.232:41021|0.014|0.011|0.000|200|200|273|-|/visit|sign=91CD1988CE8B313B8A0454A4BBE930DF|-|-|http|POST|112.4.238.213

awk -F"|" '{print $3}' access.log | sort | uniq -c | sort -nk1 -r | head -n10
```

## [192. 统计词频](https://leetcode-cn.com/problems/word-frequency/)

```shell
cat words.txt | awk '{for(i=1;i<=NF;i++){arr[$i]++;}};END{for(w in arr){print arr[w],w;}}' | sort -rn|awk '{print $2,$1}'

cat words.txt | xargs -n1 | sort | uniq -c | sort -rn | awk '{print $2,$1}'
```



# 磁盘相关

## du 和 df
- du 通过搜索文件来计算每个文件的大小然后累加得到值。
- df 通过文件系统来获取空间大小信息。

如果用户删除了一个正在运行的应用程序所打开的某个目录下的文件。du 返回的值是删除了文件后的大小，df 返回的值，不显示减去该文件后的大小。

如果要删除某个日志：`echo "" > access.log`

## 如何查看已删除文件
- `lsof | grep deleted`


# 参考资料
- [一分钟了解nohup和&的功效](https://mp.weixin.qq.com/s?__biz=MjM5ODYxMDA5OQ==&mid=2651961108&idx=1&sn=3fccc94c4307fa2306bae680d8570c18&chksm=bd2d02c88a5a8bde8944e383a3fb9b6d24461cf07ed5a65dbf774a2382bf1f318aca18df5572&scene=21#wechat_redirect)
- [linux下追查线上问题常用命令](https://mp.weixin.qq.com/s?__biz=MjM5ODYxMDA5OQ==&mid=205216499&idx=1&sn=656b5403f3be045772bb539c873e6bd0&scene=21#wechat_redirect)
- [线上问题排查，这些命令你一定用得到！](https://mp.weixin.qq.com/s?__biz=MjM5ODYxMDA5OQ==&mid=2651963003&idx=1&sn=fdf4c9a54dbc03568c5d5ad7a0c2112b&chksm=bd2d0ba78a5a82b132d5968a147f13ccd4c81cd1da4fc0a9de1228f505191b2357b17488e00d&scene=21#wechat_redirect)
- [Linux上，最常用的一批命令解析（10年精选）](https://mp.weixin.qq.com/s/9RbTGQ4k4s92mrSf2xJ5TQ)
- [LeetCode上稀缺的四道shell编程题解析](https://mp.weixin.qq.com/s/EI63RZZcPzJT4c0zl8XQSA)