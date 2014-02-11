title: mg插件MetaData? 插件 
link: http://www.54chen.com/life/mg%e6%8f%92%e4%bb%b6metadata-%e6%8f%92%e4%bb%b6.html
author: cc0cc
description: 
post_id: 332
created: 2008/12/10 14:00:25
created_gmt: 2008/12/10 06:00:25
comment_status: open
post_name: mg%e6%8f%92%e4%bb%b6metadata-%e6%8f%92%e4%bb%b6
status: private
post_type: post

# mg插件MetaData? 插件 

### 功能描述

MetaData[?](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/edit/PlatForm/MetaData?topicparent=PlatForm.MogileFSPluginMetaData)插件为特定fid提供了基于key-value形式的附加数据存储。使用者在插入新文件的时候，可以附加的存储和此文件相关的一些其它信息，如文件类型，文件标识等等。这些信息和fid是关联的，通过fid可以获得这些信息，也即是让每一个fid对应了一个hash表，表中存储fid的相关信息。 

### 提供接口

MetaData[?](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/edit/PlatForm/MetaData?topicparent=PlatForm.MogileFSPluginMetaData)提供了以下接口： 

#### delete_metadata

  * 提供参数：fid[in]
  * 提供功能：根据fid删除其对应的hash表

#### get_metadata

  * 提供参数：fid[in]
  * 提供功能：根据fid获得其对应的hash表，返回的是一个key-value数组

#### set_metadata

  * 提供参数：fid[in]，meta_by_name[in]：key-value数组
  * 提供功能：将key-value数组插入到fid对应的hash表

### 在系统中的应用

在FilePaths中使用了此模块，当插入一个新文件时，接口提供了一个hash表参数，使用者可以在此参数中加入一些附加值 

### 所涉及的数据存储

