title: Slackware随机包pssh的使用
link: http://www.54chen.com/life/slackware%e9%9a%8f%e6%9c%ba%e5%8c%85pssh%e7%9a%84%e4%bd%bf%e7%94%a8.html
author: cc0cc
description: 
post_id: 268
created: 2008/12/10 10:54:30
created_gmt: 2008/12/10 02:54:30
comment_status: open
post_name: slackware%e9%9a%8f%e6%9c%ba%e5%8c%85pssh%e7%9a%84%e4%bd%bf%e7%94%a8
status: private
post_type: post

# Slackware随机包pssh的使用

## 难题

集群越来越大，越来越需要能同时并发访问很多台机器，一台台登录实在是太麻烦了，我们有没有办法能同时登录多台机器呢？ 

## [[编辑](http://10.62.164.49/wiki/index.php?title=Slackware%E9%9A%8F%E6%9C%BA%E5%8C%85pssh%E7%9A%84%E4%BD%BF%E7%94%A8&action=edit&section=2)] pssh & authorized_keys

方法是有的，只要我们结合 pssh （slackware随机附带）和ssh的authorized_keys，就可以给自己做一个很舒服的环境。 

### [[编辑](http://10.62.164.49/wiki/index.php?title=Slackware%E9%9A%8F%E6%9C%BA%E5%8C%85pssh%E7%9A%84%E4%BD%BF%E7%94%A8&action=edit&section=3)] 生成authorized_keys

在自己的工作 linux 机器里头，运行下面命令： 
    
    
      ssh-keygen #不带任何参数

这样在我们的家目录的.ssh/目录里头，会生成一组密钥，其中有一个 id_rsa.pub 的文件，是我们的公钥，这把公钥拷贝到我们想免口令登录的机器上去： 
    
    
      scp ~/.ssh/id_rsa.pub {某个远程机器}:.ssh/authorized_keys #耐心些，这是最后一次输入密码啦

### [[编辑](http://10.62.164.49/wiki/index.php?title=Slackware%E9%9A%8F%E6%9C%BA%E5%8C%85pssh%E7%9A%84%E4%BD%BF%E7%94%A8&action=edit&section=4)] 使用 pssh

比较方便的办法，是编辑一个文件，文件是一行一条记录，每行的格式如下：   
    
    
      某个远程的机器:ssh的端口 用户名

比如我的omniFe文件的内容如下： 
    
    
      ws1101.search.cnb:22 hewp
      ws1102.search.cnb:22 hewp
      ws1103.search.cnb:22 hewp
      ws1104.search.cnb:22 hewp
      ws1105.search.cnb:22 hewp
      ws1106.search.cnb:22 hewp
      ws1101.search.cng:22 hewp
      ws1102.search.cng:22 hewp
      ws1103.search.cng:22 hewp
      ws1104.search.cng:22 hewp
      ws1105.search.cng:22 hewp

#### [[编辑](http://10.62.164.49/wiki/index.php?title=Slackware%E9%9A%8F%E6%9C%BA%E5%8C%85pssh%E7%9A%84%E4%BD%BF%E7%94%A8&action=edit&section=5)] 运行命令

然后就可以运行命令啦，看看我的例子： 
    
    
      pssh -h ../conf/omniFe -i 'chmod 600 .ssh/authorized_keys'

如果需要输出命令运行结果，可以： 
    
    
      pssh -h ../conf/omniFe -i --print 'uptime'

看看，好玩不？ 

### [[编辑](http://10.62.164.49/wiki/index.php?title=Slackware%E9%9A%8F%E6%9C%BA%E5%8C%85pssh%E7%9A%84%E4%BD%BF%E7%94%A8&action=edit&section=6)] 命令参数

pssh 有个很简单的命令参数，不带任何选项运行pssh，就可以看到帮助列表： 
    
    
    Usage: pssh [OPTIONS] -h hosts.txt prog [arg0] ..
    
      -h --hosts   hosts file (each line "host[:port] [user]")
      -l --user    username (OPTIONAL)
      -p --par     max number of parallel threads (OPTIONAL)
      -o --outdir  output directory for stdout files (OPTIONAL)
      -e --errdir  output directory for stderr files (OPTIONAL)
      -t --timeout timeout in seconds to do ssh to a host (OPTIONAL)
      -v --verbose turn on warning and diagnostic messages (OPTIONAL)
      -O --options SSH options (OPTIONAL)
      -P --print   print output as we get it (OPTIONAL)
      -i --inline  inline aggregated output for each server (OPTIONAL)
    
    Example: pssh -h nodes.txt -l irb2 -o /tmp/foo uptime

当然，还有pscp（注意，不要和windows里putty的pscp混淆了）： 
    
    
    Usage: pscp [OPTIONS] -h hosts.txt local remote
    
      -r --recursive recusively copy directories (OPTIONAL)
      -h --hosts     hosts file (each line "host[:port] [login]")
      -l --user      username (OPTIONAL)
      -p --par       max number of parallel threads (OPTIONAL)
      -o --outdir    output directory for stdout files (OPTIONAL)
      -e --errdir    output directory for stderr files (OPTIONAL)
      -t --timeout   timeout in seconds to do scp to a host (OPTIONAL)
      -O --options   SSH options (OPTIONAL)
    
    Example: pscp -h hosts.txt -l irb2 foo.txt /home/irb2/foo.txt

## [[编辑](http://10.62.164.49/wiki/index.php?title=Slackware%E9%9A%8F%E6%9C%BA%E5%8C%85pssh%E7%9A%84%E4%BD%BF%E7%94%A8&action=edit&section=7)] TODO

修改pssh，让 -h 参数接受一个连接PG数据库表所生成的机器范围。 思路： 修改代码，把 -h 变成接受参数，然后查询数据库，生成一组指定配置格式相同的数据流。