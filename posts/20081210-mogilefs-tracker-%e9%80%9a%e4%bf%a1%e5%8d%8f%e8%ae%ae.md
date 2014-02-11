title: MogileFS Tracker 通信协议 
link: http://www.54chen.com/life/mogilefs-tracker-%e9%80%9a%e4%bf%a1%e5%8d%8f%e8%ae%ae.html
author: cc0cc
description: 
post_id: 322
created: 2008/12/10 13:30:10
created_gmt: 2008/12/10 05:30:10
comment_status: open
post_name: mogilefs-tracker-%e9%80%9a%e4%bf%a1%e5%8d%8f%e8%ae%ae
status: private
post_type: post

# MogileFS Tracker 通信协议 

### 协议简介

[MogileFS](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/view/PlatForm/MogileFS) 的 Tracker 同 Client 之间的通信协议是一种纯文本协议，基于典型的 Server-Client 交互模式。Client 向 Tracker 发送一条合法的命令及对应参数，Tracker 返回对应的响应结果。为了使接口数据格式尽量一致，该协议中所有命令参数和响应结果都是以 HTTP GET 形式进行 URL encoding 的（非英文字符用其 UTF-8 字节序列进行编码），参数名和参数值之间以“=”分隔，不同参数之间以“&”分隔，参数名区分大小写（在 `MogileFS::Worker::Query::decode_url_args()` 方法中没有改变参数名）；命令本身仅由字母和下划线组成，不区分大小写（在 `MogileFS::Worker::Query::process_line()` 方法中统一转换为小写）；命令和其参数之间以空格分隔。合法命令的例子如下： 
    
    
    CREATE_OPEN domain=www.yahoo.com.cn&key=/album/wxz/a%23b+c.jpg&class=image
    get_paths domain=www.yahoo.com.cn&key=/album/wxz/a%23b+c.jpg

命令响应结果格式为： 
    
    
    OK _返回参数列表(可选, 经过 URL encoding)_
    ERR **错误代号(简短的单词)** **错误信息(经过 URL encoding)**

响应结果的例子如下： 
    
    
    OK
    OK devid=2&fid=43749&path=http://10.62.162.120:8080/dev2/vol0/0/000/043/0000043749.fid
    ERR unknown_command Unknown+server+command

  

### Tracker 可识别的命令

#### 文件操作相关命令

  * **CREATE_OPEN**
    * _功能_ ：获得新建文件所需的存储位置信息，存储位置由系统内所有磁盘设备的权重决定，权重最高的设备最有可能被选中。处理该请求时还会预先创建每个存储位置对应的后端 WebDAV[?](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/edit/PlatForm/WebDAV?topicparent=PlatForm.MogileFSTrackerProtocol) 路径，以保证客户端 PUT 数据时对应的前缀路径都已存在（该操作通过调用 `MogileFS::DevFID::vivify_directories()` 完成）。
    * _参数_ ： 
      * **domain** ：新建文件所属的 domain 字符串。最终文件所属的域由 `CREATE_CLOSE` 命令的 domain 参数决定。
      * **class** ：(opt.)新建文件所属的 class 字符串，省略该参数时表示使用给定 domain 内的默认存储类别。
      * **key** ：(opt.)新建文件的 key（可理解为文件名），该参数会被列在临时文件表中，仅有方便人工维护的意义，不影响具体的文件操作，因此可以安全地省略。最终保存的文件名是由 `CREATE_CLOSE` 命令的 key 参数给定的。
      * **multi_dest** ：(opt.)返回最多 3 个（选择策略在 `MogileFS::Worker::Query::cmd_create_open()` 方法中的 find_deviceid 部分实现）可能的存储位置供客户端自行选择，这样当第一个存储位置对应的节点突然下线时客户端可以不再查询 Tracker 直接尝试其他位置。
      * **fid** ：(opt.)期望使用的文件 fid ，省略该参数时表示让系统自行生成文件的 fid。
      * **debug_profile** ：(opt.)响应结果是否包括性能测试数据。
    * _响应结果_ ：操作失败时返回 
        
                ERR no_domain No+domain+provided（没有提供 domain 参数）
        ERR unreg_domain Domain+name+invalid/not+found（给定的 domain 不存在）
        ERR plugin_aborted Action+aborted+by+plugin（当前执行的命令被 cmd_create_open 插件回调函数取消）
        ERR unreg_class unreg_class（给定的 class 不存在）
        ERR no_devices No+devices+found+to+store+file（找不到能够存储新文件的磁盘设备）
        ERR fid_in_use fid_in_use（期望使用的 fid 已经被其他用户占用）
        ERR db Database+error（元数据数据库操作错误）

