#移植avs-device-sdk

##1. 简介

为了支持avs，亚马逊提供了sdk,现在的目的是将sdk移植到以安卓为底层的环境上。

[avs-device-sdk](https://github.com/alexa/avs-device-sdk/wiki)

* ubuntu linux环境安装avs-device-sdk
* 移植avs-device-sdk到以安卓为底层的环境

已经编译好的工程

链接: https://pan.baidu.com/s/10rrIwMOwvi6wbdoTsvVbEQ 密码: xd5m

可以下载上面已经编译好的工程，安装步骤3.8将对应的库文件push到板子上测试执行。

##2. ubuntu linux环境安装avs-device-sdk 

[Ubuntu Linux Quick Start Guide](https://github.com/alexa/avs-device-sdk/wiki/Ubuntu-Linux-Quick-Start-Guide)

按照上述链接即可以很容易的在ubuntu环境上安装avs-device-sdk,出于方便考虑，下面移植的过程中，最好参考该链接中描述，生成一个类似的目录结构。

##3. 移植avs-device-sdk 

参考在ubuntu linux环境安装avs-device-sdk的步骤，设置一个如下的目录结构。

执行

```
mkdir avs_sdk_dir_arm && cd avs_sdk_dir_arm && mkdir dependencies && mkdir android_home && cd android_home && mkdir usr && cd usr && mkdir local && cd ../.. && mkdir sdk-folder && cd sdk-folder && mkdir sdk-build sdk-source third-party application-necessities
```

目录结构如下：

```
avs_sdk_dir_arm$ tree
.
├── android_home
│   └── usr
│       └── local
├── dependencies　
└── sdk-folder
    ├── application-necessities
    ├── sdk-build
    ├── sdk-source
    └── third-party
```

目录介绍:

* android_home : 编译工具链路径
* android_home/usr/local : 依赖库安装路径
* dependencies : 依赖库的源码
* sdk-folder : avs-device-sdk源码

修改`.bashrc`,设置环境变量,添加如下设置

```
PATH=$PATH:/workspace/avs_sdk_dir_arm/android_home/toolchain_12b/bin
```

修改结束之后`source .bashrc`或者重新打开一个终端，然后最好验证一下:

```
$ type arm-linux-androideabi-gcc
arm-linux-androideabi-gcc is /workspace/avs_sdk_dir_arm/android_home/toolchain_12b/bin/arm-linux-androideabi-gcc
```

**注意:**

编译curl、nghttp2等第三方库一定要执行make install 安装到　`android_home/usr/local`路径下，因为编译时会通过`pk-config`来查找对应的头文件等。

###3.1 编译zlib

参考链接: [常用库之四：zlib的交叉编译](https://blog.csdn.net/npy_lp/article/details/6991704)

* 设置环境变量CC(因为zlib的configure不支持设置CC,所以直接设置环境变量)

```
export CC=arm-linux-androideabi-gcc
```

* 生成Makefile

```
./configure --prefix=/workspace/avs_sdk_dir_arm/android_home/usr/local
```

* make
* make install

如下，已经安装到对应的目录:

```
milo@milo-OptiPlex-7040:/workspace/avs_sdk_dir_arm/android_home/usr/local/lib$ ls
libz.a  libz.so  libz.so.1  libz.so.1.2.11  pkgconfig
```

编译结束之后注意设置环境变量CC:

```
export CC=
```

### 3.2 编译openssl

* 生成Makefile

```
./Configure --cross-compile-prefix=arm-linux-androideabi- --openssldir=/workspace/avs_sdk_dir_arm/android_home/usr/local  --prefix=/workspace/avs_sdk_dir_arm/android_home/usr/local --sysroot=/workspace/avs_sdk_dir_arm/android_home/toolchain_12b/sysroot linux-x32  no-asm shared zlib
```

* 修改Makefile

删除-mx32,共两处

* make
* make install_sw (去文档安装)

查看安装结果:

```
milo@milo-OptiPlex-7040:/workspace/avs_sdk_dir_arm/android_home/usr/local/lib$ ls
engines-1.1  libcrypto.a  libcrypto.so  libcrypto.so.1.1  libssl.a  libssl.so  libssl.so.1.1  libz.a  libz.so  libz.so.1  libz.so.1.2.11  pkgconfig
```

### 3.3 编译nghttp2

* 查看android-config文件

**由于其中包含ANDROID_HOME，所以需要设置环境变量**

```
export ANDROID_HOME=/workspace/avs_sdk_dir_arm/android_home/
```

* 根据我们的`toolchain` 修改android-config文件中`TOOLCHAIN`项

```
$ cat android-config
#!/bin/sh
#
# nghttp2 - HTTP/2 C Library
#
# Copyright (c) 2013 Tatsuhiro Tsujikawa
#
# Permission is hereby granted, free of charge, to any person obtaining
# a copy of this software and associated documentation files (the
# "Software"), to deal in the Software without restriction, including
# without limitation the rights to use, copy, modify, merge, publish,
# distribute, sublicense, and/or sell copies of the Software, and to
# permit persons to whom the Software is furnished to do so, subject to
# the following conditions:
#
# The above copyright notice and this permission notice shall be
# included in all copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
# EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
# MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
# NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
# LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
# OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
# WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

if [ -z "$ANDROID_HOME" ]; then
    echo 'No $ANDROID_HOME specified.'
    exit 1
fi
PREFIX="$ANDROID_HOME"/usr/local
TOOLCHAIN="$ANDROID_HOME"/toolchain_12b
PATH="$TOOLCHAIN"/bin:"$PATH"

./configure \
    --disable-shared \
    --host=arm-linux-androideabi \
    --build=`dpkg-architecture -qDEB_BUILD_GNU_TYPE` \
    --with-xml-prefix="$PREFIX" \
    --with-openssl="$PREFIX" \
    --without-libxml2 \
    --disable-python-bindings \
    --disable-examples \
    --disable-threads \
    --prefix=$PREFIX \
    CC="$TOOLCHAIN"/bin/arm-linux-androideabi-gcc \
    CXX="$TOOLCHAIN"/bin/arm-linux-androideabi-g++ \
    CPPFLAGS="-fPIE -I$PREFIX/include" \
    PKG_CONFIG_LIBDIR="$PREFIX/lib/pkgconfig" \
    LDFLAGS="-fPIE -pie -L$PREFIX/lib"
```

* 执行./android-config
* 执行`./android-make`
* 执行`./android-make install`

查看安装结果:

```
milo@milo-OptiPlex-7040:/workspace/avs_sdk_dir_arm/android_home/usr/local/lib$ ls
engines-1.1  libcrypto.a  libcrypto.so  libcrypto.so.1.1  libnghttp2.a  libnghttp2.la  libssl.a  libssl.so  libssl.so.1.1  libz.a  libz.so  libz.so.1  libz.so.1.2.11  pkgconfig
```

编译结束之后，注意将环境变量设置为原来的值

```
export ANDROID_HOME=
```

### 3.4 编译libcurl

* 生成Makefile

```
./configure --host=arm-linux-androideabi CC=arm-linux-androideabi-gcc CXX=arm-linux-androideabi-g++ CFLAGS="-pie -fPIE" LDFLAGS="-pie -fPIE" PKG_CONFIG_LIBDIR=/workspace/avs_sdk_dir_arm/android_home/usr/local/lib/pkgconfig --prefix=/workspace/avs_sdk_dir_arm/android_home/usr/local --with-nghttp2=/workspace/avs_sdk_dir_arm/android_home/usr/local --with-ssl=/workspace/avs_sdk_dir_arm/android_home/usr/local --with-ca-bundle=ca-certificates.crt
```

参数解释：

* CFLAGS="-pie -fPIE" LDFLAGS="-pie -fPIE" : 保证curl执行文件在板子上可以执行


* --with-ca-bundle=ca-certificates.crt : 设置根证书的路径

注意查看是否支持openssl 和nghttp2

```
onfigure: Configured to build curl/libcurl:

  curl version:     7.54.0
  Host setup:       arm-unknown-linux-androideabi
  Install prefix:   /workspace/avs_sdk_dir_arm/android_home/usr/local　--with-nghttp2=/workspace/avs_sdk_dir_arm/android_home/usr/local
  Compiler:         arm-linux-androideabi-gcc
  SSL support:      enabled (OpenSSL)
  SSH support:      no      (--with-libssh2)
  zlib support:     enabled
  GSS-API support:  no      (--with-gssapi)
  TLS-SRP support:  enabled
  resolver:         default (--enable-ares / --enable-threaded-resolver)
  IPv6 support:     enabled
  Unix sockets support: enabled
  IDN support:      no      (--with-{libidn2,winidn})
  Build libcurl:    Shared=yes, Static=yes
  Built-in manual:  enabled
  --libcurl option: enabled (--disable-libcurl-option)
  Verbose errors:   enabled (--disable-verbose)
  SSPI support:     no      (--enable-sspi)
  ca cert bundle:   no
  ca cert path:     no
  ca fallback:      no
  LDAP support:     no      (--enable-ldap / --with-ldap-lib / --with-lber-lib)
  LDAPS support:    no      (--enable-ldaps)
  RTSP support:     enabled
  RTMP support:     no      (--with-librtmp)
  metalink support: no      (--with-libmetalink)
  PSL support:      no      (libpsl not found)
  HTTP2 support:    enabled (nghttp2)
  Protocols:        DICT FILE FTP FTPS GOPHER HTTP HTTPS IMAP IMAPS POP3 POP3S RTSP SMB SMBS SMTP SMTPS TELNET TFTP

  SONAME bump:     yes - WARNING: this library will be built with the SONAME
                   number bumped due to (a detected) ABI breakage.
                   See lib/README.curl_off_t for details on this.
```

* make
* make install

查看安装结果:

```
milo@milo-OptiPlex-7040:/workspace/avs_sdk_dir_arm/android_home/usr/local/lib$ ls
engines-1.1  libcrypto.so      libcurl.a   libcurl.so    libnghttp2.la  libssl.so      libz.a   libz.so.1       pkgconfig
libcrypto.a  libcrypto.so.1.1  libcurl.la  libnghttp2.a  libssl.a       libssl.so.1.1  libz.so  libz.so.1.2.11
```

### 3.5 编译sqlite3

* 生成Makefile

```
 ./configure CC=arm-linux-androideabi-gcc --host=arm-linux-androideabi --prefix=/workspace/avs_sdk_dir_arm/android_home/usr/local
```

* make
* make install

查看安装结果:

```
milo@milo-OptiPlex-7040:/workspace/avs_sdk_dir_arm/android_home/usr/local/lib$ ls
engines-1.1  libcrypto.so      libcurl.a   libcurl.so    libnghttp2.la  libsqlite3.la  libssl.a   libssl.so.1.1  libz.so    libz.so.1.2.11
libcrypto.a  libcrypto.so.1.1  libcurl.la  libnghttp2.a  libsqlite3.a   libsqlite3.so  libssl.so  libz.a         libz.so.1  pkgconfig
```

###3.6 mbedtls

下载mbedtls
https://tls.mbed.org/download

编译:
设置环境变量 CC CXX

修改Makefile，intsll路径

make SHARED=1

### 3.7 修改avs-device-sdk代码

因为avs-device-sdk中的SamppleApp的代码依赖gstreamer和portaudio，所以必须修改SamppleApp的相关代码去除对这些库的依赖。

另外avs-device-sdk的编译过程中，如果关掉对gstreamer和portaudio的依赖是不生成编译SamppleApp的Makefile的，所以还需要修改cmakelist.txt.

* 修改`/workspace/avs_sdk_dir_arm/sdk-folder/sdk-source/avs-device-sdk`下的CMakeLists.txt添加如下一行,目的是添加查找库的路径

```
link_directories(/workspace/avs_sdk_dir_arm/android_home/usr/local/lib)
```

* `cd /workspace/avs_sdk_dir_arm/sdk-folder/sdk-build`
* 执行`./build_avs.sh`

```
#!/bin/bash

PLATFORM_VERSION=21

#CROSS_COMPILE=/bws/avs/curl2/curl-android-build/ndk-standalone-toolchain/bin/arm-linux-androideabi-
CROSS_COMPILE=/workspace/avs_sdk_dir_arm/android_home/toolchain_12b/bin/arm-linux-androideabi-

#export NDK_HOME=/bws/avs/ndk/android-ndk-r15b-linux-x86_64/android-ndk-r15b
#export NDK_HOME=/home/milo/Downloads/toolchains/ndk/android-ndk-r12b
export NDK_HOME=/home/milo/Downloads/toolchains/ndk/android-ndk-r15c

export AR="$CROSS_COMPILE"ar
#export CC="$CROSS_COMPILE"clang
#export CXX="$CROSS_COMPILE"clang++
export CC="$CROSS_COMPILE"gcc
export CXX="$CROSS_COMPILE"g++
export LD="$CROSS_COMPILE"ld
export LINK=$CXX
export RANLIB="$CROSS_COMPILE"ranlib
export STRIP="$CROSS_COMPILE"strip


export ARCH_FLAGS=" -march=armv7-a -mfloat-abi=softfp -mfpu=vfpv3-d16 "
export ARCH_LINK=" -Wl,--fix-cortex-a8 -lnb* -lz"


export CPPFLAGS=" ${ARCH_FLAGS} -fpic -pie -fPIE -ffunction-sections -funwind-tables -fstack-protector -fno-strict-aliasing -finline-limit=64 -fpermissive "
export CXXFLAGS=" ${ARCH_FLAGS} -fpic -pie -fPIE -ffunction-sections -funwind-tables -fstack-protector -fno-strict-aliasing -finline-limit=64 -frtti -fexceptions "
export CFLAGS=" ${ARCH_FLAGS} -D__ANDROID_API__=$PLATFORM_VERSION -fpic -pie -fPIE -ffunction-sections -funwind-tables -fstack-protector -fno-strict-aliasing -finline-limit=64 "
export LDFLAGS=" ${ARCH_LINK} -pie -fPIE -L/workspace/avsdir3/android_home/usr/local/lib"


cmake ../sdk-source/avs-device-sdk/  \
  -DANDROID_PLATFORM=android-21  \
  -DANDROID_ABI=armeabi-v7a  \
  -DANDROID_STL=c++_shared  \
  -DCMAKE_INSTALL_PREFIX:PATH=./AVSD/out/armv7  \
  -DCMAKE_TOOLCHAIN_FILE=$NDK_HOME/build/cmake/android.toolchain.cmake  \
  -DCMAKE_BUILD_TYPE=Debug  \
  -DPORTAUDIO=OFF  \
  -DGSTREAMER_MEDIA_PLAYER=OFF  \
  -DAMAZON_KEY_WORD_DETECTOR=OFF  \
  -DKITTAI_KEY_WORD_DETECTOR=OFF  \
  -DSENSORY_KEY_WORD_DETECTOR=OFF  \
  -DACSDK_EMIT_SENSITIVE_LOGS=OFF ../sdk-source/avs-device-sdk  \
  -DCURL_LIBRARY=/workspace/avs_sdk_dir_arm/android_home/usr/local/lib/libcurl.so \
  -DCURL_INCLUDE_DIR=/workspace/avs_sdk_dir_arm/android_home/usr/local/include
  
  #-DCURL_LIBRARY=/bws/avs/curl2/curl-android-build/libcurl-android/curl/lib/armeabi-v7a/libcurl.a  \
  #-DCURL_INCLUDE_DIR=/bws/avs/curl2/curl-android-build/libcurl-android/curl/include  \


  #   -DGTEST_LIBRARY=/media/joel/0298EBFB98EBEADD/Alexa/AVSD/out/armv7/lib/libgtest.a
  # -DGTEST_MAIN_LIBRARY=/media/joel/0298EBFB98EBEADD/Alexa/AVSD/out/armv7/lib/libgtest_main.a
  # -DGTEST_INCLUDE_DIR=../ThirdParty/googletest-release-1.8.0/googletest/include
  # 
  # 
make 
```

### 3.8 导入依赖的库文件

导入时注意板子上已经存在`libcurl.so`,注意修改名称，不要将原来的文件覆盖。

`cd sdk-build`后打开对应项的注释执行下面的脚本

```
if false; then
./Authorization/CBLAuthDelegate/src/libCBLAuthDelegate.so
./PlaylistParser/src/libPlaylistParser.so
./DCFDelegate/src/libDCFDelegate.so
./CertifiedSender/src/libCertifiedSender.so
./AVSCommon/libAVSCommon.so
./RegistrationManager/src/libRegistrationManager.so
./AFML/src/libAFML.so
./CapabilityAgents/AIP/src/libAIP.so
./CapabilityAgents/SpeakerManager/src/libSpeakerManager.so
./CapabilityAgents/Alerts/src/libAlerts.so
./CapabilityAgents/System/src/libAVSSystem.so
./CapabilityAgents/ExternalMediaPlayer/src/libExternalMediaPlayer.so
./CapabilityAgents/PlaybackController/src/libPlaybackController.so
./CapabilityAgents/Notifications/src/libNotifications.so
./CapabilityAgents/Settings/src/libSettings.so
./CapabilityAgents/SpeechSynthesizer/src/libSpeechSynthesizer.so
./CapabilityAgents/AudioPlayer/src/libAudioPlayer.so
./CapabilityAgents/TemplateRuntime/src/libTemplateRuntime.so
./ContextManager/src/libContextManager.so
./ADSL/src/libADSL.so
./ACL/src/libACL.so
./Storage/SQLiteStorage/src/libSQLiteStorage.so
./KWD/src/libKWD.so
./ESP/src/libESP.so
./ApplicationUtilities/Resources/Audio/src/libAudioResources.so
./ApplicationUtilities/DefaultClient/src/libDefaultClient.so
fi

#SO_PATH="./ApplicationUtilities/Resources/Audio/src/libAudioResources.so"
#arm-linux-androideabi-strip 
#adb push   /system/lib

#arm-linux-androideabi-strip ./ApplicationUtilities/DefaultClient/src/libDefaultClient.so
#adb push ./ApplicationUtilities/DefaultClient/src/libDefaultClient.so /system/lib

#arm-linux-androideabi-strip ./DCFDelegate/src/libDCFDelegate.so 
#adb push ./DCFDelegate/src/libDCFDelegate.so /system/lib

#arm-linux-androideabi-strip  ./ESP/src/libESP.so
#adb push ./ESP/src/libESP.so /system/lib

#arm-linux-androideabi-strip ./CapabilityAgents/Alerts/src/libAlerts.so
#adb push  ./CapabilityAgents/Alerts/src/libAlerts.so /system/lib

#arm-linux-androideabi-strip ./CertifiedSender/src/libCertifiedSender.so
#adb push  ./CertifiedSender/src/libCertifiedSender.so /system/lib

#arm-linux-androideabi-strip ./CapabilityAgents/Notifications/src/libNotifications.so
#adb push  ./CapabilityAgents/Notifications/src/libNotifications.so /system/lib

#arm-linux-androideabi-strip ./ApplicationUtilities/Resources/Audio/src/libAudioResources.so
#adb push  ./ApplicationUtilities/Resources/Audio/src/libAudioResources.so /system/lib


#SO_PATH="./CapabilityAgents/PlaybackController/src/libPlaybackController.so"
#arm-linux-androideabi-strip  $SO_PATH
#adb push $SO_PATH /system/lib

#SO_PATH="./CapabilityAgents/SpeakerManager/src/libSpeakerManager.so"
#arm-linux-androideabi-strip  $SO_PATH
#adb push $SO_PATH /system/lib

#SO_PATH="./CapabilityAgents/SpeechSynthesizer/src/libSpeechSynthesizer.so"
#arm-linux-androideabi-strip  $SO_PATH
#adb push $SO_PATH /system/lib

#SO_PATH="./CapabilityAgents/Settings/src/libSettings.so"
#arm-linux-androideabi-strip  $SO_PATH
#adb push $SO_PATH /system/lib

#SO_PATH="./CapabilityAgents/TemplateRuntime/src/libTemplateRuntime.so"
#arm-linux-androideabi-strip  $SO_PATH
#adb push $SO_PATH /system/lib

#SO_PATH="./CapabilityAgents/AudioPlayer/src/libAudioPlayer.so"
#arm-linux-androideabi-strip  $SO_PATH
#adb push $SO_PATH /system/lib

#SO_PATH="./CapabilityAgents/ExternalMediaPlayer/src/libExternalMediaPlayer.so"
#arm-linux-androideabi-strip  $SO_PATH
#adb push $SO_PATH /system/lib

#SO_PATH="./CapabilityAgents/System/src/libAVSSystem.so"
#arm-linux-androideabi-strip  $SO_PATH
#adb push $SO_PATH /system/lib

#SO_PATH="./Authorization/CBLAuthDelegate/src/libCBLAuthDelegate.so"
#arm-linux-androideabi-strip  $SO_PATH
#adb push $SO_PATH /system/lib

#SO_PATH="./Storage/SQLiteStorage/src/libSQLiteStorage.so"
#arm-linux-androideabi-strip  $SO_PATH
#adb push $SO_PATH /system/lib

#SO_PATH="/workspace/avsdir3/sdk-folder/third-party/libsqlite3.so"
#arm-linux-androideabi-strip  $SO_PATH
#adb push $SO_PATH /system/lib

#SO_PATH="./ACL/src/libACL.so"
#arm-linux-androideabi-strip  $SO_PATH
#adb push $SO_PATH /system/lib

#SO_PATH="./ContextManager/src/libContextManager.so"
#arm-linux-androideabi-strip  $SO_PATH
#adb push $SO_PATH /system/lib

#SO_PATH="./RegistrationManager/src/libRegistrationManager.so"
#arm-linux-androideabi-strip  $SO_PATH
#adb push $SO_PATH /system/lib

#SO_PATH="./CapabilityAgents/AIP/src/libAIP.so"
#arm-linux-androideabi-strip  $SO_PATH
#adb push $SO_PATH /system/lib

#SO_PATH="./ADSL/src/libADSL.so"
#arm-linux-androideabi-strip  $SO_PATH
#adb push $SO_PATH /system/lib

#SO_PATH="./AFML/src/libAFML.so"
#arm-linux-androideabi-strip  $SO_PATH
#adb push $SO_PATH /system/lib

#SO_PATH="./AVSCommon/libAVSCommon.so"
#arm-linux-androideabi-strip  $SO_PATH
#adb push $SO_PATH /system/lib

#SO_PATH="/workspace/avsdir3/android_home/toolchain_15c/arm-linux-androideabi/lib/libc++_shared.so"
#adb push $SO_PATH /system/lib

#SO_PATH="/workspace/avsdir3/android_home/usr/local/lib/libcurl.so"
#arm-linux-androideabi-strip  $SO_PATH
#adb push $SO_PATH /system/lib

#SO_PATH="/workspace/avsdir3/android_home/usr/local/lib/libssl.so.1.1"
#arm-linux-androideabi-strip  $SO_PATH
#adb push $SO_PATH /system/lib

#SO_PATH="/workspace/avsdir3/android_home/usr/local/lib/libcrypto.so.1.1"
#arm-linux-androideabi-strip  $SO_PATH
#adb push $SO_PATH /system/lib

SO_PATH="/workspace/avsdir3/android_home/usr/local/lib/libz.so.1"
arm-linux-androideabi-strip  $SO_PATH
adb push $SO_PATH /system/lib

SO_PATH="/workspace/avsdir3/android_home/usr/local/lib/libz.so.1.2.11"
arm-linux-androideabi-strip  $SO_PATH
adb push $SO_PATH /system/lib
```



### 3.9 push到板子上执行

* 将AlexaClientSDKConfig.json放到板子上

```
# cat AlexaClientSDKConfig.json                           
{
   "deviceInfo":{
     "deviceSerialNumber":"123456dfkujdhf8",
     "clientId":"amzn1.application-oa2-client.94b5583d8287466a8d70686f88d7f60f",
     "productId":"my_device"
   },
   "cblAuthDelegate":{
       "databaseFilePath":"/data/avsdbfile/cblAuthDelegate.db"
   },
   "miscDatabase":{
       "databaseFilePath":"/data/avsdbfile/miscDatabase.db"
   },
   "alertsCapabilityAgent":{
       "databaseFilePath":"/data/avsdbfile/alerts.db"
   },
   "settings":{
       "databaseFilePath":"/data/avsdbfile/settings.db",
       "defaultAVSClientSettings":{
           "locale":"en-US"
       }
   },
   "certifiedSender":{
      "databaseFilePath":"/data/avsdbfile/certifiedSender.db"
   },
   "notifications":{
       "databaseFilePath":"/data/avsdbfile/notifications.db"
   }
}

```

* 将`ca-certificates.crt`放到板子上
* 在包含`ca-certificates.crt`文件的目录下执行SamppleApp

```
SamppleApp ./AlexaClientSDKConfig.json
```

### 3.10 去除unused DT entry: xxx

上面的程序执行的时候会打印很多警告：

```
WARNING: linker: SampleApp: unused DT entry: type 0x6ffffffe arg 0x14568
WARNING: linker: SampleApp: unused DT entry: type 0x6fffffff arg 0x2
WARNING: linker: libDCFDelegate.so: unused DT entry: type 0x6ffffffe arg 0x148f8
WARNING: linker: libDCFDelegate.so: unused DT entry: type 0x6fffffff arg 0x2
WARNING: linker: libESP.so: unused DT entry: type 0x6ffffffe arg 0x14d8
WARNING: linker: libESP.so: unused DT entry: type 0x6fffffff arg 0x1
```

参考链接http://www.dllhook.com/post/211.html，可以去除这些警告的打印。

[为什么会输出这些打印？](https://stackoverflow.com/questions/33206409/unused-dt-entry-type-0x1d-arg)



## 4.编译过程中出现的错误及注意事项

###4.1 error: undefined reference to 'stderr' 

编译zlib时出现了该错误，最终发现是NDK版本的问题。使用ndk16时，会报错，更改为低一点的版本，可以编译通过，最终修改为ndk12b才编译通过。

```
error:
arm-linux-androideabi-gcc -O3 -D_LARGEFILE64_SOURCE=1 -DHAVE_HIDDEN -o example example.o -L. libz.a
example.o:example.c:function test_compress: error: undefined reference to 'stderr'
example.o:example.c:function test_gzio: error: undefined reference to 'stderr'
example.o:example.c:function test_deflate: error: undefined reference to 'stderr'
example.o:example.c:function test_inflate: error: undefined reference to 'stderr'
```

### 4.2 error: undefined reference to 'pthread_atfork' 

编译openssl时,开始toolchain是ndk10,出现下面的错误，修改为ndk12之后，编译通过

```
${LDCMD:-arm-linux-androideabi-gcc} -pthread  -Wall -O3 --sysroot=/workspace/avsdir3/android_home/toolchain/sysroot -L.   \
		-o apps/openssl apps/asn1pars.o apps/ca.o apps/ciphers.o apps/cms.o apps/crl.o apps/crl2p7.o apps/dgst.o apps/dhparam.o apps/dsa.o apps/dsaparam.o apps/ec.o apps/ecparam.o apps/enc.o apps/engine.o apps/errstr.o apps/gendsa.o apps/genpkey.o apps/genrsa.o apps/nseq.o apps/ocsp.o apps/openssl.o apps/passwd.o apps/pkcs12.o apps/pkcs7.o apps/pkcs8.o apps/pkey.o apps/pkeyparam.o apps/pkeyutl.o apps/prime.o apps/rand.o apps/rehash.o apps/req.o apps/rsa.o apps/rsautl.o apps/s_client.o apps/s_server.o apps/s_time.o apps/sess_id.o apps/smime.o apps/speed.o apps/spkac.o apps/srp.o apps/storeutl.o apps/ts.o apps/verify.o apps/version.o apps/x509.o \
		 apps/libapps.a -lssl -lcrypto -lz -ldl -pthread 
./libcrypto.a(threads_pthread.o):threads_pthread.c:function fork_once_func: error: undefined reference to 'pthread_atfork'
collect2: error: ld returned 1 exit status
make[1]: *** [apps/openssl] Error 1
make[1]: Leaving directory `/workspace/avsdir3/openssl/openssl'
make: *** [all] Error 2
```

### 4.3 CURLE_SSL_CACERT

https://pranavprakash.net/2014/09/27/using-libcurl-with-ssl/

[curl_easy_perform](https://curl.haxx.se/libcurl/c/curl_easy_perform.html)

动态库导入成功，程序也可以正常执行了，但是此时报错`curl_easy_performfailed error(60)`,导致该error的原因是没有根证书，在pc上程序执行正常的原有是因为`/etc/ssl/certs`下保存有该证书，所以，从pc上将证书`/etc/ssl/certs/ca-certificates.crt`push到板子上。但是应该放到那个路径下面呢？查看curl　configure得出结论：

编译curl时，可以设置`--with-ca-bundle=ca-certificates.crt`来确定ca根证书的路径。上面编译的时候已经设置好了，所以只要导入到对应的位置即可。

### 4.4 CURLE_SSL_CONNECT_ERROR 

在SamppleApp编译出来执行时一直报错CURLE_SSL_CONNECT_ERROR,后来用`curl https://example.com`来测试，打印出了更详细一些的error内容如下：

```
curl: (35) error:04091077:rsa routines:int_rsa_verify:wrong signature length
```

这个问题导致的原有是openssl编译过程存在问题，之前使用的是openssl目录下的config命令来生成Makefile，但是生成的Makefile的PLATFORM一直是x86,后来使用Configure命令生成Makefile,问题解决。参考[openssl官方文档](https://wiki.openssl.org/index.php/Compilation_and_Installation)

### 4.5 编译libcurl时，openssl支持的这项一直是no

安装 openssh后，使用 curl 的 ./configure --with-ssl 时，报错“找不到 ssl”。因为 curl在 
/usr/local/ssl的安装目录下找动态连接库。而ssl默认不生成动态连接库。解决办法是编译 ssl时使用 enable-shared  生成 动态连接库



###4.6 curl可执行程序报错`error: only position independent executables (PIE) are supported.`

之前遇见此问题，直接修改Makefile添加-pie -fPIE参数之后重新编译即可，但是对于curl的工程，仍然报错，后来使用configure 生成Makefile时即设置CFLAGS="-pie -fPIE" LDFLAGS="-pie -fPIE"，即可避免该问题。

###4.7 编译一个最简单的SamppleApp时如何修改CMakeList.txt 

查看SamppleApp中的CMakeList.txt,发现必须打开PORTAUDIO　和　GSTREAMER_MEDIA_PLAYER才能编译src，为了使用使用cmake生成Makefile，所以注释掉这一项检查,这样设置下面两项时，仍然能够生成编译SamppleApp的Makefile文件。

-DGSTREAMER_MEDIA_PLAYER=OFF 

-DPORTAUDIO=OFF

```
cmake $(sdkpath) -DSENSORY_KEY_WORD_DETECTOR=OFF -DCMAKE_BUILD_TYPE=DEBUG -DGSTREAMER_MEDIA_PLAYER=OFF -DPORTAUDIO=OFF -DPORTAUDIO_LIB_PATH=/workspace/avssdk/sdk-folder/third-party/portaudio/lib/.libs/libportaudio.a -DPORTAUDIO_INCLUDE_DIR=/workspace/avssdk/sdk-folder/third-party/portaudio/include && make
11
```

```
cmake_minimum_required(VERSION 3.1 FATAL_ERROR)
project(SampleApp LANGUAGES CXX)
include(../build/BuildDefaults.cmake)
#if(PORTAUDIO AND GSTREAMER_MEDIA_PLAYER)
add_subdirectory("src")
#endif()
```

首先编译一个最简单的SamppleApp，只包含main.cpp，里面只有一句打印，所以修改　SamppleApp/src/CmakeList.txt文件,去除依赖的库和其他文件。

```
set(SampleApp_SOURCES)
#if(0)
list(APPEND SampleApp_SOURCES 
    ConnectionObserver.cpp
    ConsolePrinter.cpp
    GuiRenderer.cpp
    InteractionManager.cpp
    KeywordObserver.cpp
    PortAudioMicrophoneWrapper.cpp
    UIManager.cpp
    UserInputManager.cpp
    SampleApplication.cpp
    main.cpp)
#endif()

list(APPEND SampleApp_SOURCES
    main.cpp)

IF (HAS_EXTERNAL_MEDIA_PLAYER_ADAPTERS)
    file(GLOB_RECURSE SRC_FILE ${CMAKE_CURRENT_SOURCE_DIR}/ExternalMediaAdapterRegistration/*.cpp)
    foreach(myfile ${SRC_FILE})
       list(APPEND SampleApp_SOURCES ${myfile})
    endforeach(myfile)
ENDIF()

add_executable(SampleApp ${SampleApp_SOURCES})

#if(0)
target_include_directories(SampleApp PUBLIC 
    "${SampleApp_SOURCE_DIR}/include"
    "${MediaPlayer_SOURCE_DIR}/include"
    "${AudioResources_SOURCE_DIR}/include"
    "${RegistrationManager_SOURCE_DIR}/include"
    "${ESP_SOURCE_DIR}/include"
    "${PORTAUDIO_INCLUDE_DIR}")
#endif()

target_link_libraries(SampleApp 
    DefaultClient
    DCFDelegate
    CBLAuthDelegate
    MediaPlayer
    SQLiteStorage
    ESP
    "${PORTAUDIO_LIB_PATH}")

if(KWD)
    target_link_libraries(SampleApp KeywordDetectorProvider)
endif()

if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
    target_link_libraries(SampleApp
        "-framework CoreAudio" 
        "-framework AudioToolbox" 
        "-framework AudioUnit" 
        "-framework CoreServices" 
        "-framework Carbon")
elseif(${CMAKE_SYSTEM_NAME} MATCHES "Linux")
    target_link_libraries(SampleApp
      rt m pthread asound)
endif()
```

编译能够成功，但是执行的时候，会报错

```
error: only position independent executables (PIE) are supported.
```

之前遇见过类似问题，编译时添加　-pie -fPIE 参数
修改　avs-device-sdk/build/cmake/BuildOptions.cmake,

```
51 set(CXX_PLATFORM_DEPENDENT_FLAGS_DEBUG      "-DDEBUG -DACSDK_DEBUG_LOG_ENABLED -Wall -Werror -Wsign-compare -g")
修改为
51 set(CXX_PLATFORM_DEPENDENT_FLAGS_DEBUG      "-DDEBUG -DACSDK_DEBUG_LOG_ENABLED -Wall -Wsign-compare -g -pie -fPIE")
```

注意要删除其中的-Werror参数，否则会报告如下错误。

```
clang++: error: argument unused during compilation: '-pie' [-Werror,-Wunused-command-line-argument]
make[2]: *** [AVSCommon/CMakeFiles/AVSCommon.dir/AVS/src/AVSDirective.cpp.o] Error 1
make[1]: *** [AVSCommon/CMakeFiles/AVSCommon.dir/all] Error 2
make: *** [all] Error 2
```

之后编译成功，执行时需要libc++_shared.so

```
WARNING: linker: SampleApp: unused DT entry: type 0x6ffffffe arg 0x770
WARNING: linker: SampleApp: unused DT entry: type 0x6fffffff arg 0x3
CANNOT LINK EXECUTABLE DEPENDENCIES: library "libc++_shared.so" not found
```

NDK或者使用编译工具链中可以找到该动态库，push到板子上执行，结果正确,至于unused DT entry问题，还没有确认是什么问题，不过，目前已经可以执行了。

```
WARNING: linker: SampleApp: unused DT entry: type 0x6ffffffe arg 0x770
WARNING: linker: SampleApp: unused DT entry: type 0x6fffffff arg 0x3
WARNING: linker: libc++_shared.so: unused DT entry: type 0x6ffffffe arg 0x2ab10
WARNING: linker: libc++_shared.so: unused DT entry: type 0x6fffffff arg 0x3
-----------------start!
```

## 参考链接

[nghttp2官网描述的编译过程](http://www.nghttp2.org/documentation/building-android-binary.html)

[干货:pkg-config工具在实际工程中的用法](https://blog.csdn.net/mantis_1984/article/details/52847435)

[libcurl官方文档](https://curl.haxx.se/libcurl/)

[openssl官方文档](https://wiki.openssl.org/index.php/Compilation_and_Installation)

openssl、x509、crt、cer、key、csr、ssl、tls 这些都是什么鬼? 
https://www.cnblogs.com/lan1x/p/5872915.html

公钥，私钥，数字签名，数字证书详解
https://blog.csdn.net/sum_rain/article/details/36896239

HTTPS 客户端验证 服务端证书流程
https://blog.csdn.net/xiaofei0859/article/details/70747034

Linux系统中如何维护自己的openssl ca-bundle
https://www.cnblogs.com/wanggan9/articles/5479386.html


