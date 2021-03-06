---
title: nginx重启几种方法
date: 2019-01-30 16:50:37
tags: [liunx]
categories: [技术,liunx]
---
````
ps -ef|grep nginx
````
平滑重启命令：
kill -HUP 住进称号或进程号文件路径
或者使用
````
/usr/nginx/sbin/nginx -s reload
````
注意，修改了配置文件后最好先检查一下修改过的配置文件是否正 确，以免重启后Nginx出现错误影响服务器稳定运行。

判断Nginx配置是否正确命令如下：
````
nginx -t -c /usr/nginx/conf/nginx.conf
````
或者
````
/usr/nginx/sbin/nginx -t
````
nginx  reload重启

执行
````
# /usr/local/nginx/sbin/nginx -s reload 
````
nginx已经重启成功

Centos nginx重启

重启Nginx
````
service nginx restart
/etc/init.d/nginx stop
/etc/init.d/nginx start

Ubuntu Nginx

$sudo service nginx start
$sudo service nginx stop
````
win7
启动
　　解压至c:\nginx，运行nginx.exe(即nginx -c conf\nginx.conf)，默认使用80端口，日志见文件夹C:\nginx\logs
常用配置
 　　C:\nginx\conf\nginx.conf,使用自己定义的conf文件如my.conf，命令为nginx -c conf\my.conf
