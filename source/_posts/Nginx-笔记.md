---
title: Nginx 笔记
date: 2018-09-17 16:24:00
tags: Nginx
category: 基础
---
Nginx ("engine x") 是一个高性能的 HTTP 和 反向代理 服务器，也是一个 IMAP/POP3/SMTP 代理服务器。Nginx 是由 Igor Sysoev 为俄罗斯访问量第二的 Rambler.ru 站点开发的。

# 目录结构

- conf 配置文件
- html 网页文件
- logs 日志文件
- sbin 主要二进制程序

# 信号控制与进程控制

- kill 信号量 进程号

信号量|作用
-----|-------
TERM，INT|立刻停止
QUIT|优雅停止
HUP|改变配置文件，平滑的加载配置文件
USR1|重读日志文件，在日志按月/日分割时有用
USR2|平滑升级
WINCH|优雅关闭旧的进程(配合 USR2 来进行升级)

如何获得进程号：
1. 通过  `ps aux|grep nginx`
1. 通过 nginx  配置文件查看 pid 写在哪个文件 （如 `cat /run/nginx.pid`） 

## 控制命令

s 代表 signal（信号量）

- nginx -s stop 立刻停止（INT）
- nginx -s quit 优雅停止（QUIT）
- nginx -s reload 平滑的加载配置文件（HUP）
- nginx -s reopen 重读日志文件（USR1）
- nginx -t 测试配置文件是否出错

# 配置文件

``` nginx
# 定义 Nginx 运行的用户和用户组
user www www;

# 指有一个工作的子进程，可以自行修改,一般设置为 CPU 数*核数
worker_processes 1; 
Event{
	# 单个进程最大连接数（最大连接数=连接数+进程数）
	worker_connections 1024; 
}
# 配置 http 服务器
http{
    # 虚拟主机配置
    server{
        # 监听端口
   		listen 80;
        # 监听的域名或者 ip
    	server_name xxx.com;
        # 文件路径，相对于 nginx 根路径
    	root /var/www;
    location / {
        
    }
    access_log logs/host.access.log;
    }
}
```

## Location  

location 有“定位” 的意思，根据 URI 来进行不同的定位。

在虚拟主机的配置中，location 可以把网站的不同部分定位到不同的处理方式上。

```nginx
location [=|~|~*|^~] patt {
    
}
```

- `location = patt{}` 精准匹配

- `location patt{}` 一般匹配

- `location ~ patt{}` 正则匹配

首先看看有没有精准匹配，如果有停止匹配过程。

## Rewrite 

rewrite 用来实现 URL 重写，让网站中的 URL 达到某种状态时则定向/跳转到某个规则，但是服务器内部的 rewrite 和 302 跳转不一样，内部的 rewrite 上下文没变。

```nginx
# 注意 if 后要加空格
if (){
	# 重写模式
}
```
条件的写法：
- `=`来判断是否相等，用于字符串比较
- `~`用来判断正则匹配（区分大小写），`~*` 不区分大小写
- `-f -d -e` 来判断是否为文件，为目录，是否存在

正则里如果有{}，正则要用 "" 包括。

例子：

### 根据方法重写

```nginx
if ($request_mothed=POST){
	return 403;
}
```
### 根据浏览器重写

```nginx
if ($http_user_agent ~* msie){
	rewrite ^(.*)$ ie.html;
    # 防止循环重定向
	break;
}
```
### 访问不存在的路径

```nginx
if (!-e $document_root$fastcgi_script_name){
	rewrite ^.*$ 404.html;
    # 仍然要加 break
    # 或者 rewrite ^.*$ 404.html break;
	break; 
}
```
### set 使用示例

set 是设置变量用的,可以用来达到多条件判断时作标志用。达到 apache 下的 rewrite_condition 的效果
```nginx
if ($http_user_agent ~* msie){
	set $isie 1;
}
if ($fastcgi_scrip_name = ie.html){
	set $isie 0;
}
if ($isie = 1){
    # 如果是ie浏览器且访问的不是 ie.html 则重定向，作用同上面的 break
	rewrite ^.*$ ie.html;
}
```
### url 重写

```nginx
location /ecshop{
	rewrite "goods-(\d{1,7})\.html" /ecshop/goods.php?id=$1;
}
```
### 将其他图片格式结尾的全部变成gif

```nginx
location ~* \.(jpg|jpeg|png)${
rewrite '(.*?).(jpg|jpeg|png)$' $1.gif
}
```
# 整合 PHP

Apache 一般是把 PHP 当作自己的模块来启动。
而 Nginx 则是把 HTTP 请求变量（如 get，user_agent 等）转发给 php 进程，与 Nginx 进行通信，称为 fastcgi 运行方式。
编译后的 php 以 fpm（fastcgi）方式运行
把请求的信息转发给 9000 端口的 PHP 进程，让 PHP 处理，指定目录下的 PHP 文件。

