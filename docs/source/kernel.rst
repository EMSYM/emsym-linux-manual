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

内核版本 ≥4.1.15
-------------------------

.. sourcecode:: bash

	# 项目源码主页 https://github.com/EMSYM/linux
	git clone https://github.com/EMSYM/linux.git #下载代码
	git checkout -b blurr-4.1.15 origin/blurr-4.1.15 #切换分支
	# 编译内核
	ARCH=arm make mx6dl_defconfig #配置内核
	ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- make -j8 #编译内核，生成zImage和dtb文件

将SD卡分区，第一个分区格式化成为fat格式。将编译生成的内核复制到SD卡。

.. sourcecode:: bash

	cp arch/arm/boot/zImage /media/emsym/udisk 
	cp arch/arm/boot/dts/imx6dl-blurr.dtb /media/emsym/udisk

启动U-boot，输入

.. sourcecode:: bash

	setenv bootargs 'console=ttymxc0,115200 root=/dev/mmcblk2p5 init=/sbin/init'
	# console 指定调试串口的编号
	# root 指定根文件系统的分区
	
	#以下是加载镜像
	fatload mmc 1:1 12000000 zImage; #将SD卡分区一的镜像加载到内存地址0x12000000
	fatload mmc 1:1 11000000 imx6dl-blurr.dtb; #加载Device Tree文件
	#启动镜像
	bootz 12000000 - 11000000


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


