UBoot使用指南
******************

下载
=========
https://github.com/EMSYM/U-boot

.. sourcecode:: bash

	git clone https://github.com/EMSYM/U-boot

.. important::
   在github列出来的分支都是开发分支，供开发人员使用。一般用户如果只是编译固件，并不想修改源码，请用带发布标签的tag。

列出所有标签的命令：

.. sourcecode:: bash

	git tag -l
	
我们发布版本的时候，UBoot的版本号与Linux版本对应，所以请根据所需Linux选择对应的UBoot。

例如，需要Linux内核的版本是3.10，那么请使用v3.10的UBoot。

.. sourcecode:: bash

	git checkout -b v3.10 blurr-3.10

编译 UBoot
========================================
第一步，生成配置文件

.. sourcecode:: bash

	make mx6dl_blurr_defconfig

根据核心板的类型，改变相应参数。支持的参数：

* mx6q_blurr_defconfig #四核，2GB内存
* mx6dl_blurr_defconfig #双核精简版，1GB内存

第二步，编译UBoot

.. sourcecode:: bash

	make CROSS_COMPILE=arm-linux-gnueabi-

烧写UBoot
==================

.. sourcecode:: bash

	sudo dd if=u-boot.imx of=/dev/sdb bs=512 seek=2 ;sync

至此，连接调试串口，主板上电，应该可以在终端看到UBoot的输出。


.. _uboot-fdt:

生成UBoot支持启动的内核镜像
=========================

主板配置文件（内核版本≥3.10）
------------------
我们基于一款处理器可以研发各种各样的系统，每种系统的主板都是不一样的。
对于这种情况，传统的Linux需要为每种主板定制不同的内核。

Linux v3.10 内核针对ARM体系结构引入主板配置文件（Device tree）的概念，
*.dtb 配置文件代表了硬件配置的组合。或者从操作系统的角度来看，
他声明了驱动程序的组合，类似 menuconfig 选择加载哪些驱动。
每一种主板对应一个dtb配置文件，主板上面需要用到的外设，我们就在配置文件里声明，
操作系统运行的时候就会加载这个外设相应的驱动。
修改配置文件的详细方法参见 :ref:`fdt`

多个内核、多个配置文件可以同时打包进一个镜像里。
UBoot的这种设计为开发带来了极大的灵活性，我们可以只烧写一个镜像，
通过启动的时候修改选项（类似PC机BIOS界面配置），
实现一个镜像兼容多种主板。

下载的UBoot源码已经包含了BLURR开发板的标准配置文件。

* blurr_imx6q.dtb
* blurr_imx6dl.dtb

如果需要导入其他主板的配置文件，先将dtb文件复制到UBoot根文件夹，
然后再修改 ``linux.its`` 文件对应内容。
如果是基于核心板二次开发的主板，可以根据 :ref:`fdt` 的说明，
新建一个主板配置文件。

``linux.its`` 文件的说明

.. sourcecode:: c 

	/dts-v1/;

	/ {
		description = "Simple image with single Linux kernel and FDT blob";
		#address-cells = <1>;

		images {
		        kernel@1 {
		                description = "Linux kernel";
		                data = /incbin/("./vmlinux.gz");  /*包含这个二进制文件到镜像*/
		                type = "kernel";
		                arch = "arm";
		                os = "linux";
		                compression = "gzip";  
		                load = <0x10008000>;
		                entry = <0x10008000>;
		                hash@1 {
		                        algo = "crc32";
		                };
		                hash@2 {
		                        algo = "sha1";
		                };
		        };
		        fdt@1 {
		                description = "Q";
		                data = /incbin/("./imx6q-sabreauto.dtb"); /*主板配置*/
		                type = "flat_dt";
		                arch = "arm";
		                compression = "none";
		                hash@1 {
		                        algo = "crc32";
		                };
		                hash@2 {
		                        algo = "sha1";
		                };
		        };
		        fdt@2 {
		                description = "DL";
		                data = /incbin/("./imx6dl-sabreauto.dtb");/*另一个主板配置*/
		                type = "flat_dt";
		                arch = "arm";
		                compression = "none";
		                hash@1 {
		                        algo = "crc32";
		                };
		                hash@2 {
		                        algo = "sha1";
		                };
		        };
		};

		configurations {
		        default = "conf@1";
		        conf@1 {
		                description = "Q"; 
		                kernel = "kernel@1";
		                fdt = "fdt@1"; 
		        };
		        conf@2 {
		                description = "DL";
		                kernel = "kernel@1";
		                fdt = "fdt@2";
		        };

		};
	};
                          
生成镜像
-------------------------------------

将Linux内核文件 ``vmlinux`` 复制到UBoot源码的文件夹。执行命令：

.. sourcecode:: bash

	gzip vmlinux

压缩内核，生成 ``vmlinux.gz`` 文件。

下一步，引入主板的dtb配置文件，复制到 UBoot 源代码文件夹。

下载的UBoot源码已经包含了开发板的默认配置文件。

生成镜像，执行

.. sourcecode:: bash

	mkimage -f linux.its kernel_fdt.itb
	# 烧写镜像
	sudo dd if=kernel_fdt.itb of=/dev/sdb bs=512 seek=2048; sync

.. tools/mkimage -n  imxcfg.imx -T imximage -e 0x17800000 -d u-boot.bin u-boot.imx


旧版本UBoot（内核≤3.0）
===============
旧版本的UBoot或者旧版本的内核都不支持device tree，
那么我们要采用uImage。生成uImage的方法参见 :ref:`uimage`

将SD卡插入PC,执行: 

.. sourcecode:: bash

	sudo dd if=arch/arm/boot/uImage of=/dev/sdb bs=512 seek=2048; sync


..	arm-linux-gnueabi-objcopy --gap-fill=0xff -O binary u-boot u-boot.bin

.. arm-linux-gnueabi-gcc -E -x c "board/freescale/mx6qsabresd/mx6dl_4x_mt41j128.cfg" -I./include -o imxcfg.imx
	uImage kernel
	---------------

UBoot参数配置
===============
至此，UBoot 镜像和 Linux 内核已经烧写到 SD 卡。

接下来配置 UBoot 的参数，加载内核。

.. sourcecode:: bash

	setenv loadaddr 0x12000000 # 设定在内存加载内核的位置

	# 设定内核的启动参数
	setenv bootargs 'console=ttymxc3,115200' # Linux 调试输出的串口
	setenv bootargs ${bootargs} 'root=/dev/mmcblk0p1' # 根文件系统
	setenv bootargs ${bootargs} 'init=/sbin/init' # 启动进程

	saveenv # 保存以上参数，重启不会丢失

	mmc dev 1; # 从1号 SD 卡加载内核镜像
	mmc read ${loadaddr} 0x800 0x3000; # 读入镜像到内存
	bootm; # 启动

	# 希望上电时自动加载内核
	setenv bootcmd 'mmc dev 1;mmc read ${loadaddr} 0x800 0x3000;bootm;'
	saveenv


.. setenv bootargs_base 'setenv bootargs console=ttymxc3,115200'
	setenv bootargs_mmc 'setenv bootargs ${bootargs} init=/sbin/init root=/dev/mmcblk0p1 '
	setenv bootargs_mmc 'setenv bootargs ${bootargs} init=/sbin/init initrd=0xf42400 root=/dev/ram0 rw'
	setenv bootcmd 'run bootargs_base bootargs_mmc;mmc read ${loadaddr} 0x800 0x3000;bootm'	


加入开机图片
============
TODO
