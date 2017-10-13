###LINUX LAMP 环境初始配置
***
文件路径

- httpd.conf
  + /etc/httpd/conf/httpd.conf
- php.ini
  + /etc/php.ini

### 参考文章-[http://www.osyunwei.com/archives/8415.html]
***
内容

安装篇：

一、安装Apache

yum install httpd #根据提示，输入Y安装即可成功安装

/etc/init.d/httpd start #启动Apache

备注：Apache启动之后会提示错误：

httpd:httpd: Could not reliably determine the server's fully qualif domain name, using ::1 for ServerName

解决办法：

vi /etc/httpd/conf/httpd.conf #编辑

ServerName www.example.com:80 #去掉前面的注释

:wq! #保存退出

chkconfig httpd on #设为开机启动

/etc/init.d/httpd restart #重启Apache

二、安装MySQL

1、安装MySQL

yum install mysql mysql-server #询问是否要安装，输入Y即可自动安装,直到安装完成

/etc/init.d/mysqld start #启动MySQL

chkconfig mysqld on #设为开机启动

cp /usr/share/mysql/my-medium.cnf /etc/my.cnf #拷贝配置文件（注意：如果/etc目录下面默认有一个my.cnf，直接覆盖即可）

2、为root账户设置密码

mysql_secure_installation

回车，根据提示输入Y

输入2次密码，回车

根据提示一路输入Y

最后出现：Thanks for using MySQL!

MySql密码设置完成，重新启动 MySQL：

/etc/init.d/mysqld restart #重启

/etc/init.d/mysqld stop #停止

/etc/init.d/mysqld start #启动

三、安装PHP

1、安装PHP

yum install php #根据提示输入Y直到安装完成

2、安装PHP组件，使PHP支持MySQL

yum install php-mysql php-gd libjpeg* php-imap php-ldap php-odbc php-pear php-xml php-xmlrpc php-mbstring php-mcrypt php-bcmath php-mhash libmcrypt

这里选择以上安装包进行安装

根据提示输入Y回车

/etc/init.d/mysqld restart #重启MySql

/etc/init.d/httpd restart #重启Apche

配置篇

一、Apache配置

vi /etc/httpd/conf/httpd.conf #编辑文件

ServerTokens OS　 在44行 修改为：ServerTokens Prod （在出现错误页的时候不显示服务器操作系统的名称）

ServerSignature On　 在536行 修改为：ServerSignature Off （在错误页中不显示Apache的版本）

Options Indexes FollowSymLinks　 在331行 修改为：Options Includes ExecCGI FollowSymLinks（允许服务器执行CGI及SSI，禁止列出目录）

#AddHandler cgi-script .cgi　在796行 修改为：AddHandler cgi-script .cgi .pl （允许扩展名为.pl的CGI脚本运行）

AllowOverride None　 在338行 修改为：AllowOverride All （允许.htaccess）

AddDefaultCharset UTF-8　在759行 修改为：AddDefaultCharset GB2312　（添加GB2312为默认编码）

Options Indexes MultiViews FollowSymLinks  在554行 修改为 Options MultiViews FollowSymLinks（不在浏览器上显示树状目录结构）

DirectoryIndex index.html index.html.var  在402行 修改为：DirectoryIndex index.html index.htm Default.html Default.htm

index.php Default.php index.html.var （设置默认首页文件，增加index.php）

KeepAlive Off  在76行 修改为：KeepAlive On （允许程序性联机）

MaxKeepAliveRequests 100  在83行 修改为：MaxKeepAliveRequests 1000 （增加同时连接数）

:wq! #保存退出

/etc/init.d/httpd restart #重启

rm -f /etc/httpd/conf.d/welcome.conf /var/www/error/noindex.html #删除默认测试页

二、php配置

vi /etc/php.ini #编辑

date.timezone = PRC #在946行 把前面的分号去掉，改为date.timezone = PRC

disable_functions = passthru,exec,system,chroot,scandir,chgrp,chown,shell_exec,proc_open,proc_get_status,ini_alter,ini_alter,ini_restore,dl,openlog,syslog,readlink,symlink,popepassthru,stream_socket_server,escapeshellcmd,dll,popen,disk_free_space,checkdnsrr,checkdnsrr,getservbyname,getservbyport,disk_total_space,posix_ctermid,posix_get_last_error,posix_getcwd, posix_getegid,posix_geteuid,posix_getgid, posix_getgrgid,posix_getgrnam,posix_getgroups,posix_getlogin,posix_getpgid,posix_getpgrp,posix_getpid, posix_getppid,posix_getpwnam,posix_getpwuid, posix_getrlimit, posix_getsid,posix_getuid,posix_isatty, posix_kill,posix_mkfifo,posix_setegid,posix_seteuid,posix_setgid, posix_setpgid,posix_setsid,posix_setuid,posix_strerror,posix_times,posix_ttyname,posix_uname

#在386行 列出PHP可以禁用的函数，如果某些程序需要用到这个函数，可以删除，取消禁用。

expose_php = Off  #在432行 禁止显示php版本的信息

magic_quotes_gpc = On  #在745行 打开magic_quotes_gpc来防止SQL注入

short_open_tag = ON  #在229行支持php短标签

open_basedir = .:/tmp/  #在380行 设置表示允许访问当前目录(即PHP脚本文件所在之目录)和/tmp/目录,可以防止php木马跨站,如果改了之后安装程序有问题(例如：织梦内容管理系统)，可以注销此行，或者直接写上程序的目录/data/www.osyunwei.com/:/tmp/

:wq! #保存退出

/etc/init.d/mysqld restart #重启MySql

/etc/init.d/httpd restart #重启Apche

系统运维 www.osyunwei.com 温馨提醒：qihang01原创内容©版权所有,转载请注明出处及原文链

测试篇

cd /var/www/html

vi index.php #输入下面内容

<?php

phpinfo();

?>

:wq! #保存退出

在客户端浏览器输入服务器IP地址，可以看到如下图所示相关的配置信息！
