---
title: TP5定时任务
date: 2018-11-02 15:15:08
tags: [php,Thinkphp5]
categories: [技术,php]
---
# TP5使用command命令行设置定时任务

## 快速生成指令法
### 1.快速指令生成
`php think make:command First first`

First表示类名，实际生成的指令类则是app\command\First（自动生成的指令类的命名空间默认都是 app\command）。
first表示指令名，建议统一使用小写，如果不传入该参数，则默认用类名的小写作为指令名。
### 2.配置参数
为了让指令可以无需定义自动调用，需要在你的应用配置的console.php配置文件中，增加下面的配置参数：
`'auto_path' => env('app_path') . 'command/',`

### 3.执行结果
配置后， 你可以在命令行测试下刚才新生成的指令。
`php think first`

输出结果为：
`first`

你可以编辑app\command\First类，完成实际的指令任务。
注意，如果你生成了指定命名空间的指令，但又不是你配置的自动加载目录，那么仍然需要在command.php 文件中定义指令。
如果需要生成一个指定的命名空间，可以使用：

`php think make:command app\index\Second second`

### 4.新建.sh文件
然后根目录下新建first.sh文件
内容：
````
PATH=/usr/local/php/bin:/opt/someApp/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin # 将php路径加入都临时变量中
cd /home/wwwroot/域名/  # 进入项目的根目录下，保证可以运行php think的命令
php think first
````
### 5.设置定时任务（*cronta写法可百度*）
连接到服务器，输入 `crontab -e`，写入：

`0 0 * * * sh /home/wwwroot/域名/crond.sh`

### 6.重启crond服务
`systemctl restart crond`

### 7.观察定时任务
` tail -f /var/log/cro`


## 接口方式
`php /home/wwwroot/域名/index.php demo/test/test`
直接设置定时执行，或者加入.sh都行


