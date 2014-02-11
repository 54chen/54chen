title: 51ditu等网站技术揭秘-利用开源框架搭建一整套的WEBGIS
link: http://www.54chen.com/_linux_/51ditu-webgis-postgresql-postgregis.html
author: cc0cc
description: 
post_id: 387
created: 2009/01/08 17:25:04
created_gmt: 2009/01/08 09:25:04
comment_status: open
post_name: 51ditu-webgis-postgresql-postgregis
status: publish
post_type: post

# 51ditu等网站技术揭秘-利用开源框架搭建一整套的WEBGIS

[文章作者：陈臻 本文版本：v1.0 最后修改：2009.1.8 转载请注明原文链接：<http://www.54chen.com/c/387>] 感谢老早前李兄做的tech talk，一直有人在问及51ditu和mapbar什么的都怎么做的，老是记不住这些开源的东东都什么名字，特做下记录。 下图是一个完整的方案图： ![](/wp-content/uploads/2009/01/e59bbee789871.jpg) 其中所涉及的开源技术有： 

####   

软件名称
License 类型
编程 语言
运行环境
用途

PostGIS
GPL
Plpgsql
Postgresql
存储数据

MapServer
MapServer License
C++
UNIX like/ Windows
提供地图相关服务

TileCache
BSD
Python
All Platform
图片缓存

OpenLayers
BSD
Javascript
IE/Firefox
客户端展现控制

GDAL/OGR
MIT License
C++
UNIX like/ Windows
数据格式导换

#### 再简单解释下：

1.PostGis是PGSQL的一个扩展，在遵循OpenGIS规范下，提供空间对象、空间索引、空间操作函数和空间操作符等空间信息服务功能。 2.MapServer可以提供openGIS规范的各种接口。 3.MapServer根据参数中指定的路径读取mapfile文件和SLD文件。 4.使用SLD(Styled Layer Descriptors ), Mapfile控制地图显示样式。 5.根据SLD和mapfile的样式参数,配合GD(Graphics Library)进行的图形制作。 6.查询只与pgsql有关

## Comments

**[疏影](#118 "2009-01-08 17:26:58"):** 沙发

**[tsung](#119 "2009-01-08 17:32:22"):** 地板的跟上

**[d_yang](#390 "2009-04-07 16:41:32"):** 没太多的用。最重要的是数据。有数据，谁都能搞出来。。。

**[cc0cc](#393 "2009-04-07 16:57:58"):** 你个踢场子的地阳

**[d_yang](#396 "2009-04-07 17:28:23"):** 噢也。

**[lds](#13305 "2011-01-07 15:02:37"):** 地图是哪来的？

**[54chen](#13307 "2011-01-07 20:20:05"):** 有关部门有出售的，不过要关系。

**[Alpha](#11993 "2009-11-14 12:15:07"):** 零蛋，你的博客不错啊

**[cc0cc](#11996 "2009-11-14 20:28:45"):** 公务员呀，稀客稀客，来来来，屋里坐

