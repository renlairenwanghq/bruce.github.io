##ALSA

[alsa架构简介](https://wenku.baidu.com/view/c4bbd2000740be1e650e9ab8.html)

[linux音频结构](http://www.360doc.com/content/15/0921/17/97538_500543275.shtml)

[Linux下音频设备的操作](https://blog.csdn.net/z2066411585/article/details/39896267)

[alas移植](https://blog.csdn.net/yuanxinfei920/article/details/52954941)

### 编译

* 生成makefile

```
sudo ./configure --prefix=/home/milo/Downloads/alsaout --enable-shared --disable-python --with-configdir=/usr/local/share/alsa --with-plugindir=/usr/local/lib/alsa_lib
```

* sudo make
* sudo make install

### 交叉编译alsa

####編譯alsa-lib

该流程是交叉编译到**安卓环境**，所以有了一步修改LDFLAGS的步骤，这是在安卓底层时使用gnulib,因为安卓使用的bionic.当编译成linux平台的时候应该是不需要修改LDFLAGS的。

* 生成makefile

```
sudo ./configure --host=arm-linux-gnueabihf --prefix=/home/milo/Downloads/alsaout --enable-shared --disable-python --with-configdir=/usr/local/share/alsa --with-plugindir=/usr/local/lib/alsa_lib CC=/home/milo/Downloads/toolchains/linux_toolchain/gcc-linaro-5.4.1-2017.05-i686_arm-linux-gnueabihf/bin/arm-linux-gnueabihf-gcc  ld=/home/milo/Downloads/toolchains/linux_toolchain/gcc-linaro-5.4.1-2017.05-i686_arm-linux-gnueabihf/bin/arm-linux-gnueabihf-ld
```

输出

```
milo@milo-OptiPlex-7040:~/Downloads/alsaout/bin$ adb shell
root@zipp_mini:/ # aserver                                                     
/system/bin/sh: aserver: No such file or directory
1|root@zipp_mini:/ #  
```

* 修改LDFLAGS

```
sudo ./configure --host=arm-linux-gnueabihf --prefix=/home/milo/Downloads/alsaout --enable-shared --disable-python --with-configdir=/system/lib/gnulib/alsa --with-plugindir=/system/lib/gnulib CC=/home/milo/Downloads/toolchains/linux_toolchain/gcc-linaro-5.4.1-2017.05-i686_arm-linux-gnueabihf/bin/arm-linux-gnueabihf-gcc  LD=/home/milo/Downloads/toolchains/linux_toolchain/gcc-linaro-5.4.1-2017.05-i686_arm-linux-gnueabihf/bin/arm-linux-gnueabihf-ld CFLAGS="-Wl,-rpath,/system/lib/gnulib" LDFLAGS="-Wl,-dynamic-linker=/system/lib/gnulib/ld-linux-armhf.so.3"
```

* sudo make
* sudo make install



####編譯alsa-util

```
./configure --host=arm-linux-gnueabihf --prefix=/home/milo/Downloads/alsautilout CFLAGS="-I/home/milo/Downloads/alsaout/include -Wl,-rpath,/system/lib/gnulib" LDFLAGS="-L/home/milo/Downloads/alsaout/lib -lasound -Wl,-dynamic-linker=/system/lib/gnulib/ld-linux-armhf.so.3" --disable-alsamixer --disable-xmlto --with-alsa-inc-prefix=/home/milo/Downloads/alsaout/include --with-alsa-prefix=/home/milo/Downloads/alsaout/lib CC=arm-linux-gnueabihf-gcc CXX=arm-linux-gnueabihf-g++ LD=arm-linux-gnueabihf-ld
```

參考資料
aplay arecord 使用
https://www.cnblogs.com/cslunatic/p/3227655.html

http://blog.sina.com.cn/s/blog_753b6c170102uyo3.html

alsa 官網
http://www.alsa-project.org/main/index.php/Download

C++ 用libcurl库进行http通讯网络编程
https://www.cnblogs.com/moodlxs/archive/2012/10/15/2724318.html

pc环境

```
sudo ./configure --prefix=/home/milo/Downloads/alsaoutpc --enable-shared --disable-python --with-configdir=/system/lib/gnulib/alsa --with-plugindir=/system/lib/gnulib 

./configure  --prefix=/home/milo/Downloads/alsautiloutpc --disable-alsamixer --disable-xmlto --with-alsa-inc-prefix=/home/milo/Downloads/alsaoutpc/include --with-alsa-prefix=/home/milo/Downloads/alsaoutpc/lib
```



###alsa 工具使用

```
播放： $ aplay  musicdemo.wmv
录音： $ arecord -c 2 -r 44100 -f S16_LE musicdemo.wmv
调节音量大小: $ alsamixer 
```



