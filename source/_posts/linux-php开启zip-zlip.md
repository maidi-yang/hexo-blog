---
title: 'linux php开启zip,zlip'
date: 2019-02-20 11:56:31
tags: [php，liunx，sql server]
categories: [技术,liunx]
---
PHP各个版本下载地址：http://php.net/releases/
解压压缩包：tar -xzvf  php-7.0.33.tar.gz
现在以安装zip扩展为例

````
cd /root/Downloads/php-7.0.33/ext/zip
find / -name phpize

/usr/local/php/bin/phpize
````

生成一些版本配置文件

执行phpize，报错：“Cannot find config.m4.”，执行：

````
cp config0.m4 config.m4
````

````
which php-config
````
/usr/local/bin/php-config

````
./configure --with-php-config=/usr/local/php/bin/php-config
make && make install
````
顺利的话，最终会提示：
````
Installing shared extensions:  /usr/local/php/lib/php/extensions/no-debug-non-zts-20100525
````

修改php.ini

查看配置文件位置
``find / -name php.ini`` 
修改php.ini增加 
``vi php.ini `` 
``extension=/usr/local/php/lib/php/extensions/no-debug-non-zts-20100525/zip.so``
``:wq  #保存退出``
重启服务器
````
service nginx restart
service php-fpm restart
````
去查看phpinfo()吧