常用应用程序
========================================

opkg的使用说明
-----------------

1. 添加更新文件路径到配置文件(如果没有就创建一个opkg.conf文档)
 - vi /etc/opkg/opkg.conf
2. 在里面添加ipk包的路径
 - src/gz local1 file:////文件路径
 - src/gz local2 file:////文件路径
 - dest root /                                                  
 - lists_dir ext /var/lib/opkg
 - 例添加如下文件
 - src/gz local1 file:////media/sda1/ipk/all                    
 - src/gz local2 file:////media/sda1/ipk/blurr_imx6dl           
 - src/gz local3 file:////media/sda1/ipk/i686-nativesdk         
 - src/gz local4 file:////media/sda1/ipk/armv7a-vfp-neon  
 - 添加好后保存退出
3. 输入命令>opkg update更新下opkg文件
4. 输入opkg install 输入需要安装的ipk文件的名字
 - 例：提示没有playbin2
 - 先用opkg list | grep playbin查找playbin相关的文件
 - 然后找到自己需要的gst-plugins-base-playbin
 - 再用opkg install gst-plugins-base-playbin安装
5. 提示ipk文件安装成功，如果失败，重新安装或者寻找另外的。
6. opkg文件说明网址 http://wiki.openwrt.org/doc/techref/opkg

安装视频播放ipk使用文档
------------------

1. 按照ipk的使用文档要求，配置好文件

2. 编译好之后，在板子上测试，有可能会出现一些缺少库的现象，是用opkg list | grep  XXX 来查找自己需要的库是否存在，一般需要的都是与gst相关的，找到相似的，然后用opkg install 安装那些缺少的库。

目前需要安装的包：
 
+--------------------+------------------------------------------+
| Tip                |     plugins                              |
+====================+==========================================+
| playbin2           | #. gst-plugins-base-playbin              | 
|                    | #. gst-plugins-base-playbin-dev          |
+--------------------+------------------------------------------+
| uridecodebin       | #. gst-plugins-base-decodebin   		|
|		     | #. gst-plugins-base-decodebin-dev	|
|		     | #. gst-plugins-base-decodebin2		|
|		     | #. gst-plugins-base-decodebin2-dev	|
+--------------------+------------------------------------------+
| typefind	     | #. gst-plugins-base-typefindfunctions	|
|		     | #. gst-plugins-base-typefindfunctions-dev|
+--------------------+------------------------------------------+
| mux		     | #. gst-plugins-bad-mpegdemux		|
+--------------------+------------------------------------------+
| plug-in	     | #. gst-fsl-plugin			|
+--------------------+------------------------------------------+
| autovideosink	     | #. gst-meta-video			|
+--------------------+------------------------------------------+
| autoaudiosink	     | #. gst-meta-audio			|
+--------------------+------------------------------------------+
| libphononE.so.4    | #. libphonone4				|
+--------------------+------------------------------------------+
| backend            | #. packagegroup-fsl-gstreamer		|
+--------------------+------------------------------------------+
如果还缺少什么别的库，找相似的名字然后安装即可。

3. 用Qt编译下面的视频程序。

代码：

.. highlight:: c
.. code-block:: ruby

	#include "mainwindow.h"
	#include "ui_mainwindow.h"
	#include <QWidget>
	MainWindow::MainWindow(QWidget * parent) :
		QMainWindow(parent),ui(new Ui::MainWindow)
	{
	ui->setupUi(this);

	QWidget * widget = new QWidget;

        widget->setWindowTitle("Video Play");

        Phonon::VideoPlayer * videoPlayer =new Phonon::VideoPlayer(Phonon::VideoCategory, widget);
        videoPlayer->load(Phonon::MediaSource("video.mp4"));

        videoPlayer->play();
  }
