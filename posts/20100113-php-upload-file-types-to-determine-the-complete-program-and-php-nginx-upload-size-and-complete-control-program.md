title: PHP上传文件类型彻底判断方案及PHP＋nginx上传大小彻底控制方案
link: http://www.54chen.com/php-tech/php-upload-file-types-to-determine-the-complete-program-and-php-nginx-upload-size-and-complete-control-program.html
author: cc0cc
description: 
post_id: 925
created: 2010/01/13 09:06:21
created_gmt: 2010/01/13 01:06:21
comment_status: open
post_name: php-upload-file-types-to-determine-the-complete-program-and-php-nginx-upload-size-and-complete-control-program
status: publish
post_type: post

# PHP上传文件类型彻底判断方案及PHP＋nginx上传大小彻底控制方案

上回科学院发过一篇讲述上传判断的文章，位置是 

### [PHP JAVA C上传文件如何准确判断文件类型－mime知识普及](../cc/php-java-c-upload-files-how-to-accurately-determine-the-file-type-mime-literacy.html)

本文目的在于，进一步更正前文所述的mime判断方式，以及增加一个nginx环境里的文件上传大小所影响的代码。 **上传类型控制：** 在我（[54chen]()）工作中发现，其实修改文件的后缀，浏览器就会很傻瓜地传送错误的[mime](http://www.54chen.com/_linux_/everyone-network-intel-technology-symposium-record.html)类型，所以前文的判断是一个半错误的方法（除了C代码是正确的）。 网上流传一段PHP读取文件头判断文件类型的方法，有一些bug，经我（[54chen](http://www.54chen.com/php-tech/mediawiki-pgsql.html)）修改实测，应该是这个样子： 

> /** * 读取文件前几个字节 判断[文件](/webfe/academy-of-sciences-in-recent-years-a-good-article-summing-up.html)类型 * * @return String */ function checkTitle($filename) { $file     = fopen($filename, "rb"); $bin      = fread($file, 2); //只读2字节 fclose($file); $strInfo  = @unpack("c2chars", $bin); $typeCode = intval($strInfo['chars1'].$strInfo['chars2']); $fileType = ''; switch ($typeCode) { case 7790: $fileType = 'exe'; break; case 7784: $fileType = 'midi'; break; case 8297: $fileType = 'rar'; break; case 255216: $fileType = 'jpg'; break; case 7173: $fileType = 'gif'; break; case 6677: $fileType = 'bmp'; break; case 13780: $fileType = 'png'; break; default: $fileType = 'unknown'.$typeCode; } //Fix if ($strInfo['chars1']=='-1' && $strInfo['chars2']=='-40' ) { return 'jpg'; } if ($strInfo['chars1']=='-119' && $strInfo['chars2']=='80' ) { return 'png'; } return $fileType; }

这代码可以非常正确地分出修改后的文件，从而达到阻止修改后缀名上传的情况。 **上传大小控制：** 在PHP代码中直接读取$_FILE的size，而如果是特别大的文件，又使用了PHP＋[nginx](/architecture/54-chen-skills-the-whole-point-for-domain-names-using-nginx-and-javascript-to-do-a-simple-jump-to-inform-the-friendly-exchange-of-domain-names.html)的话，很有可能，超过2M的文件直接就被nginx就抛出来413错误了。 解决的办法： 修改/etc/nginx/nginx.conf 找到对应域名的[server](http://www.54chen.com/architecture/wordpress-nginx-php-cgi-mysql-memory-in-the-128m-to-install-and-optimize-the-vps-notes.html)段：修改client_max_body_size的值，默认是2M。 这样子还不够，如果不修改php.ini里的值，你会发现上传的文件用上面的代码判断类型的时候会出问题。 修改/etc/php.ini 找到upload_max_[filesize](http://www.54chen.com/cc/php-java-c-upload-files-how-to-accurately-determine-the-file-type-mime-literacy.html)，修改这个值，默认是2M。 一番折腾，类型和大小的控制基本上就完善了。 

## **重要PS：**

对于上传文件类型的判断，一直没有太好的办法，即使使用上面的代码，也有办法构造假的图片的（如何构造不再传播），有人使用getimagesize来判断，不失为一种好办法： if(in_array($attach['ext'], array('jpg', 'jpeg', 'gif', 'png', 'swf', 'bmp')) && function_exists('getimagesize') && [!@getimagesize($target](mailto:!@getimagesize\($target))) {     @unlink($target);     upload_error('post_attachment_ext_notallowed', $attacharray);    } 摘自dz代码。 函数参考 <http://cn2.php.net/getimagesize>

## Comments

**[franzcai](#12413 "2010-06-04 15:37:27"):** @unpack(”c2chars”, $bin); 这句如果是@unpack(”C2chars”, $bin);应该会得出137,80 255,216 这两个结果

**[changkang](#15494 "2013-04-24 16:40:21"):** mime_content_type 这个函数是读取的前几个字节， 举个例子： 我把一个 XXX.jpg 的图片改为 XXX.rar mime_content_type(XXX.rar) 返回的结果就是 image/jpg

**[54chen](#13298 "2011-01-04 09:58:10"):** mime_content_type也是读取的前几个字节？dz里用获取长度和宽度来判断是否图片，是个好办法，不过也有办法破。

**[laruence](#13295 "2010-12-30 15:12:28"):** 为啥不用mime_content_type呢?

