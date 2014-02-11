title: 解读PHP开源项目中列表和hook方法：while(has_items()): thme_ite();和apply_filters
link: http://www.54chen.com/php-tech/php-open-source-projects-reading-lists-and-hook-methods-while-has_items-thme_ite-and-apply_filters.html
author: cc0cc
description: 
post_id: 949
created: 2010/02/08 17:23:55
created_gmt: 2010/02/08 09:23:55
comment_status: open
post_name: php-open-source-projects-reading-lists-and-hook-methods-while-has_items-thme_ite-and-apply_filters
status: publish
post_type: post

# 解读PHP开源项目中列表和hook方法：while(has_items()): thme_ite();和apply_filters

在[wordpress](/architecture/wordpress-nginx-php-cgi-mysql-memory-in-the-128m-to-install-and-optimize-the-vps-notes.html),lilina等开源项目中，常常会看到一些莫名的代码，来无影去无踪，看上去很有意味，特地拿来分析分析，看看一般的作法。 **part 1  列表 循环显示** 先看代码： 

> <?php $num = 0; if(has_items()) { while(has_items()): the_item(); } ?>

以上代码，出自某开源项目。。。 再进入the_item()函数内部，可以看到一个关键的函数： 

> * Returns the current item * * @since 1.0 * * @return bool|stdClass False if item doesn't exist, otherwise returns the specified item */ public function current_item() { $this->previous_item = $this->current_item; $this->current_item = ''; $item = each($this->items); $item = $item['value']; if(!$item) return false; $this->current_item = $item; $this->current_feed = $item->feed; return $item; }

这个关键的函数是each()，来看手册： 

# each

(PHP 3, PHP 4, PHP 5) each --  返回数组中当前的[键／值](/translate/dynamo-based-systems-designed-linkin-voldemort-voldemort-design-chinese-documents-i-am-a-chan-academy-of-sciences-translation-finalized.html)对并将数组指针向前移动一步

## 说明

array **each** ( array &array ) 返回 array 数组中当前指针位置的键／值对并向前移动数组指针。键值对被返回为四个单元的数组，键名为 0，1，key 和 value。单元 0 和 key 包含有数组单元的键名，1 和 value 包含有数据。 如果内部指针越过了数组的末端，则 **each()** 返回 `**FALSE**`。 综合来看，其实就是一个做了一个游标一样的模型，利用each来操作一个数组或者相似的结构，每次在显示的时候，都有对数据数组的本身操作，而while里的has_item，只是作简单的判断有无而已。 **part2 hook** 第二种情况，是一个叫apply_filters的函数的[实现](/architecture/wordpress-nginx-php-cgi-mysql-memory-in-the-128m-to-install-and-optimize-the-vps-notes.html)。先看代码： 

> function the_item() { global $lilina_items, $item; $item = apply_filters('the_item', $lilina_items->current_item()); }

这是lilina里的一个函数，要看的是这个apply_filters方法，有一个简单的例子： 

> function example_hook($string, $arg1, $arg2) { //Do stuff return $string; } $value = apply_filters('example_hook', 'filter me', 'arg1', 'arg2');

apply_filters里面最关键的一个函数： 

# call_user_func_array

(PHP 4 >= 4.0.4, PHP 5) call_user_func_array --  Call a user function given with an array of parameters

## Description

mixed **call_user_func_array** ( callback function, array param_arr ) Call a user defined function given by function, with the [parameters](/php-tech/multi-nginx-configuration-of-single-php-fpm-approach-from-academy-of-sciences.html) in param_arr. 其实这些渐渐被遗忘的[函数](http://www.54chen.com/webfe/academy-of-sciences-in-recent-years-a-good-article-summing-up.html)，在高手们的使用下，对项目化的开发很有优势，随手记之。