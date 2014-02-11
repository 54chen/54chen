title: PHP JAVA C上传文件如何准确判断文件类型－mime知识普及
link: http://www.54chen.com/php-tech/php-java-c-upload-files-how-to-accurately-determine-the-file-type-mime-literacy.html
author: cc0cc
description: 
post_id: 862
created: 2009/12/14 15:14:28
created_gmt: 2009/12/14 07:14:28
comment_status: open
post_name: php-java-c-upload-files-how-to-accurately-determine-the-file-type-mime-literacy
status: publish
post_type: post

# PHP JAVA C上传文件如何准确判断文件类型－mime知识普及

MIME的英文全称是"Multipurpose Internet Mail Extensions" 多功能Internet 邮件扩充服务，它是一种多用途网际邮件扩充协议，在1992年最早应用于电子邮件系统，但后来也应用到浏览器。服务器会将它们发送的多媒体数据的类型告诉 浏览器，而通知手段就是说明该多媒体数据的[MIME](/61-php%e2%80%9c%e6%94%b6%e5%8f%91%e2%80%9d%e9%82%ae%e4%bb%b6%e7%9a%84%e4%b8%80%e4%b8%aa%e7%a8%8b%e5%ba%8f/)类型，从而让浏览器知道接收到的信息哪些是MP3文件，哪些是Shockwave文件等等。服务器将 [MIME](http://www.54chen.com/61-php%e2%80%9c%e6%94%b6%e5%8f%91%e2%80%9d%e9%82%ae%e4%bb%b6%e7%9a%84%e4%b8%80%e4%b8%aa%e7%a8%8b%e5%ba%8f/)标志符放入传送的数据中来告诉浏览器使用哪种插件读取相关文件。 

文件
用IE7上传
用Firefox3.0上传

GIF

image/gif

image/gif

JPG

image/pjpeg

image/jpeg

ZIP
application/x-compressed
application/octet-stream

JSP

text/html

text/html

EXE
application/octet-stream
application/octet-stream
上边的黄图哦不红图表示出了不同的浏览器所上传的不同的[mime](/61-php%e2%80%9c%e6%94%b6%e5%8f%91%e2%80%9d%e9%82%ae%e4%bb%b6%e7%9a%84%e4%b8%80%e4%b8%aa%e7%a8%8b%e5%ba%8f/)的区别，研究了一下，暂时没有发现可修改假冒mime的方法。 

`<form **enctype="multipart/form-data"** action="URL" method="post"> <input type="hidden" name="MAX_FILE_SIZE" value="1000"> <input **name="myFile"** type="file"> <input type="submit" value="上传文件"> </form>`

这个代码上传文件后：

使用PHP:

$_FILES['myFile']['**type**']   文件的 MIME 类型，需要浏览器提供该信息的支持，例如"[image](/576-54%e9%99%88%e7%a7%91%e5%ad%a6%e9%99%a28021x%e6%ba%90%e4%bb%a3%e7%a0%81%e4%b8%8b%e8%bd%bd/)/gif"。

使用struts2限制类型：

<interceptor-ref name="fileUpload"> <param name="allowedTypes">application/zip,application/x-zip-compressed,application/rar,application/octet-stream</param> <param name="maximumSize">3145728</param> </interceptor-ref>

使用C/java（由[人人网](/801-54-chen-academy-schools-the-development-process-and-the-album-broke-the-heart-of-technical-analysis/)文斌大侠提供）：

**public** **static** **boolean** isImageJpeg(**byte**[] blob) {

## Comments

**[天缘](#12059 "2009-12-16 16:54:37"):** 每次遇到这个问题都是搜一下，博主下面的图片格式判断程序蛮好，拷贝收藏了。

**[cc0cc](#12070 "2009-12-22 23:50:15"):** 呵呵 拷贝的时候别发广告哈