操作成功时返回 
        
                OK _新文件相关信息_

其中相关信息为： 
        
                **fid**=_新建文件的fid_
        **prof_x_name**=_性能测试点x的名称，仅在指定了 debug_profile 参数时出现，这里的x为>=1的整数_
        **prof_x_time**=_性能测试点x耗费的时间，以秒为单位的浮点数，仅在指定了 debug_profile 参数时出现，这里的x为>=1的整数_
        **dev_count**=_可能的存储位置总数，仅在指定了 multi_dest 参数时出现_
        **devid_y**=_可能的存储磁盘设备ID，仅在指定了 multi_dest 参数时出现，这里的y为[1,dev_count]范围内的整数_
        **path_y**=_可能的存储位置URL，同相同y的磁盘设备ID对应，仅在指定了 multi_dest 参数时出现，这里的y为[1,dev_count]范围内的整数_
        **devid**=_可能的存储磁盘设备ID，仅在未指定 multi_dest 参数时出现_
        **path**=_可能的存储位置URL，仅在未指定 multi_dest 参数时出现_

  * **CREATE_CLOSE**
    * _功能_ ：告知 Tracker 文件保存完毕可以关闭，删除给定 fid 对应的老文件的元数据（实际数据的删除会延后进行）并触发新数据的冗余操作。若保存完毕的文件为匿名临时文件，则该文件关闭时即被删除。
    * _参数_ ： 
      * **domain** ：保存完毕的文件所属的 domain 字符串，文件所属域完全由这里的值确定，会覆盖掉 `CREATE_OPEN` 时给定的域。
      * **fid** ：保存完毕的文件对应的 fid。
      * **devid** ：保存完毕的文件所在的磁盘设备 ID。
      * **path** ：保存完毕的文件所在的存储位置 URL。
      * **key** ：(opt.)保存完毕的文件对应的 key，若新建文件需要持久保存则这里应该给出文件名，若新建的文件为临时文件则不必给出该参数，此时文件数据会被直接删除。
      * **size** (opt.)保存的文件长度，若给出该参数则系统会进行存储位置处的文件长度检查。
      * **mimetype** (opt.)保存的文件默认MIME type，若给出该参数则会被作为文件的默认类型保存下来，若不给出则默认为 `application/octet-stream` 。（**在 YLEM Tracker 中新增**）
    * _响应结果_ ：操作失败时返回 
        
                ERR no_domain No+domain+provided（没有提供 domain 参数）
        ERR unreg_domain Domain+name+invalid/not+found（给定的 domain 不存在）
        ERR bogus_args bogus_args（给定的 path 地址同 devid 和 fid 组合出来的结果不相符）
        ERR no_temp_file no_temp_file（没有同给定的 fid 对应的临时文件记录，fid 无效）
        ERR size_verify_error Expected:+...%3B+actual:+0+%28...%29%3B+path:+...%3B+error:+...（文件长度检测失败，给定了 size 参数，但给定 key 对应的文件长度无法检测到，可能是文件不存在）
        ERR size_mismatch Expected:+...;+actual:+...;+path:+...（文件长度校验错误，给定的 size 参数同实际检测到的文件长度不同）
        ERR empty_file empty_file（未给定 size 参数时，给定 key 对应的文件长度检测不到或长度为 0）
        ERR plugin_aborted Action+aborted+by+plugin（本次操作被 file_stored 插件回调函数取消，新建文件被删除）
        ERR db_error db_error（更新存储设备上的文件计数时发生数据库错误）

操作成功时返回 
        
                OK