title: [54chen原创]简单好用的土办法抗击洋鬼子对wordpress系统的广告灌入
link: http://www.54chen.com/php-tech/easy-method-to-antispam-wordpress.html
author: 54chen
description: 
post_id: 1301
created: 2010/08/23 23:24:02
created_gmt: 2010/08/23 15:24:02
comment_status: open
post_name: easy-method-to-antispam-wordpress
status: publish
post_type: post

# [54chen原创]简单好用的土办法抗击洋鬼子对wordpress系统的广告灌入

![54chen](http://img04.taobaocdn.com/imgextra/i4/T1c3hJXb8pXXcVO1Z._112132.jpg) 自升级wordpress3.0以后，一切风调雨顺，自上周起，被洋鬼子的广告肉机盯上了，平均每三五分钟一条广告，评论内容大致相同，评论ip来自五湖四海，各不相同。 虽然说akismet是wordpress中antispam无二的选择，但是akismet那个验证码让人很纠结，于是只能自己山寨一把了。 **土办法步骤一 转发默认评论地址** 常见的洋鬼子的广告肉机，都是利用了wordpress公开的post地址来做到脚本灌入的，其默认地址是wp-comments-post.php，第一步要做的是，屏蔽这个地址（以nginx为例）： rewrite ^/wp\\-comments\\-post\\.php$ http://www.54chen.com/ last; **土办法步骤二 伪造假地址** 既然第一步中的真实地址无法访问了，那第二步肯定是假冒出来可以访问的地址： if (!-e $request_filename){ rewrite ^/wc([0-9]+)\\.html$ /wp-comments-post.php?54chen=$1 last; } **土办法步骤三 javascript制造假post** 完成上面两步之后 ，最后一步就是将原来的comments.php修改post action的地址为伪造的地址，为了更加有效果，使用js来产生随机的wc1234.html即可： 在评论的form中增加onsubmit="chenAntiSpam()" name="commentform"； 再增加js: <script type="text/javascript"> function chenAntiSpam() {  var param ='wc'+Math.round(Math.random()*1000)+'.html';  document.commentform.action = 'http://www.54chen.com/'+param; } </script> 大功告成，有效防住了洋鬼子的灌。

## Comments

**[wwek](#13016 "2010-08-30 16:03:47"):** 还是觉得加验证码才是防止机器的最好方法·

**[54chen](#13017 "2010-08-30 16:10:57"):** 不过我这个办法很适用，现在一条也进不来了，除非纯人肉的

**[g.cn](#13021 "2010-09-01 18:04:53"):** 用中文验证码怎么样？？ 哈哈

**[54chen](#13022 "2010-09-01 20:08:28"):** 输入验证码的操作都是增加用户输入成本的事情不是太好

**[54chen](#12994 "2010-08-24 10:26:20"):** 就是那个了，注册是十分不爽的做法，所以就自己动手丰衣足食了

**[胖子](#12989 "2010-08-24 09:21:56"):** 学习了， 哥也有类似的经历，不过在纠结了一段时间后，就不理我了

**[54chen](#12990 "2010-08-24 09:24:19"):** 经过哥的上述折腾后，已经有效把洋鬼子挡在门外

**[xiaokai](#12991 "2010-08-24 09:48:03"):** 陈大威武, 哥学习之.

**[54chen](#12992 "2010-08-24 09:51:24"):** 忧郁哥，早

**[ElmerZhang](#12993 "2010-08-24 10:13:22"):** akismet验证码？你是说akismet那个API KEY吗？直接到wordpress.com上注册一个帐号就有了呀

**[鱼此鱼已](#13278 "2010-12-22 09:49:21"):** Apache的规则怎么写呢？谢谢。

