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
