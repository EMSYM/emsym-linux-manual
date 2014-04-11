.. Linux Manual documentation master file, created by
   sphinx-quickstart on Mon Apr  7 09:57:41 2014.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

修改根文件系统
========================================

启动时禁止LCD显示控制台命令行
-------------------------------------
修改 /etc/inittab 文件，找到一行包含::

	1:2345:respawn:/sbin/getty 38400 tty1

其中关键字的含义为：

/sbin/getty 
	运行控制台的命令
tty1
	终端名，tty1 代表显示屏；如果是ttymxc3，代表串口终端
	

开机自动启动程序
-----------------
在 /etc/init.d 文件夹创建一个脚本。可以复制同文件夹下面其他脚本，修改。

之后在 /etc/rc5.d 做一个软链，名字为 S10filename ，指向前面的脚本。

原理参见：
