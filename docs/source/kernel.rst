Linux内核使用指南
*********************

下载
=========
源代码下载地址 https://github.com/EMSYM/linux

* Blurr (freescale i.MX6) 维护的内核版本

  * 4.1.15
  * 3.10
  * 3.0

* Jazz (freescale i.MX233) 维护版本
 
  * 3.14
  * 2.6.35

如果希望将代码修改合并到EMSYM维护的版本库，
请按照github的fork→pull request流程提交patch，我们会尽快处理。
感谢各位的无私贡献：

.. include:: thanks.rst 

编译
======

编译环境搭建
-------------------------

如果之前已经搭建好环境的可跳过此步

如何确定是否配置好环境：

.. sourcecode:: bash

	# 第一步，安装git,没有git，就不能顺利进行
	sudo apt-get install git
	# 查看自己所安装的版本
	arm-linux-gnueabi-gcc -v
	# 若没有版本提示，则执行下条命令安装
	sudo apt install gcc-arm-linux-gnueabi
	# 为了避免一些错误，再执行下面这条命令
	sudo apt-get install build-essential gcc
	
至此，编译环境搭建完成
	
内核版本 ≥4.1.15
-------------------------

.. sourcecode:: bash

	# 项目源码主页 https://github.com/EMSYM/linux
	git clone https://github.com/EMSYM/linux.git #下载代码
	git checkout -b blurr-4.1.15 origin/blurr-4.1.15 #切换分支
	# 编译内核
	ARCH=arm make blurr_defconfig #配置内核
	ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- make -j8 #编译内核，生成zImage和dtb文件
	# -j8指定你的cpu核数×2，即我的cpu核数为4，但也可不指定。
	
烧录
+++++++++

SD卡分区
^^^^^^^^^^^^

SD卡需要创建2个分区，并且在第一个分区之前预留一段>=1MB的空间用于保存Uboot Image。第一个分区需要设置为fat格式，用于保存zImage和dtb文件，第二个分区需要设置为ext4格式，用于保存根文件系统。

.. sourcecode:: bash

	# 在分区之前，先将SD卡在windows pc上将其格式化成fat
	# 插入SD卡，查看挂载位置
	sudo fdisk -l # 其中与你SD卡容量差不多的便是你的SD卡
	#在这里默认大家的挂载位置都为/dev/sdb
	sudo fdisk /dev/sdb # 查看分区信息
	# 输入m显示所有可用命令
	# 输入p显示分区情况
	# 输入d删除分区(可选则删除分区号)
	# 输入w保存分区信息
	# 输入n新建分区
	#   输入p，分割为主分区(primary)
	#   输入e，分割为扩展分区(extend)
	# 输入q结束不保存分区
	# 输入t格式化SD卡分区
	
以一张16g SD卡为例：

.. sourcecode:: bash

	sudo fdisk /dev/sdb
	# 通过d指令删除原来的分区。重复的输入d并回车，可以删除SD卡原有的分区，知道没有任何分区为止。
	# 通过n指令新建分区。输入n并回车。
	# 输入分区编号:1
	# 输入分区的起始位置。该位置是sectors为单位的，每个sector的大小为512B，这里预留4M，输入：8192
	# 输入分区的结束位置。输入：73727
	# 再新建一个分区。输入n并回车。
	# 输入分区编号：2
	# 输入分区起始位置：73728
	# 输入分区结束位置：直接enter默认到SD卡最后一个扇区
	# 输入p查看分区
	# 输入w保存分区
	
格式化分区
^^^^^^^^^^^^

.. sourcecode:: bash

	sudo fdisk /dev/sdb
	# 输入t
	# 选择分区：1
	# 这里将分区1格式化为fat格式，输入6或者c都行
	# 再次输入t
	# 选择分区：2
	# 将分区2格式化为ext4格式，输入83
	# 保存退出
	
至此，对于SD卡的处理就完成了
	

.. sourcecode:: bash

	cp arch/arm/boot/zImage /media/emsym/udisk
	cp arch/arm/boot/dts/imx6dl-blurr.dtb /media/emsym/udisk
	# 在这儿为了省事，同学们可直接将zImage和imx6dl-blurr.dtb这两个文件复制到SD卡的第一个分区

启动
+++++++++

设置U-boot为SD卡启动
其中有两个地方需要注意：**其一，在靠近HDMI借口的地方有个拨动开关，将其拨到boot一端；其二，红色八位开关，调至01000010状态。**
将SD卡插入靠近两个USB插口的SD3卡槽。

启动U-boot
在windows pc上下好串口调试软件putty，用一根数据线连接blurr和pc(**注意：mini USB一端插在靠近两个USB插口的那个插口上**)
打开设备管理器，点开端口，查看括号内显示的COM编号，打开putty，选择Serial，将COM1改为之前查到的COM编号，speed改为115200，然后最下点击open
blurr上电，此时应该看到输出。

输入

.. sourcecode:: bash

	setenv bootargs 'console=ttymxc0,115200 root=/dev/mmcblk2p5 init=/sbin/init'
	# console 指定调试串口的编号
	# root 指定根文件系统的分区
	saveenv # 保存设置
	
	#以下是加载镜像
	fatload mmc 1:1 12000000 zImage; #将SD卡分区一的镜像加载到内存地址0x12000000
	fatload mmc 1:1 11000000 imx6dl-blurr.dtb; #加载Device Tree文件
	#启动镜像
	bootz 12000000 - 11000000
此时如若连接上了显示屏，会在屏幕左上角看到两只可爱的企鹅欢迎你！

内核版本 ≥3.10
-------------------------
最新的ARM内核采用device tree配置文件，扩展名*.dtb。
一个dtb文件与一种类型的主板对应。
详细解释参见 :ref:`fdt`

此时， 内核启动需要两个文件:

#. 未经压缩的内核可执行程序：vmlinux。包含内核、模块、驱动等。
#. device tree 设备配置文件。一个dtb文件与一种类型的主板对应。

编译的命令

.. sourcecode:: bash

	make blurr_defconfig
	make
	cp vmlinux /path/to/uboot
	cp arch/arm/boot/dts/blurr.dtb /path/to/uboot

然后需要将内核制作成镜像，是UBoot支持的启动格式，参见 :ref:`uboot-fdt`


.. _uimage:

编译生成uImage内核镜像（内核版本≤3.0）
----------------
编译内核，然后生成 uImage 内核镜像，供UBoot启动。
编译的命令:

.. sourcecode:: bash

	make blurr_defconfig
	make uImage
	# 烧写镜像到SD卡
	sudo dd if=arch/arm/boot/uImage of=/dev/sdb bs=512 seek=2048; sync

配置内核
=========
TODO
.. make menuconfig


