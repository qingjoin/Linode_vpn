#Linode 设置VPN 服务



##1、安装PPTP服务器

更新软件
apt-get update
安装pptpd
apt-get install pptpd
##2、配置PPTP服务器

用 vi 编辑/etc/pptpd.conf
查找如下内容：

*#localip 192.168.0.1
*#remoteip 192.168.0.234-238,192.168.0.245
把#去掉，然后修改成下面的

localip 192.168.217.1
remoteip 192.168.217.234-238,192.168.217.245
上面的两行为VPN服务器的IP和VPN客户端连接后获取到的IP范围。保存退出 

添加PPTP VPN用户

##添加服用名。服务。密码

编辑/etc/ppp/chap-secrets 添加如下内容：

username pptpd password *
其中username为你要添加的VPN帐号的用户名，password为你VPN帐号的密码。

 

##修改DNS服务器

编辑/etc/ppp/options，添加如下内容：

ms-dns 8.8.8.8
ms-dns 8.8.4.4
开启IPv4转发

##编辑/etc/sysctl.conf文件，去掉net.ipv4.ip_forward=1前的注释
运行如下命令，使配置修改生效

sysctl -p
 
##重启pptpd服务

/etc/init.d/pptpd restart
安装iptables

apt-get install iptables  
 

##开启iptables转发

iptables -t nat -A POSTROUTING -s 192.168.217.0/24 -o eth0 -j MASQUERADE
iptables-save > /etc/iptables.pptp
 

##在/etc/network/if-up.d/目录下创建iptables文件，内容如下：

*#!/bin/sh
iptables-restore < /etc/iptables.pptp
给脚本添加执行权限：

chmod +x /etc/network/if-up.d/iptables
至此PPTP VPN服务器端的设置就完成了。