```nginx
location ~ \.php$ {
	root html;
	fastcgi_pass 127.0.0.1:9000;
	fastcgi_index index.php
	fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
	include fastcgi_params;
}
```
请求过程：

1. 遇到 php 结尾的文件
1. 把根目录定位到 html
1. 把请求上下文转交给 php 进程
1. 并告诉 php 进程，当前的脚本是 $document_root$fastcgi_script_name，php 就会去找这个脚本并处理

## 支持 pathinfo

- 方法一

```nginx
location / {
    # 当请求的文件不存在
	if (!-e $request_filename){
	rewrite ^(.*)$ /index.php?s=$1
}
```

- 方法二

```nginx
location / {
	try_files $uri /index.php?$uri;
}
```

- 方法三

```nginx
location ~ \.php(.*)${
	...
	fastcgi_param PATH_INFO $1;# 后向引用
}
```

# 传输速度优化

## gzip 压缩

常用参数
```nginx
gzip on|off; # 是否开启gzip
gzip_buffers 32 4K| 16 8K # 缓冲(压缩在内存中缓冲几块? 每块多大?)
gzip_comp_level [1-9] # 推荐 6 压缩级别(级别越高,压的越小,越浪费 CPU 计算资源)
gzip_disable # 正则匹配 UA 什么样的 Uri 不进行 gzip
gzip_min_length 4000 # 开始压缩的最小长度(再小就不要压缩了,意义不在)
gzip_http_version 1.0|1.1 # 开始压缩的http协议版本(可以不设置,目前几乎全是1.1协议)
gzip_proxied # 设置请求者代理服务器,该如何缓存内容
gzip_types text/plain application/xml # 对哪些类型的文件用压缩 如txt,xml,html ,css
gzip_vary on|off # 是否传输gzip压缩标志
```
可以写在 http，server，location 里。
ps：图片，MP3 这样的二进制文件不必压缩，因为压缩比较小。

## expire 缓存

写在 location 或 if 里面。
格式：`expires 30s(30m,2h,30d);`

```nginx
locaion ~* \.(jpg|jpeg|gif|png)$ {
	root html;
    # 图片在两天内缓存在浏览器中
	expire 2d;
}
```
**服务器的日期要准确，如果服务器的日期落后于实际日期，可能导致缓存失效。**

# 反向代理和负载均衡

## 反向代理

用 Nginx 做反向代理用 `proxy_pass`。比如，Nginx 不自己处理图片相关的请求，而是把图片的请求转发给 Apache 来处理。

反向代理反导致后端服务器接收到的客户端 IP为前端服务器的 IP，而不是客户真正的 IP，可以通过设置 `X-Forwarded-For` 头信息将用户 IP 传到后台服务器。

配置：

```nginx
location ~ \.(jpg|jpeg|png|gif)$ {
    proxy_pass http://IP:port;
    proxy_set_header X-Forwarded-For $remote_addr;
}
```
## 负载均衡

把多台服务器用 upstream 加入到一个组。

```nginx
upstream imgserver{
	server 127.0.0.1:81 weight=1 max_fails=2 fail_timeout=3;
	server 127.0.0.1:82 weight=1 max_fails=2 fail_timeout=3;
}
upstream # 做负载均衡不可以用 localhost
location ~* \.(png|jpg|jpeg)$ {
	proxy_pass http://imgserver;
    proxy_set_header X-Forwarded-For $remote_addr;
}
```

默认地负载均衡算法就是针对后端服务器的顺序，逐个请求。也有其他负载均衡算法，如一致性哈希，需要安装第三方模块。

#### 使用第三方模块实现一致性哈希
如 http://wiki.nginx.org/NginxHttpUpstreamConsistentHash
这个模块就是用一致性 hash 来请求后端节点，并且其算法与 PHP 中的 memcache 模块的一致性hash算法兼容。
安装后

```nginx
upstream memserver {
	consistent_hash $request_uri;
	server 127.0.0.1:11211;
	server 127.0.0.1:11212;
}
```
在 PHP.ini 中，如下配置

```ini
inimemcache.hash_strategy = consistent
```

#  定时任务实现日志按日期存储

凌晨00:00:01，把昨天的日志重命名，放在相应的目录下。
再用 USR1 信息号控制 nginx 重新生成新的日志文件。

```shell
#!/bin/bash
base_path='/usr/local/nginx/logs'
log_path=$(date -d yesterday +"%Y%m")
day=$(date -d yesterday +"%d")
mkdir -p $base_path/$log_path
mv $base_path/access.log $base_path/$log_path/access_$day.log
kill -USR1 `cat /usr/local/nginx/logs/nginx.pid`

```

Crontab 编辑定时任务

```shell
01 00 * * * /xxx/path/b.sh # 每天 0 时 1 分(建议在 02-04 点之间,系统负载小)
```

# 参考文章

- [WEB请求处理二：Nginx请求反向代理](https://www.jianshu.com/p/bed000e1830b)