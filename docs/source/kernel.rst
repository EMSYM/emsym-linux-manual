Linux内核使用指南
*********************

下载
=========
源代码下载地址 https://github.com/EMSYM/linux

* Blurr (freescale i.MX6) 维护的内核版本

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


