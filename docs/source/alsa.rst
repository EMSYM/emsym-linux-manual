.. _alsa:

ALSA播放WAV音频
============================


直接编译
----------------------------

- **PC环境安装**

从ALSA官网（https://www.alsa-project.org/main/index.php/Main_Page）下载alsa-lib，这里以alsa-lib-1.1.5为例。
解压下载下来的ALSA库压缩包，并进入解压后的文件目录：
::

    tar -xjf alsa-lib-1.1.5.tar.bz2
    cd alsa-lib-1.1.5
    
配置、编译、安装：
::

    sudo ./configure
    sudo make
    sudo make install

- **直接编译**

在PC（Linux操作系统）上，打开终端，进入到c源文件所在的目录，编译时连接ALSA库和线程库：
::

    gcc wavplayer.c -o wavplayer -lasound -lpthread

然后把wav音频文件（high.wav，middle.wav，low.wav）拷贝到当前目录下，运行刚刚生成的可执行文件：
::

    ./wavplaye

在PC的音响中就可以听到播放出的wav音频。


交叉编译
----------------------------

- **PC环境安装**

在这里要使用arm-linux-gnueabihf工具链来交叉编译c源文件，生成的可执行文件才可以移植到blurr板上运行。
首先在Linux系统上要安装工具链：
::

    sudo apt-get install gcc-arm-linux-gnueabihf

为避免一些错误，再执行下面这条命令：
::

    sudo apt-get install build-essential gcc

然后安装ALSA库：
::

    sudo rpm2cpio alsa-lib-dev-1.1.0-r0.cortexa9hf_neon_mx6qdl.rpm | cpio -idv
    sudo cp usr/* -a /usr/arm-linux-gnueabihf

注：如果该rpm包/usr/lib中缺少“libasound.so.2.0.0”文件，再将该文件拷贝进去：
::

    sudo cp libasound.so.2.0.0 /usr/arm-linux-gnueabihf/lib

- **交叉编译**

交叉编译时，需要连接lasound和lpthread库：
::

    arm-linux-gnueabihf-gcc wavplayer.c -o wavplayer -lasound -lpthread

- **Blurr环境安装**

在blurr板上也需要安装ALSA库：
::

    rpm -ivh alsa-lib-dev-1.1.0-r0.cortexa9hf_neon_mx6qdl.rpm

- **运行**

将交叉编译好的可执行文件wavplayer和wav音频文件（high.wav，middle.wav，low.wav）通过U盘拷贝到blurr板上。
首先将U盘插在blurr板的USB口上，挂载进来：
::

    mount /dev/sda1 /mnt

拷贝可执行文件、音频文件到用户目录下（alsa为自己创建的文件夹）：
::

    cd /mnt
    cp wavplayer ~/alsa
    cp *.wav ~/alsa

运行：
::

    cd ~/alsa
    ./wavplayer

将耳机插入blurr板绿色耳机孔中，就可以听到播放出的wav音频。

