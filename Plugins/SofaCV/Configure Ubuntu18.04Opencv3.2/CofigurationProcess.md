参考：
 - https://blog.csdn.net/cyfzwj/article/details/111991295
 - https://blog.csdn.net/autoliuweijie/article/details/53561473


## ubuntu18.04中安装opencv3.2
### 基本环境
ubuntu18.04

### 需求
因为需要使用SofaCV的插件，需要从git上安装OpenCV 3.2版本。
手动下载opencv3.2和opencv_contrib3.2的源码，然后将两个源码放在一起手动编译安装。

### 下载opencv3.2和opencv_contrib3.2
去opencv的github下载源码到 ~/
````
$ cd ~/
$ wget https://github.com/opencv/opencv/archive/3.2.0.zip
$ unzip 3.2.0.zip
$ rm 3.2.0.zip
$ wget https://github.com/opencv/opencv_contrib/archive/3.2.0.zip
$ unzip 3.2.0.zip
$ rm 3.2.0.zip
————————————————
````
此时 ~/ 目录下应有如下两个文件夹：
````
opencv-3.2.0  
opencv_contrib-3.2.0
````

### 开始安装
### 一、安装必要的库

注意：有些库在配置SOFA的时候已经装过了
````
sudo apt-get install build-essential 
sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev
````
如果上面命令执行会报错，错误说libjaster-dev无法安装，解决办法如下：
````
sudo add-apt-repository "deb http://security.ubuntu.com/ubuntu xenial-security main"
sudo apt update
sudo apt install libjasper1 libjasper-dev
````
配置我们使用cmake-gui
```
sudo apt install cmake-gui
```

### 二、安装前准备

 - 创建build文件夹
 - 创建安装文件夹
````
$ cd ~/opencv-3.2
$ mkdir build
$ mkdir -p ~/opencv_install/3.2/release
````

### 三、配置参数

直接在终端中输入cmake-gui，打开cmake图形配置界面
添加参数
````
where is the source code  :            /home/hh/opencv-3.2
where to build the binary :            /home/hh/opencv-3.2/build
````

 - 点击 Configure
 - 然后在主界面会检测出需要配置的参数，在下面的调试界面会产生各种编译文件检查信息

### 四、解决问题
修改参数
|参数名	|参数值	|原因|
|---    |---    |--- |
|CMAKE_BUILD_TYPE	|Release| | 
|OPENCV_EXTRA_MODULES_PATH|	/home/hh/opencv_contrib-3.2.0/modules|	这就是SIFT和SURF特征提取算法所在的地方|
|CMAKE_INSTALL_PREFIX|	/home/hh/opencv_install/3.2/release|	这就是编译完成后安装的库文件和头文件的位置|
|ENABLE_PRECOMPILED_HEADERS|	OFF	|如果不关闭这个参数的话，后面编译会报错误/usr/include/c++/7/cstdlib:75:15:fatal error:stdlib.h:No such file or direct.|
|OPENCV_ENABLE_NONFREE|	ON	|使用各种算子都要引用nonfree.hpp头文件，并且3.2中已经废弃的legacy.hpp头文件，至于如何使用3.2中的特征提取方法大家就自己学习去吧|

修复调试界面出现的各种问题
1）ippicv_linux_20151201.tgz文件无法下载，下载失败。

          $ cd /home/hh/opencv/opencv-3.2/3rdparty/ippicv/downloads/linux-xxxxxxx/ippicv_linux_20151201.tgz
              ippicv_linux_20151201.tgz，该文件大小为0kb

解决办法：

在Gitee上搜索ippicv，下载对应版本 （20151201） 的该文件，并将下载的文件替换原来0kb的文件就好。
2）boostdesc_bgm* 和 vgg_generated* 共11个文件（注意，我这里使用的*代表一个通配符），下载失败。包括boostdesc_bgm_bi.i、vgg_generated_48等文件，进入下面的文件夹之后你会发现一些以hash值命名的文件夹，其中虽然有你需要的文件，但是每个文件的大小都是0kb。

        $ cd /home/hh/opencv/opencv_contrib-3.2/modules/xfeatures2d/cmake/.download   这是下载文件夹
        $ cd /home/hh/opencv/opencv_contrib-3.2/modules/xfeatures2d/cmake/ 这是下载脚本所在的文件夹，在这里你会看到boostdesc和vgg下载脚本
1
2
解决办法：

在csdn中直接搜索文件名，开源大神都已上传（但是没VIP你估计拿不到）；github官网；gitee官网都可以找到。
将download_boostdesc.cmake和download_vgg.cmake文件中最后几行的boostdesc_download和vgg_download注释掉，免的再次configure的时候又使用原下载链接再下载一遍，导致重新下载了一个0kb的错误文件。
然后将你下载好的文件boostdesc_bgm* 和 vgg_generated*放入以下路径
````
/home/hh/opencv/opencv_contrib-3.2/modules/xfeatures2d/src
````

### 五、重新配置
点击Configure
点击Generate，生成makefile文件
### 六、编译安装
````
$ cd ~/opencv-3.2/build
$ make
$ make install
````
### 七、配置环境
在你的home目录中的 .profile 环境配置文件中添加以下内容，方便 编译程序 时查找库文件。
````
$ cd ~/.profile
export MY_OPENCV=/home/hh/opencv/opencv-install/opencv-3.2/release
export CLASSPATH=${MY_OPENCV}/lib:$CLASSPATH
export PATH=${MY_OPENCV}/bin:$PATH
export PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/home/cloud/opencv/opencv-install/opencv-3.2/release/lib/pkgconfig
$ source ~/.profile
````

在文件 /etc/ld.so.conf 中新开一行直接添加以下内容，方便 执行程序 执行时查找动态库文件。
````
$ sudo vim /etc/ld.so.conf
/home/hh/opencv/opencv-install/opencv-3.2/release/lib
````
至此，你就可以在opencv3.2中使用各种特征提取算法了。
至于为什么要设置最后一个变量 PKG_CONFIG_PATH，是为了方便使用命令 pkg-config 获取opencv库而添加的。
