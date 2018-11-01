---
title: liunx基础指令
date: 2018-11-01 08:50:32
tags: [liunx]
categories: [技术,liunx]
---
*rm -rf default.conf 删除
ln -s /vagrant/nginx.conf default.conf 制定公用地址
service nginx restart 重启nginx*



# 快捷键
tab:命令补全
Ctrl+c：强行终止
Ctrl+d 键盘输入结束或退出终端
Ctrl+s 暂停当前程序，暂停后按下任意键恢复运行
Ctrl+z 将当前程序放到后台运行，恢复到前台为命令fg
Ctrl+a 将光标移至输入行头，相当于Home键
Ctrl+e 将光标移至输入行末，相当于End键
Ctrl+k 删除从光标所在位置到行末
Alt+Backspace 向前删除一个单词
Shift+PgUp 将终端显示向上滚动
Shift+PgDn 将终端显示向下滚动

# Shell 常用通配符
\* 匹配 0 或多个字符
? 匹配任意一个字符
[list] 匹配 list 中的任意单一字符
[!list] 匹配 除list 中的任意单一字符以外的字符
[c1-c2] 匹配 c1-c2 中的任意单一字符 如：[0-9] [a-z]
{string1,string2,...} 匹配 string1 或 string2 (或更多)其一字符串
{c1..c2} 匹配 c1-c2 中全部字符 如{1..10}

# 命令
touch 创建文件
mkdir 创建目录
ls 查看文件目录
man 调用手册
apt-get 搜索，安装软件
cat，nl 查看文件
chown 变更文件所有者 sudo chown 所有者 文件名 chown www.www 目录 -R
chmod 修改文件权限 chmod go-rw 文件名 chmod -R 777 文件名 "0"表示没有权限、“1”表示可执行权限、“2”表示可写权限、“4”表示可读权限。“7=1+2+4
cp 复制文件，复制目录需要带上-r参数 cp 文件名 目录，cp -r 文件名 目录
rm 删除文件，目录需要带上-r参数 rm 文件名 目录，rm -r 文件名 目录
mv 移动文件,重命名文件 mv 源目录文件 目的目录，mv 旧的文件名 新的文件名



# 用户管理命令
su 切换命令
sudo 使用root权限
adduser 创建用户
groups 查看用户组
usermod 添加用户到用户组 sudo usermod -G sudo 用户名
deluser 删除用户 sudo deluser 用户名 --remove-home

# 变量
定义变量：变量名 = 变量值
修改变量：
${变量名#匹配字串} 从头向后开始匹配，删除符合匹配字串的最短数据
${变量名##匹配字串} 从头向后开始匹配，删除符合匹配字串的最长数据
${变量名%匹配字串} 从尾向前开始匹配，删除符合匹配字串的最短数据
${变量名%%匹配字串} 从尾向前开始匹配，删除符合匹配字串的最长数据
${变量名/旧的字串/新的字串} 将符合旧字串的第一个字串替换为新的字串
${变量名//旧的字串/新的字串} 将符合旧字串的全部字串替换为新的字串
变量删除：unser 变量名

# 搜索文件
## locate
快而全，通过“ /var/lib/mlocate/mlocate.db ”数据库查找，不过这个数据库也不是实时更新的，系统会使用定时任务每天自动执行 updatedb 命令更新一次，所以有时候你刚添加的文件，它可能会找不到，需要手动执行一次 updatedb 命令（在我们的环境中必须先执行一次该命令）。它可以用来查找指定目录下的不同文件类型
示例：locate /etc/sh，locate /usr/share/\*.jpg

## which 
小而精，本身是 Shell 内建的一个命令，我们通常使用 which 来确定是否安装了某个指定的软件，因为它只从 PATH 环境变量指定的路径中去搜索命令
示例：which ma

## find 
精而细，通过文件类型、文件名进行查找而且可以根据文件的属性（如文件的时间戳，文件的权限等）进行搜索
格式：find [path] [option] [action]
示例：sudo find 目录 -name 文件名
通过时间搜索：
-atime 最后访问时间
-ctime 最后修改文件内容的时间
-mtime 最后修改文件属性的时间
示例： 列出home目录中，当天有改动的文件：find ~ -mtime 0
列出用户目录下比code文件夹新的文件：find ~ -newer /home/vagrant/Co

# 压缩文件：
## zip 
zip -r -9 -q -o 压缩文件名.zip 需要压缩的文件路径
-r参数表示递归打包包含子目录的全部内容。，-q 参数表示为安静模式，即不向屏幕输出信息。-o表示输出文件，需在其后紧跟打包输出文件名。-e参数可以创建加密压缩包。
zip解压:：unzip -q 需要解压文件 -d 解压指定目录
查看压缩文件：du -h 压缩文件名 ，du -h -d 0 *.zip ~ | sort

## rar
rar的命令参数没有-，如果加上会报错。rar d 压缩文件名.rar 需要压缩的文件路径
rar解压：unrar e 压缩文件名.rar 需要解缩的文件路径

## tar
打包：tar -zcvf something.tar something
解包：tar -zxvf something.tar
指定路径：-C 参数

# 文件系统管理与磁盘操作
df -h 查看磁盘的容量
du -h -d 查看目录的容量

## 帮助命令：
help 显示shell内建命令的帮助信息 ls --help
man 详细获取命令手册，不区分内建与外部命令。 man ls

## 任务计划crontab：
格式：
* Example of job definition:# .---------------- minute (0 - 59)# | .------------- hour (0 - 23)# | | .---------- day of month (1 - 31)# | | | .------- month (1 - 12) OR jan,feb,mar,apr ...# | | | | .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat# | | | | |# * * * * * user-name command to be executed*

crontab -e 添加计划任务
crontab -e 查看计划任务
pgrep cron 查看任务执行
sudo tail -f 任务路径 查看到执行任务命令之后在日志中的信息反馈
crontab -r 删除alte计划任务



