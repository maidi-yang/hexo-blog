---
title: liunx php链接sql server
date: 2019-01-30 16:41:10
tags: [php，liunx，sql server]
categories: [技术,liunx]
---
一、加入微软的源
````
curl https://packages.microsoft.com/config/rhel/7/prod.repo > /etc/yum.repos.d/mssqlrelease.repo
````
二、防止冲突先卸载原有版本(可选)
````
yum remove unixODBC
````
三、安装驱动（三个都要装上，缺一不可）
````
yum install msodbcsql mssql-tools unixODBC-devel
````
四、下载pdo_sqlsrv扩展包
````
wget http://pecl.php.net/get/pdo_sqlsrv-5.3.0.tgz
````
五、解压
````
tar -zxvf pdo_sqlsrv-5.3.0.tgz
````
六、进入解压目录
````
cd pdo_sqlsrv-5.3.0
````
七、执行命令
````
/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config
````
八、编译安装
````
make && make install
````
九、更新php.ini

修改`/usr/local/php/etc/php.ini` 查找：extension = 再最后一个extension= 后面添加上extension = "pdo_sqlsrv.so"

十、重启lnmp 
