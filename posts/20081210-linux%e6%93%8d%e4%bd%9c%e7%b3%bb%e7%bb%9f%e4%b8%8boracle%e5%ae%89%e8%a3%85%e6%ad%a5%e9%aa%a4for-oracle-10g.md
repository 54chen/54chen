title: Linux操作系统下oracle安装步骤(for oracle 10g) 
link: http://www.54chen.com/life/linux%e6%93%8d%e4%bd%9c%e7%b3%bb%e7%bb%9f%e4%b8%8boracle%e5%ae%89%e8%a3%85%e6%ad%a5%e9%aa%a4for-oracle-10g.html
author: cc0cc
description: 
post_id: 338
created: 2008/12/10 14:08:49
created_gmt: 2008/12/10 06:08:49
comment_status: open
post_name: linux%e6%93%8d%e4%bd%9c%e7%b3%bb%e7%bb%9f%e4%b8%8boracle%e5%ae%89%e8%a3%85%e6%ad%a5%e9%aa%a4for-oracle-10g
status: private
post_type: post

# Linux操作系统下oracle安装步骤(for oracle 10g) 

* [Linux操作系统下oracle安装步骤(for oracle 10g)](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/view/PlatForm/InstallOracleForLinux#*Linux操作系统下oracle安装步骤\(for oracle 10g\)*)
    * [step1：添加组和用户](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/view/PlatForm/InstallOracleForLinux#*step1：添加组和用户*)
    * [step2：修改ssh配置参数值并重启](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/view/PlatForm/InstallOracleForLinux#*step2：修改ssh配置参数值并重启*)
    * [step3：从CVS(uppdev8上) CHECK OUT出所有的必需的软件和配置文件](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/view/PlatForm/InstallOracleForLinux#*step3：从CVS\(uppdev8上\) CHECK OUT出所有的必需的软件和配置文件*)
    * [step4：拷贝相应的配置文件](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/view/PlatForm/InstallOracleForLinux#*step4：拷贝相应的配置文件*)
    * [step5：相关软件安装](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/view/PlatForm/InstallOracleForLinux#*step5：相关软件安装*)
    * [step6：修改oracle用户的密码用客户端软件xmanager远程登陆服务器](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/view/PlatForm/InstallOracleForLinux#*step6：修改oracle用户的密码用客户端软件xmanager远程登陆服务器*)
    * [step7：在PC机打开的终端上执行解压并安装](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/view/PlatForm/InstallOracleForLinux#*step7：在PC机打开的终端上执行解压并安装*)
    * [step8：安装数据库](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/view/PlatForm/InstallOracleForLinux#*step8：安装数据库*)
    * [step9：oracle安装完成进行验证](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/view/PlatForm/InstallOracleForLinux#*step9：oracle安装完成进行验证*)

#### **step1：添加组和用户**

  * groupadd 
    1. groupadd -g 503 oinstall
    2. groupadd -g 501 dba
  * useradd 
    1. useradd -u 20643 -g oinstall -G oinstall,dba oracle
　　　说明: 此项是添加oracle用户,此用户可能在在新的机器中已经添加，所以可以免去。 

#### **step2：修改ssh配置参数值并重启**

  * 修改 /etc/ssh/sshd_config 使X11Forwarding可用，即：
    
    
    X11Forwarding yes

  * sudo /etc/init.d/sshd restart

#### **step3：从CVS(uppdev8上) CHECK OUT出所有的必需的软件和配置文件**

  * cvs co tools/opstool/oracle
　　oracle目录结构说明： 
    
    
    tools/opstool/oracle/conf
                       + --- sysctl.conf 
                               + --- .bash_profile 
                              + --- .bashrc
                              /software
                      + ---  oracle database 安装包：10201_database_linux32.zip
                              + ---  xmanager安装包：xmanager-v139e.rar
                             /xterm下xterm安装包： 可以直接使用 yinst  i ports/xterm 安装 
                      + --- xterm-179-5.EL.i386.rpm(For 32bit)
                             + --- xterm-179-5.EL.x86_64.rpm(For 64bit)
                             + --- xterm-192-1.x86_64.rpm(For 64bit)

#### **step4：拷贝相应的配置文件**

  * 拷贝.bash_profile .bashrc到/home/oracle下
  * 拷贝sysctl.conf 到 /etc/下

#### **step5：相关软件安装**

  * 在服务器端安装xterm（32位or 64位）
    
    
    sudo rpm –ivh packagename

  * 在自己的PC机上安装客户端软件xmanager.

#### **step6：修改oracle用户的密码用客户端软件xmanager远程登陆服务器**

  * 修改oracle用户的密码 说明：此密码修改后须立即使用，系统一定时间后会自动更新此密码，当然可以重复设置此密码。
  * 运行PC客户端软件Xstart 进行远程连接，此刻将开启一个终端。

#### **step7：在PC机打开的终端上执行解压并安装**

  * unzip 10201_database_linux32.zip 说明: 解压前将此安装包拷贝到/home/oracle下
  * 开始安装
    
    
    ./database/runInstaller

#### **step8：安装数据库**

  * 同样在PC上运行Xstart进行远程连接并打开一个终端
  * 在终端上运行命令：dbca

#### **step9：oracle安装完成进行验证**

  * 配置.bash_profile 中的环境变量
    
    
    ORACLE_HOME(根据oracle当时的安装路径配置,默认为：home/oracle/oracle/product/10.2.0/db_1)
    ORACLE_SID（在安装数据库时要求填写的SID）

  * 执行命令进行验证
    
    
    bash-2.05b$ sqlplus / as sysdba
    SQL> select username from all_users;

                    \-- [ZhangZhigang](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/view/Main/ZhangZhigang) \- 24 Apr 2007