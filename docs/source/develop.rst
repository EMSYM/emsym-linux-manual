开发、编译程序
========================================

编译程序
-----------------
#. 下载SDK安装包，在命令行直接运行。
#. 如果提示错误信息：“/opt/poky/1.4.3/sysroots/i686-pokysdk-linux/lib”:No such file or dircctory.
  * 若安装jazz-sdk,执行下面的操作：
     * 新建目录：mkdir /opt/poky/
     * 创建软链：ln -s /opt/jazz-sdk/ /opt/poky/1.4.3/
     * 重新执行install-jazz-sdk.sh
  * 若安装blurr-sdk,执行下面的操作：
     * 新建目录：mkdir /opt/poky/
     * 创建软链：ln -s /opt/blurr-sdk/ /opt/poky/1.4.3/
     * 重新执行install-blurr-sdk.sh
#. 设置环境变量，执行
	::
	
		$ source /install_path/environment-setup-ARCH-poky-linux-gnueabi
		# verify installation and environment
		$ export
		
#. 编译命令 arm-poky-linux-gnueabi-gcc		


编译Qt程序
-----------------
确保已安装SDK，然后执行 ::

	$ source /install_path/environment-setup-ARCH-poky-linux-gnueabi
	$ qmake PROJECT.pro
	$ make
