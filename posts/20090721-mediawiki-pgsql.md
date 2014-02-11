title: [原创]mediawiki安装改进手记-使用mediawikit和postgresql搭建有强大搜索功能的wiki
link: http://www.54chen.com/php-tech/mediawiki-pgsql.html
author: cc0cc
description: 
post_id: 640
created: 2009/07/21 08:18:07
created_gmt: 2009/07/21 00:18:07
comment_status: open
post_name: mediawiki-pgsql
status: publish
post_type: post

# [原创]mediawiki安装改进手记-使用mediawikit和postgresql搭建有强大搜索功能的wiki

[ 文章作者：陈臻 本文版本：v1.1 最后修改：2009.7.28 转载请注明原文链接：<http://www.54chen.com/c/640> ] 在yahoo工程技术部，最有趣的事情当属打开工程技术部的wiki，慢慢阅读来自前辈们的大作。 yahoo以前使用的是twiki，后来更换了mediawiki，扩展了postgresql的搜索，使mediawiki的搜索功能得到了加强，这里，记录下我在校内搭建一整套的手记： **第一步.准备php和postgresql环境** 大环境：因为是内部系统，所以图省事，WEB使用的是一个[ubuntu](/c/372)的机器，安装这些丝毫不用费力了。就是先用apt安装php、nginx，源码包安装lighttpd取[spawn-cgi](http://www.54chen.com/c/640)一枚备用。源码包安装postgresql8.3.3。 #apt-get install php5 php5-pgsql php5-mbstring php5-cgi #apt-get install nginx #wget <http://www.lighttpd.net/download/lighttpd-1.4.19.tar.gz> #tar -xvf lighttpd-1.4.19.tar.gz #cd lighttpd-1.4.19/ #./configure #make #cp src/spawn-fcgi /usr/local/bin/spawn-fcgi (这里图快，没使用php-fpm) #adduser www #spawn-fcgi -a 127.0.0.1 -p 9000 -C 5 -u www-data -g www-data -f /usr/bin/php-cgi 修改php.ini文件 cgi.fix_pathinfo=1; 这样php-cgi方能正常使用SCRIPT_FILENAME这个变量 在/etc/nginx/sites-enable/default中添加以下代码 server { listen 80; //端口 server_name wiki.54chen.com; //虚拟域名 access_log /var/log/nginx/hwiki.54chen.com.access.log; //访问日志 location / { root /home/wiki.54chen.com; //size根目录 index index.php; } location ~ \\.php$ { #php fastcgi的配置 fastcgi_pass 127.0.0.1:9000; #php fastcgi的代理端口与ip fastcgi_index index.php; fastcgi_param SCRIPT_FILENAME /home/wiki.54chen.com$fastcgi_script_name; #要处理的php文件的路径 include /etc/nginx/fastcgi_params; #fastcgi的参数文件地址 } } 启动 
    
    
    /etc/init.d/nginx start

大功告成！ postgresql的安装请进[使用postgreSQL+bamboo搭建比lucene方便N倍的全文搜索 第一部分](/c/601) **第二步.安装mediawiki** #wget <http://downloads.sourceforge.net/sourceforge/wikipedia/mediawiki-1.8.2.tar.gz?use_mirror=jaist> #解压到/home/wiki.54chen.com 通过浏览器访问安装，安装时选择postgresql. 将wiki目录下connfig目录中的LocalSettings.php剪切到wiki目录中，把$wgEnableUploads = false;改为$wgEnableUploads = true;（这样允许上传图片了），还有就是把Wiki的Logo改成您所需要的$wgLogo = "$wgStylePath/common/images/wiki.png"。 **第三步.修改meidawiki及修改sql** 1.修改DB 按标题搜的触发器修改为 `CREATE OR REPLACE FUNCTION mediawiki.ts2_page_title() RETURNS "trigger" AS $BODY$ BEGIN IF TG_OP = 'INSERT' THEN NEW.titlevector = to_tsvector('chinesecfg',COALESCE(REPLACE(NEW.page_title,'/',' '),'')); ELSIF NEW.page_title != OLD.page_title THEN NEW.titlevector := to_tsvector('chinesecfg',COALESCE(REPLACE(NEW.page_title,'/',' '),'')); END IF; RETURN NEW; END; $BODY$ LANGUAGE 'plpgsql' VOLATILE; ALTER FUNCTION mediawiki.ts2_page_title() OWNER TO wikiuser;` 按内容搜索的触发器修改为 ` CREATE OR REPLACE FUNCTION mediawiki.ts2_page_text() RETURNS "trigger" AS $BODY$ BEGIN IF TG_OP = 'INSERT' THEN NEW.textvector = to_tsvector('chinesecfg',COALESCE(NEW.old_text,'')); ELSIF NEW.old_text != OLD.old_text THEN NEW.textvector := to_tsvector('chinesecfg',COALESCE(NEW.old_text,'')); END IF; RETURN NEW; END; $BODY$ LANGUAGE 'plpgsql' VOLATILE; ALTER FUNCTION mediawiki.ts2_page_text() OWNER TO wikiuser;` 对标题的索引修改 ` drop index ts2_page_title; CREATE INDEX ts2_page_title ON mediawiki.page USING gin (titlevector);` 对内容的索引修改 ` drop index ts2_page_text; CREATE INDEX ts2_page_text ON mediawiki.pagecontent USING gin (textvector);` 修改 includes/SearchPosgres.php 146行： `$prefix = $wgDBversion < 8.3 ? "'default'," : "'chinesecfg',";` 大功告成，搜索的时候就使用了bamboo的分词来搜了，如果还有兴趣可以加强175-179行的sql就能得到更加强大的wiki。

## Comments

**[fireonyou](#10668 "2009-07-24 08:50:04"):** good~~好东西 要顶

**[子江](#10676 "2009-07-28 10:24:29"):** 我觉得你把mediawiki修改完，发布一个自己的增强版吧 哈哈 反正开源

**[cc0cc](#10677 "2009-07-28 10:52:03"):** 没时间整理哪 有空可以弄一个。。。不知道media的人会不会BS我

**[fireonyou](#10679 "2009-07-28 23:36:59"):** 好东西，很少有人用pgsql

**[江江](#10683 "2009-07-29 08:57:49"):** 还是这个，同顶

