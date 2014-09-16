.. Linux Manual documentation master file, created by
   sphinx-quickstart on Mon Apr  7 09:57:41 2014.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

通过命令行使用、控制网络
========================================

连接以太网
---------------

连接 wifi 
--------------
查看网卡信息：ifconfig -a;

查看指定网卡的信息，比如wlan0：ifconfig wlan0;

打开无线网卡：ifconfig wlan0 up;

列出区域内的无线网络：iwlist wlan0 scan;

连接到指定的网络，比如EMSYM : iwconfig wlan0 essid "EMSYM";

如果网络是加密的，密码是1234567890，则连接命令是：iwconfig wlan0 essid "EMSYM" key "1234567890";

wpa 连接 wifi
--------------
ifconfig -a   //查看可用网络接口

ifconfig wlan0 up     //启动无线网卡

iwlist wlan0 scan      //查看可连接的无线网络

wpa_passphrase "EMSYM" 12345678 > wpa.conf  //设置无线网卡账号和密码,例:ID:EMSYM 密码:12345678

wpa_supplicant -B -i wlan0 -c wpa.conf   //为网络配置ID和密码

udhcpc -i wlan0 //自动分配IP地址
