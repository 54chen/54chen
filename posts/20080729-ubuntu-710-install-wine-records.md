title: ubuntu 7.10 安装wine记录
link: http://www.54chen.com/_linux_/ubuntu-710-install-wine-records.html
author: admin
description: 
post_id: 51
created: 2008/07/29 10:14:30
created_gmt: 2008/07/29 02:14:30
comment_status: open
post_name: ubuntu-710-install-wine-records
status: publish
post_type: post

# ubuntu 7.10 安装wine记录

cz@cz-desktop:~$ wget -q http://wine.budgetdedicated.com/apt/387EE263.gpg -O- | sudo apt-key add - OK cz@cz-desktop:~$ sudo wget http://wine.budgetdedicated.com/apt/sources.list.d/gutsy.list -O /etc/apt/sources.list.d/winehq.list cz@cz-desktop:~$ sudo apt-get update cz@cz-desktop:~$ sudo apt-get install wine