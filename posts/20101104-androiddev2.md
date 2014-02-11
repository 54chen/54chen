title: android开发入门2：概念建立
link: http://www.54chen.com/java-ee/androiddev2.html
author: 54chen
description: 
post_id: 1432
created: 2010/11/04 18:31:51
created_gmt: 2010/11/04 10:31:51
comment_status: open
post_name: androiddev2
status: publish
post_type: post

# android开发入门2：概念建立

![android 54chen dev](http://img01.taobaocdn.com/imgextra/i1/T19G0QXdVzXXcu602a_120602.jpg) 上一记传送门：[android开发入门](/_linux_/androiddev.html) 在第一记中，我（54chen）完成了一个完整的android开发环境，并且简单开发了一个Hello world android程序，在有了一个大概的开发过程的感觉之后，再进一步来学习android开发过程中需要接触到的常识。 **android开发表皮几大块** 1）extends Activity的Activity类很是奇异，其实这很像是mvc框架中的controller，继承Activity实现的类，它控制了所有的业务逻辑。 2）打开项目的文件夹，你会发现有许多的xml，其中，比较关键的一个是main.xml（layout目录下），它相当于mvc框架中的view，负责了软件界面哪个位置摆放一个什么样的控件，当然了，这些控件都是系统本身的，你可以想像成是html标签里的那些控件一样。 3）values目录下有settings.xml，是定义了一些字符串、颜色、数组等常量数据，有点像普通java项目里的属性文件（properties文件） **走进android项目中的layout/main.xml** 开发android项目和开发web项目有几分神似，在main.xml里(也可能是xx.xml哦，main.xml是默认的第一个，在java代码里，会用R.layout.main来表示man.xml)，包括了各种各样的基础控件（它们都是大小写敏感的，如果在xml里写错了，那一定会在运行过程中出现空指针错误），就像是web项目的前端一样，不过其复杂程度自然要比写html复杂。可能大部分入门android的时间都会花在这里。 <TextView android:layout_height=”wrap_content” android:layout_width=”fill_parent” android:id=”@+id/TextView01″ android:text=”54chen Android2.2 学习手记” android:textColor=”#0f0″ android:textSize=”28dip” android:layout_y=”330dip” android:layout_x=”35dip“> </TextView> 这就是main.xml里的一个片段，这是一个TextView控件(请勿直接复制，看看即可，有半角全角的问题会出现错误)，这个控件，关键词是TextView，大小写是敏感的，另外它丰富的属性都是由android:xx来定义的，其他的控件也是类似的定义。一般都会有，高度、宽度、内容、id、颜色、字体大小、布局相关的信息。 例子到此，详细的更多控件的使用，我们在学习完intent之后再在后面的手记中去记录。 **Intent：android项目中的胶水** 前面说到，相当于controller层的activity，实际上一般情况是一个activity对应一个layout的xml，而一个常见的软件，都会弹出来多个窗体（做过vb vc delphi啥的一定知道subform的概念），这个时候需要多个 activity来回切换，这时候就是靠intent在中间起作用。 在一个Android应用中，Intent是对执行某个操作的一个抽象描述，Intent 负责提供组件之间相互调用的相关信息传递，实现调用者和被调用者之间的解耦。Intent是一种在不同组件之间传递的请求信息，是应用程序发出的请求和意图，作为一个完整的消息传递机制，Intent不仅需要发送端，还需要接收端。 在android应用总控xml（AndroidManifest.xml）里有这样的内容：<intent-filter> ，这里面定义了可以访问的intent动作。 

> // 获得可编辑文本框中的值，也就是电话号码 String phoneNumber = PhoneNumberEditText.getText().toString(); // new Intent(行为，数据)，其中action_dial是拨号行为，数据是电话号码 Intent intent = new Intent(Intent.ACTION_DIAL, Uri .parse("tel://" + phoneNumber)); // 去调用那些可以处理拨号行为的Activity startActivity(intent); 

看完上述代码，是不是有点对intent概念的感觉了呢，如果还有疑惑，再看 

> //在当前Activity中启动另外一个Activity startActivity(new Intent(this,目标Activity.class));

## Comments

**[54chen](#13180 "2010-11-06 09:45:36"):** 真相不详，我会反馈给相关同事的，谢谢你的反馈

**[janwen](#13182 "2010-11-06 10:17:14"):** 刚才去看了看我人人的日志发现，导入的日志数量比我搜狐博客的少了好几篇，看来还是有些问题，希望尽早解决啊

**[janwen](#13181 "2010-11-06 10:14:02"):** 不知道是我反馈给你的原因还是见鬼了？今天早上上人人发现我的日志被导入进去了，记得以前很快就会导入的现在时间间隔太长了，谢谢

**[阿政](#13170 "2010-11-05 09:53:37"):** 持续关注 坚持学习 呵呵

**[54chen](#13171 "2010-11-05 10:40:06"):** 好同学，哈哈

**[janwen](#13174 "2010-11-05 12:40:04"):** 要是在上点图就好了

**[54chen](#13175 "2010-11-05 12:53:08"):** 下一手记的时候再上图，这一记是概念

**[白小白](#13176 "2010-11-05 15:58:15"):** 嘻嘻~我看热闹，那个老太太的图配的不错，我也用游戏织吧~省的天天一针针的手工织，哈哈

**[janwen](#13177 "2010-11-05 21:15:47"):** 大侠的新浪微薄居然不让私信？只能上这了，我人人设置了博客导入，最近一段时间不知道为什么一直没有导入我的博客(搜狐博客)，是人人没有去获取我搜狐博客的rss还是搜狐不让你们家人人获取啊，求真相，谢谢

