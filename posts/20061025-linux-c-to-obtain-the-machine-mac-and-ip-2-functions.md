title: linux C获取机器MAC及IP的两个函数
link: http://www.54chen.com/web-ral/linux-c-to-obtain-the-machine-mac-and-ip-2-functions.html
author: admin
description: 
post_id: 28
created: 2006/10/25 18:10:52
created_gmt: 2006/10/25 10:10:52
comment_status: open
post_name: linux-c-to-obtain-the-machine-mac-and-ip-2-functions
status: publish
post_type: post

# linux C获取机器MAC及IP的两个函数

[文章作者：陈臻 本文版本：v1.0 最后修改：2006.10.25 转载请注明原文链接：<http://www.54chen.com/c/28>] 以下两函数分别将mac ip通过memcpy至mac_addr和ip_addr char * get_mac() { int nSocket; struct ifreq struReq; nSocket = socket(PF_INET,SOCK_STREAM,0); memset(&struReq,0,sizeof(struReq)); strncpy(struReq.ifr_name, "eth0", sizeof(struReq.ifr_name)); ioctl(nSocket,SIOCGIFHWADDR,&struReq); close(nSocket); memcpy(mac_addr,(char *)ether_ntoa(struReq.ifr_hwaddr.sa_data),strlen((char *)ether_ntoa(struReq.ifr_hwaddr.sa_data))); return 0; } char * get_ip() { int sock; struct sockaddr_in sin; struct ifreq ifr; sock = socket(AF_INET, SOCK_DGRAM, 0); if (sock == -1) { perror("socket"); return ""; } strncpy(ifr.ifr_name, "eth0", sizeof(ifr.ifr_name)); if (ioctl(sock, SIOCGIFADDR, &ifr) < 0) { perror("ioctl"); return ""; } memcpy(&sin, &ifr.ifr_addr, sizeof(sin)); close(sock); memcpy(ip_addr,inet_ntoa(sin.sin_addr),strlen(inet_ntoa(sin.sin_addr))); return ""; }