fid对应的hash表被存储在单独的表中，这些表是由MetaData模块创建的，它是独立于MofileFS系统的。以下是表创建脚本： 
    
    
    CREATE TABLE plugin_metadata_names (
    nameid BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    PRIMARY KEY (nameid)
    )
    CREATE TABLE plugin_metadata_data (
    fid BIGINT UNSIGNED NOT NULL,
    nameid BIGINT UNSIGNED NOT NULL,
    data VARCHAR(255) NOT NULL,
    PRIMARY KEY (fid, nameid)
    )
    
    
    
    
    ## FilePaths[?](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/edit/PlatForm/FilePaths?topicparent=PlatForm.MogileFSPluginFilePaths) 插件分析
    
    
    
    
    ### 功能描述
    
    
    提供了基于全路径的文件存储，用户在调用插入新文件时，可以提供类似于/home/tom/var/test.dat的key格式；在获取文件时，可以提供/home/tom/var/test.dat的key格式，从而获得此文件，也即是基于全路径来管理文件的存储。此外，还可以根据/home/tom/var获得此目录下的所有文件，从而使文件有了目录的概念，方便基于文件夹的文件查询和管理。
    
    
    ### 原理分析
    
    
    此插件和MetaData插件一样，是独立于MogileFS的。FilePaths 
    插件的实现相当于在MogileFS系统上封装了一下层，通过在数据库中记录path和fid的对应关系，从而实现根据path找到fid的功能。
    举例说明，针对一个全路径文件/home/tom/var/test.dat，系统将其分割成home、tom、var、test.dat四个字段，每个字段都将生产一条节点记录，根据顺序，上一个节点是下一个节点的父节点，这些节点记录将保存在FilePaths插件创建的数据表中，并将文件插入所获得的fid更新到节点记录中。当需要查询一个目录下的所有文件时，根据目录获得该节点ID，然后查找所有以该节点ID为父节点的所有节点。
    
    
    ### 提供接口
    
    
    
    
    #### cmd_create_open
    
    
    
    
    
    	
      * 提供参数：dmid[in]、key[in]:全路径文件
    
    	
      * 提供功能：在主命令cmd_create_open中被调用，仅仅实现相关参数的合法性检查而已
    
    
    
    
    #### cmd_create_close
    
    
    
    
    
    	
      * 提供参数：dmid[in]、key[in/out]:全路径文件、fid[in]
    
    	
      * 提供功能：在主命令cmd_create_close中被调用，将原key赋值给logical_path，并将"fid:$fid"赋值给key
    
    
    
    
    #### file_stored
    
    
    
    
    
    	
      * 提供参数：dmid[in]、logical_path[in]:全路径文件、key[in]、fid[in]
    
    	
      * 提供功能：在主命令create_close中被调用，具体流程图如下：
    ![plugin_file_store.jpg](http://twiki.corp.cnb.yahoo.com:9999/twiki/pub/PlatForm/MogileFSPluginFilePaths/plugin_file_store.jpg)
    
    
    
    
    #### cmd_get_paths
    
    
    
    
    
    	
      * 提供参数：dmid[in]、key[in]:全路径文件
    
    	
      * 提供功能：在主命令get_paths中被调用，根据全路径文件获得对应的fid，就是通过插件数据表的映射获得key(fid:$fid)
    
    
    
    
    #### cmd_delete
    
    
    
    
    
    	
      * 提供参数：dmid[in]、key[in]:全路径文件
    
    	
      * 提供功能：在主命令delete中被调用，在插件数据表中删除全路径对应的文件，主命令中完成实际fid的删除
    
    
    
    
    #### filepaths_enable
    
    
    
    
    
    	
      * 提供参数：dmid[in]
    
    	
      * 提供功能：直接响应命令，让dmid下的filepaths插件可用
    
    
    
    
    #### filepaths_disable
    
    
    
    
    
    	
      * 提供参数：dmid[in]
    
    	
      * 提供功能：直接响应命令，让dmid下的filepaths插件可用
    
    
    
    
    #### filepaths_list_directory
    
    
    
    
    
    	
      * 提供参数：dmid[in]、arg1[in]:路径名称，必须以/开头
    
    	
      * 提供功能：直接响应命令，获得路径下的所有节点列表，节点以file$i开头，file$i标识文件名、file$i.type表示节点类型:D-文件夹、F-文件，可以根据文件名进一步获得文件...
    
    
    
    
    #### filepaths_rename
    
    
    
    
    
    	
      * 提供参数：
    
    	
      * 提供功能：直接响应命令，路径重命名，就是更改插件数据库而已
    
    
    
    
    ### 在系统中应用
    
    
    可以以此来引入文件夹概念，有利于查找
    
    
    ### 数据表设计
    
    
    这些表是由FilePaths模块创建的，它是独立于MofileFS系统的。以下是表创建脚本：
    
    
    
    
    
    
    CREATE TABLE plugin_filepaths_paths (
    nodeid BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
    dmid SMALLINT UNSIGNED NOT NULL,
    parentnodeid BIGINT UNSIGNED NOT NULL,
    nodename VARCHAR(255) BINARY NOT NULL,
    fid BIGINT UNSIGNED,
    PRIMARY KEY (nodeid),
    UNIQUE KEY (dmid, parentnodeid, nodename)
    
    CREATE TABLE plugin_filepaths_domains (
    dmid SMALLINT UNSIGNED NOT NULL,
    PRIMARY KEY (dmid)

### 需要注意的问题

  1. 全路径名称必须以/号开始，不存在相对路径一说
  2. 文件的插入，走的仍然是MogileFS系统命令循环，主命令的操作仍然被执行。只是在主命令执行时，会调用一些插件提供的函数，在调用插件函数过程中完成了信息存储的偷梁换柱。
  3. 经过此插件，文件key的格式为fid:$fid，需要通过转换才能获得自定义的key值
  4. 在fid的查找过程中，有使用memcache，但是，在插件中基于路径节点的查询，是没有相应cahche操作的，可能会对性能产生影响

### 插件使用例子（perl）
    
    
    #!/usr/bin/perl
    use MogileFS::Client::FilePaths;
    
    # From MogileFS::Client (See its documentation for details)
    $mogc = MogileFS::Client::FilePaths->new(domain => "jianboDomain",
    hosts  => ['10.62.162.75:6061']);
    $mogc->filepaths_enable;