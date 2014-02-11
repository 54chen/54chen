title: PHP上传进度条深度解析
link: http://www.54chen.com/php-tech/php-upload-progress.html
author: cc0cc
description: 
post_id: 1074
created: 2010/04/18 18:32:09
created_gmt: 2010/04/18 10:32:09
comment_status: open
post_name: php-upload-progress
status: publish
post_type: post

# PHP上传进度条深度解析

随着互联网的发展，越来越多的技术开始注重用户体验，以人为本才是长久之道，于是在上传的时候，大家都不再满足一个单一的“浏览”按钮，纷纷推出了带上传进度条的功能。而作为解释型语言的PHP，如何做到对上传文件的检测，如何实现上传进度条以其背后的原理，54chen将在本文中一步步展开。 **一. 实现篇** 一般情况，用PHP实现上传进度条就下面两种方法： 1.APC扩展（作者是PHP的创始人，5.2后PHP已经加入APC扩展） 2.PECL扩展模块 uploadprogress 不论是APC还是uploadprogress，都需要编译源码，因为原有的PHP函数根本不可能读取到临时文件夹里的东西。下面来看如何使用以及关键的代码： 

**APC实现方法:**

1.安装APC

2.配置php.ini，设置参数 apc.rfc1867=1

3.关键代码：

> if ($_SERVER['REQUEST_METHOD'] == 'POST') {  //上传请求
> 
> $status = apc_fetch('upload_' . $_POST['APC_UPLOAD_PROGRESS']);
> 
> $status['done'] = 1;
> 
> echo json_encode($status);  //输出给用户端页面里的ajax调用，相关文档请自己寻找
> 
> exit;
> 
> } elseif (isset($_GET['progress_key'])) {   //读取上传进度
> 
> $status = apc_fetch('upload_'.$_GET['progress_key']);
> 
> echo json_encode($status);
> 
> exit;
> 
> }

**uploadprogress实现方法：**

1.使用PECL 安装uploadprogress

2.php.ini里面设置 uploadprogress.file.filename_template = “/tmp/upd_%s.txt”

3.关键代码：

> if($_SERVER['REQUEST_METHOD']=='POST') {
> 
> if (is_uploaded_file($_FILES['upfile']['tmp_name'])) {
> 
> $upload_dir = 'your_path/';
> 
> $ext        = strrchr($_FILES['video']['name'], '.');
> 
> $sessid     = $_POST['UPLOAD_IDENTIFIER'] ;
> 
> $tmpfile    = $upload_dir . $sessid;
> 
> $sessfile   = $upload_dir . $sessid .$ext;
> 
> if (move_uploaded_file($_FILES['upfile']['tmp_name'],$tmpfile)) {
> 
> //上传成功
> 
> }
> 
> }
> 
> } elseif (!empty($_GET['sessid'])) {
> 
> header("Expires: Mon, 26 Jul 1997 05:00:00 GMT");
> 
> header("Last-Modified: " . gmdate("D, d M Y H:i:s") . " GMT");
> 
> header("Cache-Control: no-store, no-cache, must-revalidate");
> 
> header("Cache-Control: post-check=0, pre-check=0", false);
> 
> header("Pragma: no-cache");
> 
> header("Content-Type:text/html;charset=UTF-8");
> 
> $unique_id = $_GET['sessid'];
> 
> $uploadvalues = uploadprogress_get_info($unique_id);
> 
> if (is_array($uploadvalues)) {
> 
> echo json_encode($uploadvalues);
> 
> } else {
> 
> //读取进度失败，另外处理逻辑
> 
> }
> 
> }

**二. 原理篇 ** 注意上一篇中的红色函数。 下载到uploadprogress1.0.1进行源码分析，在代码中作了注释。 

> static void uploadprogress_file_php_get_info(char * id, zval * return_value) { char s[1024]; char * filename; char * template; FILE *F; TSRMLS_FETCH(); template = INI_STR("uploadprogress.file.filename_template"); <<这里读取设置好的模板 if (strcmp(template, "") == 0)  { return; } else { filename = uploadprogress_mk_filename( id, template );<<<存在的话，会创建 if (!filename) return; F = VCWD_FOPEN(filename, "rb"); if (F) { array_init(return_value); while ( fgets(s, 1000, F) ) {<<<从流中读取一字符串 *s结果数据的首地址；1000-1:一次读入数据块的长度,其默认值为1k，即1024;F文件指针 char *k, *v, *e; int index = 0; e = strchr(s,'='); <<<查找字符串s中首次出现字符=的位置 if (!e) continue; *e = 0; /* break the line into 2 parts */ v = e+1; k = s; /* trim spaces in front of the name/value */ while (*k && *k <= 32) k++; while (*v && *v <= 32) v++; /* trim spaces everywhere in the name */ for (e=k; *e; e++) if (*e <= 32) { *e = 0; break; } /* trim spaces only at the end of the value */ /* http://pecl.php.net/bugs/bug.php?id=14525 */ //for (e=v; *e; e++) if (*e <= 32) { *e = 0; break; } if (v != NULL) {<<<当文件有内容时 for (index = strlen(v); index > 0; index--) { if (v[index] > 32) break;<<<累计 v[index] = 0; } } add_assoc_string( return_value, k, v, 1 ); } fclose(F); } if (filename) efree(filename); return; } }

在源码中还能发现: 

> PHP_MINIT_FUNCTION(uploadprogress) { REGISTER_INI_ENTRIES(); php_rfc1867_callback = uploadprogress_php_rfc1867_file; return SUCCESS; }

在MINIT中修改了php_rfc1867_callback，抽取uploadprogress_php_rfc1867_file的关键代码： 

> upload_id = emalloc(strlen(*e_data->value) + 1); strcpy(upload_id, *e_data->value); progress->upload_id = upload_id; progress->time_last  = time(NULL); progress->speed_average  = 0; progress->speed_last     = 0; progress->bytes_uploaded = read_bytes; progress->files_uploaded = 0; progress->est_sec        = 0; progress->identifier = uploadprogress_mk_filename(upload_id, template);<<<在指定的模板位置放下了临时文件 progress->identifier_tmp = emalloc(strlen( progress->identifier) + 4); sprintf( progress->identifier_tmp, "%s.wr", progress->identifier );

关于php_rfc1867_callback是什么，可以看哥学社另一哥鸟哥的分析 <http://www.laruence.com/2008/11/07/586.html> **三.总结** 在探测临时文件大小的时候，APC和 uploadprogress其实是大同小异的方法，先记录，再取大小百分比。 关于哥学社： [哥学社](/blog-brother)是一个自由博客组织，以提高原创技术博客质量，为成员博客增加更有效评论，由来自腾讯、百度、盛大、人人网、新浪、奇虎、TOM等各大企业的哥们共同起草发起。 关于五四陈： 54chen(陈臻)，人人网分布式存储研究人员，业余时间混迹于各技术组织且乐此不疲。目前关注实施PHP培训。对flex等前端技术有一点研究。 个人技术站点:<http://www.54chen.com/> 。可以通过电子邮件 czhttp@gmail.com 联系到他。 本文提及的文章： <http://www.php.net/releases/5_2_0.php> <http://www.ibm.com/developerworks/cn/opensource/os-php-v525/> <http://www.laruence.com/2008/11/07/586.html>

## Comments

**[pfeng](#12254 "2010-04-22 08:47:46"):** 额，gmail里的附件上传进度条处理的不错。

**[pfeng](#12255 "2010-04-22 08:49:12"):** 另外，五四陈，麻烦交换个链接，我的刚起步，需要你的照顾啊！我的blog:www.pfeng.org

