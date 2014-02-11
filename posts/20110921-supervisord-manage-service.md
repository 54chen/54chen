title: 用supervisord管理杂乱的服务
link: http://www.54chen.com/_linux_/supervisord-manage-service.html
author: 54chen
description: 
post_id: 1831
created: 2011/09/21 14:57:24
created_gmt: 2011/09/21 06:57:24
comment_status: open
post_name: supervisord-manage-service
status: publish
post_type: post

# 用supervisord管理杂乱的服务

![supervisord](http://supervisord.org/_static/logo_hi.gif) Supervisord是用Python实现的一款非常实用的进程管理工具，在批量服务化管理时特别有效。 **环境** centos python2.4 **安装 ** wget http://pypi.python.org/packages/source/s/supervisor/supervisor-3.0a10.tar.gz#md5=99c6fbd45bade87301296b7a597fb68e tar zxvf supervisor-3.0a10.tar.gz cd supervisor-3.0a10 python setup.py install 如果上述遇到问题，请按如下操作。 **依赖setuptools安装** wget http://pypi.python.org/packages/2.4/s/setuptools/setuptools-0.6c11-py2.4.egg#md5=bd639f9b0eac4c42497034dec2ec0c2b sh setuptools-0.6c11-py2.4.egg **产生基础配置文件** echo_supervisord_conf > /etc/supervisord.conf **配置** [inet_http_server]前面的分号去掉; 定义port=192.168.1.1:8888 **启动** supervisord -c /etc/supervisord.conf **查看** 浏览器打开 http://192.168.1.1:8888 ** 深入配置**

> [program:service] command=sh /opt/soft/service/run.sh ; the program (relative uses PATH, can take args) process_name=%(program_name)s ; process_name expr (default %(program_name)s) numprocs=1 ; number of processes copies to start (def 1) directory=/opt/soft/service/ ; directory to cwd to before exec (def no cwd) autostart=true ; start at supervisord start (default: true) autorestart=true ; retstart at unexpected quit (default: true) stopsignal=QUIT ; signal used to kill process (default TERM) stopwaitsecs=10 ; max num secs to wait b4 SIGKILL (default 10)

以上配置了一个shell脚本的启动和stop过程。 一般java程序都喜欢在一个shell脚本内初始化classpath，再执行java main。 直接在shell中run java会出现stop失败的情况，在启动前添加 exec即可。 

> #!/bin/sh export CLASSPATH=. for jarpath in `ls library/*.jar` do CLASSPATH=$CLASSPATH:$jarpath done export CLASSPATH=$CLASSPATH exec java com.chen.main; 

** 帮助**

> supervisord，初始启动Supervisord，启动、管理配置中设置的进程。 supervisorctl stop programxxx，停止某一个进程(programxxx)，programxxx为[program:chatdemon]里配置的值，这个示例就是chatdemon。 supervisorctl start programxxx，启动某个进程 supervisorctl restart programxxx，重启某个进程 supervisorctl stop all，停止全部进程，注：start、restart、stop都不会载入最新的配置文件。 supervisorctl reload，载入最新的配置文件，并按新的配置启动、管理所有进程。 supervisorctl reread，当一个服务由自动启动修改为手动启动时执行一下就ok

**如何添加进程不启动所有的服务进程**

> 1）修改/etc/supervisord.conf 2）supervisorctl reread 3）supervisorctl add xxservice 

**如何删除进程不启动所有的服务进程**

> 1）修改/etc/supervisord.conf 2）supervisorctl reread 3）supervisorctl update 

http://smarden.org/runit/runscripts.html 一些可参考的启动配置 **supervisord开机自启动** chkconfig --add supervisord #加为服务 运行ntsysv，选中supervisord启动系统时跟着启动。 [/etc/rc.d/init.d/supervisord] 

> #!/bin/sh # # /etc/rc.d/init.d/supervisord # # Supervisor is a client/server system that # allows its users to monitor and control a # number of processes on UNIX-like operating # systems. # # chkconfig: - 64 36 # description: Supervisor Server # processname: supervisord # Source init functions . /etc/rc.d/init.d/functions prog="supervisord" prefix="/usr/" exec_prefix="${prefix}" prog_bin="${exec_prefix}/bin/supervisord" PIDFILE="/var/run/$prog.pid" start() { echo -n $"Starting $prog: " daemon $prog_bin --pidfile $PIDFILE [ -f $PIDFILE ] && success $"$prog startup" || failure $"$prog startup" echo } stop() { echo -n $"Shutting down $prog: " [ -f $PIDFILE ] && killproc $prog || success $"$prog shutdown" echo } case "$1" in start) start ;; stop) stop ;; status) status $prog ;; restart) stop start ;; *) echo "Usage: $0 {start|stop|restart|status}" ;; esac 

**注意 ** 不带参数运行supervisord是以daemon方式运行。 把supervisord加入到开机启动项里就可以完成监控进程的功能了。 当supervisord以非daemon方式运行时，杀掉supervisord后，被监控的进程也退出了。 而以daemon方式运行，杀掉supervisord对被监控进程无影响。

## Comments

**[higkoo](#15673 "2013-09-21 12:00:45"):** 好东西！

