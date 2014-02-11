title: linux下系统托盘的实现
link: http://www.54chen.com/web-ral/linux-system-tray-under-the-realization-of.html
author: admin
description: 
post_id: 24
created: 2006/10/13 22:47:16
created_gmt: 2006/10/13 14:47:16
comment_status: open
post_name: linux-system-tray-under-the-realization-of
status: publish
post_type: post

# linux下系统托盘的实现

[文章作者：陈臻 本文版本：v1.0 最后修改：2006.10.13 转载请注明原文链接：<http://www.54chen.com/c/24>] 在gnome中国的网页上找到这个内容，他们把它叫做“通知功能图标”。它是一个在未来版本中决定发布的已实现包。 http://www.gnome-cn.org/documents/tutorial/system-tray-icon-application/implement-your-tray 具体实现前需要在下载两个开发补丁源文件，然后包含头文件到你的文件中。 具体代码： void create_tray (tray_demo * tray) { GtkWidget *image; GtkWidget *event_box; GtkTooltips *tooltips; EggTrayIcon *tray_icon; tooltips = gtk_tooltips_new (); tray_icon = egg_tray_icon_new ("Tray icon demo"); event_box = gtk_event_box_new (); gtk_container_add (GTK_CONTAINER (tray_icon), event_box); g_signal_connect (G_OBJECT (event_box), "button-press-event", G_CALLBACK (tray_button_press_event_cb), tray); gtk_tooltips_set_tip (GTK_TOOLTIPS(tooltips), event_box, _("Try a left/right click"), NULL); image = load_image (GCN_ICON); gtk_container_add (GTK_CONTAINER (event_box), image); gtk_widget_show_all (GTK_WIDGET (tray_icon)); return; }