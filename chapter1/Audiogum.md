#集成AudiogumSDK

##简介

[audiogum文档](https://www.audiogum.com/developers/docs)

```
[ro.id.audiogum]: [163faecca7b548d9abc48a9a2ccf5ab4]
[ro.secret.audiogum]: [fZ/8/+KbExPXzfgUNaQ6aVIMToaQWj+QHIuqasStEjDdLZnlkqOHHu/8Z6nyysAa]
```

## 1. websocket

[websocket教程](https://www.cnblogs.com/jingmoxukong/p/7755643.html)

###1.1 websocket 协议介绍

https://www.cnblogs.com/jice1990/p/5435419.html

https://www.cnblogs.com/yjf512/archive/2013/03/11/2953483.html

**websocket在线测试工具**

http://ws.douqq.com/

**libwebsocket中的测试工具**

libwebsockets_local/minimal-examples

**websocket教程(包含示例)**

http://www.ruanyifeng.com/blog/2017/05/websocket.html

###1.2 libwebsocket

[libwebsockets（一）简介](https://blog.csdn.net/u013780605/article/details/79489183)

[libwebsockets（二）实现简易http服务器](https://blog.csdn.net/u013780605/article/details/79489193)

[libwebsockets（三）实现简易websocket服务器](https://blog.csdn.net/u013780605/article/details/79489197)

[libwebsockets（四）增加ssl支持](https://blog.csdn.net/u013780605/article/details/79489206)

[libwebsocket 交叉编译及应用注意细节](https://blog.csdn.net/elvis02/article/details/78295612)

[libwebsocket官方文档](https://libwebsockets.org/lws-api-doc-master/html/modules.html)

**主要数据结构&函数**

```
struct lws_context_creation_info info;
```

```
lws_create_context();
```

```
struct lws_client_connect_info;
```

```
lws_client_connect_via_info;
```



**下载**

```
git clone https://github.com/warmcat/libwebsockets.git
```

####1.2.1 libwebsocket v3.0.0

**本地编译**

```
git clone https://github.com/warmcat/libwebsockets.git
cd libwebsockets
mkdir build
cd build
cmake ..
make
sudo make install
sudo ldconfig
```

**交叉编译**

**注意**

此脚本是编译`libwebsocket v3.0.0`时的脚本，但是使用该版本会导致audiogum example的程序运行返回404,所以最终使用`libwebsocket v2.4`版本。v2.4版本的编译参照章节`1.2.2`.

cat build.sh

```
#!/bin/bash

export CROSS_COMPILE=/workspace/avs_sdk_dir_arm/android_home/toolchain_15c/bin/arm-linux-androideabi-
export SYSROOT=/workspace/avs_sdk_dir_arm/android_home/toolchain_15c/sysroot

#export CROSS_COMPILE=/workspace/avs_sdk_dir_arm/android_home/toolchain_12b/bin/arm-linux-androideabi-
#export SYSROOT=/workspace/avs_sdk_dir_arm/android_home/toolchain_12b/sysroot

export CC="$CROSS_COMPILE"gcc
export CXX="$CROSS_COMPILE"g++
export LD="$CROSS_COMPILE"ld
export LINK=$CXX
export RANLIB="$CROSS_COMPILE"ranlib
export STRIP="$CROSS_COMPILE"strip
export CFLAGS=" -I ${SYSROOT}/usr/include/  -Wno-type-limits -Wno-implicit-function-declaration"
export CXXFLAGS=" -I ${SYSROOT}/usr/include/ -I  -Wno-type-limits"
export LDFLAGS=" -L ${SYSROOT}/usr/lib/ -L /workspace/deltan_0815/vendor/libratone/sdk/avs-ndk2/android_home/usr/local/lib"

cmake .. \
	-DCMAKE_C_COMPILER=$CC \
	-DOPENSSL_ROOT_DIR=/workspace/deltan_0815/vendor/libratone/sdk/avs-ndk2/android_home/usr/local/lib/ \
	-DOPENSSL_INCLUDE_DIR=/workspace/deltan_0815/vendor/libratone/sdk/avs-ndk2/android_home/usr/local/include \
    -DOPENSSL_LIBRARIES=libssl.so \
	-DLWS_WITHOUT_TESTAPPS=ON
```

**参数解释**

* DLWS_WITHOUT_TESTAPPS 

  如果不添加该参数，会编译 test_apps下的代码，源文件中使用了stderr，但是我们使用的是ndk15,该ndk是不支持stderr的，但是如果使用ndk12此时又会出现某个头文件找不到的问题，所以决定不编译test_apps下的文件


[](https://blog.csdn.net/Best_ZYJ/article/details/79545222)

#### 1.2.2 libwebsocket v2.4

**注意**

* 由于libwebsocket最新版本v3.0导致audiogum example运行异常，所以使用libwebsocket版本v2.4**
* 在`libwebsockets-2.4-stable_cross/lib/libwebsocket.h`第122行添加代码`#include <netinet/in.h>`,否则会报错`'struct sockaddr_in' declared inside parameter list`

**获取代码**

https://github.com/warmcat/libwebsockets/tree/v2.4-stable

**生成makefile的脚本　build.sh**

```
#!/bin/bash

#export CROSS_COMPILE=/workspace/avs_sdk_dir_arm/android_home/toolchain_15c/bin/arm-linux-androideabi-
#export SYSROOT=/workspace/avs_sdk_dir_arm/android_home/toolchain_15c/sysroot

export CROSS_COMPILE=/workspace/avs_sdk_dir_arm/android_home/toolchain_12b/bin/arm-linux-androideabi-
export SYSROOT=/workspace/avs_sdk_dir_arm/android_home/toolchain_12b/sysroot

export CC="$CROSS_COMPILE"gcc
export CXX="$CROSS_COMPILE"g++
export LD="$CROSS_COMPILE"ld
export LINK=$CXX
export RANLIB="$CROSS_COMPILE"ranlib
export STRIP="$CROSS_COMPILE"strip
export CFLAGS=" -I ${SYSROOT}/usr/include/  -Wno-type-limits -Wno-implicit-function-declaration"
export CXXFLAGS=" -I ${SYSROOT}/usr/include/ -I  -Wno-type-limits"
export LDFLAGS=" -L ${SYSROOT}/usr/lib/ -L /workspace/deltan_0815/vendor/libratone/sdk/avs-ndk2/android_home/usr/local/lib"

cmake .. \
	-DCMAKE_C_COMPILER=$CC \
	-DLWS_WITH_BUNDLED_ZLIB=OFF\
	-DLWS_WITHOUT_DAEMONIZE=ON \
	-DCMAKE_C_FLAGS="$CFLAGS" \
	-DOPENSSL_ROOT_DIR=/workspace/deltan_0815/vendor/libratone/sdk/avs-ndk2/android_home/usr/local/lib/ \
	-DLWS_OPENSSL_INCLUDE_DIR=/workspace/deltan_0815/vendor/libratone/sdk/avs-ndk2/android_home/usr/local/include \
    -DLWS_OPENSSL_LIBRARIES=/workspace/deltan_0815/vendor/libratone/sdk/avs-ndk2/android_home/usr/local/lib/libss.a \
	-DZLIB_INCLUDE_DIR=/workspace/deltan_0815/vendor/libratone/sdk/avs-ndk2/android_home/usr/local/include \
	-DZLIB_LIBRARY=/workspace/deltan_0815/vendor/libratone/sdk/avs-ndk2/android_home/usr/local/lib/libz.a \
	-DLWS_WITHOUT_TESTAPPS=ON
	
	#-DCMAKE_BUILD_TYPE=Debug
```



## 2. 交叉编译libaudiogum

**注意**

* 在pc平台可以访问到安装到`usr/include`下的websocket的相关头文件，但是修改编译器和平台后，会报错找不到websocket的某些头文件，需要设置websocket的头文件路径
* 在pc下存在uuid的库，在板子上不存在，需要交叉编译libuuid，并设置uuid的路径

**修改Makefile**

```
IDIR=include
EXTERNINCLUDES=external_libs
SRCDIR =src
TARGET=target
EXTERN_FILES = $(shell find external_libs -name '*.c')
SRC = $(shell find src -name '*.c')
SRC += $(EXTERN_FILES)
CROSS_COMPILE=/workspace/avs_sdk_dir_arm/android_home/toolchain_15c/bin/arm-linux-androideabi-
SYSROOT=/workspace/avs_sdk_dir_arm/android_home/toolchain_15c/sysroot
ANDROID_HOME=/workspace/deltan_0815/vendor/libratone/sdk/avs-ndk2/android_home/usr/local/

CC := $(CROSS_COMPILE)gcc
AR = $(CROSS_COMPILE)ar

CFLAGS += -I $(SYSROOT)/usr/include -I $(ANDROID_HOME)/include -I ./include/websocket 
LDFLAGS +=  -L $(SYSROOT)/usr/include -L $(ANDROID_HOME)/lib -L ./libs/websocket

# creates target/libaudiogum.a

all: libs

$(info $(CC))
audiogum.o: $(SRCDIR)/audiogum.c $(SRCDIR)/aghttp.c include/audiogum.h
	@$(CC) -std=gnu99 -Wall -O -c $(CFLAGS) $(SRC) -I$(IDIR) -I$(EXTERNINCLUDES) $(LDFLAGS)

audiogum.a: audiogum.o
	@mkdir -p $(TARGET)
	@$(AR) rcs $(TARGET)/libaudiogum.a audiogum.o cJSON.o aghttp.o
	@echo "Created $(TARGET)/libaudiogum.a"

libs: audiogum.a

clean:
	@rm -f *.o $(TARGET)/*.a
```

## 3. 交叉编译example/remotecontrol

**注意**

* 该编译依赖uuid的库，所以要提前编译好libuuid.如何编译libuuid,参照下面`移植uuid`。
* 使用ndk15会遇到`stdin`和`stderr`不存在的问题，所以要使用nkd12来编译
* websocket编译使用ndk12时会报错`sys/syslog.h`找不到，但是当编译remotecontrol链接使用nkd15编译的libwebsocket时仍然会报错websocket中`stderr`不存在，所以最终修改websocket的代码，把使用stderr的地方注释掉，此时可以编译成功，编译remotecontrol时也不会报错
* 交叉编译之后，在设备上执行时，会报错`ws_ssl_client_connect2 failed`,这是因为根证书的路径设置的不正确，在代码中添加一行代码src/audiogum.c第1867行添加代码`info.client_ssl_ca_filepath="./ca-certificates.crt";`设置一下根证书的路径，否则会按照系统默认设置查找。

**修改Makefile**

```
APPNAME=remotecontrol
IDIR=../../include
LIBS=-laudiogum -L../../target -lwebsockets -lcurl
#LIBS= -L../../target ../../target/libaudiogum.a ../../libs/websocket/libwebsockets.a -lcurl
UNAME := $(shell uname -s)
ifeq ($(UNAME),Linux)
    #LIBS += -lm -lssl -lcrypto -lpthread -luuid
    LIBS += -lm -lssl -lcrypto  -luuid
    #LIBS += -lm /workspace/deltan_0815/vendor/libratone/sdk/avs-ndk2/android_home/usr/local/lib/libssl.a $(ANDROID_HOME)/lib/libcrypto.a  -luuid -lz
endif
LIBS += $(LIB)
#CC := gcc

$(warning  $(LIBS)) 
#CROSS_COMPILE=/workspace/avs_sdk_dir_arm/android_home/toolchain_15c/bin/arm-linux-androideabi-
#SYSROOT=/workspace/avs_sdk_dir_arm/android_home/toolchain_15c/sysroot
CROSS_COMPILE=/workspace/avs_sdk_dir_arm/android_home/toolchain_12b/bin/arm-linux-androideabi-
SYSROOT=/workspace/avs_sdk_dir_arm/android_home/toolchain_12b/sysroot
ANDROID_HOME=/workspace/deltan_0815/vendor/libratone/sdk/avs-ndk2/android_home/usr/local/

CC := $(CROSS_COMPILE)gcc
AR = $(CROSS_COMPILE)ar

CFLAGS += -I $(SYSROOT)/usr/include -I $(ANDROID_HOME)/include -I ../../include/websocket -I /workspace/AudioGum/libaudiogum-cross/uuid/libuuid-1.0.3/buildout/include -pie -fPIE
LDFLAGS +=  -L $(SYSROOT)/usr/include -L $(ANDROID_HOME)/lib -L ../../libs/websocket -L /workspace/AudioGum/libaudiogum-cross/uuid/libuuid-1.0.3/buildout/lib

all:
	@mkdir -p bin
	@$(CC) -g main.c -std=gnu99 $(CFLAGS) -o bin/$(APPNAME) $(LDFLAGS) -I$(IDIR) $(LIBS)
	@echo "Created bin/$(APPNAME)"

clean:
	@rm -rf bin

```



## 4. 移植libuuid

```
# ./configure --host=arm-linux-androideabi --prefix=/workspace/AudioGum/libaudiogum-cross-12b/uuid/libuuid-1.0.3/buildout CC=arm-linux-androideabi-gcc
# make
```

## 5. 工作流程介绍

**获取access token**

将client_id:client_secrect进行[base64编码](http://tool.oschina.net/encrypt?type=3)

```
./curl -vX POST --header 'Content-Type: application/json' --header 'Authorization: Basic MTYzZmFlY2NhN2I1NDhkOWFiYzQ4YTlhMmNjZjVhYjQ6ZlovOC8rS2JFeFBYemZnVU5hUTZhVklNVG9hUVdqK1FISXVxYXNTdEVqRGRMWm5sa3FPSEh1LzhaNm55eXNBYQ==' -d '{ "deviceid": "nativedi",  "scope": "device-all", "grant_type":"client_credentials"}' 'https://api.audiogum.com/v1/tokens'
```

**获取remote_control token**

  ```
  ./curl -vX POST --header 'Authorization: Bearer v1::gJ/bu7FPv6asDOjuo83oyg==::mSAe/VQDvgspD8619lVpLJunuSRE2QMWHfeILAPy/cpBwpQHE56PegD9dacn5M667w/d34NnlQUlCMq022woPCm82zcucM3fKV+YvPytkOvLYHhBjtnSsGON6mYzyYJm3zlRk5V0ZmhyYvtLNinvawCCl7S8jYk+xrLcpvcsO6MawF3Xu32dd6u++1BLSUSUIcTDW3g+qrD0OsTKWMDOOZcW9cLrEO1ZRLT5H/IXVgSKPajdJwnmySBvjg9e7UJZ'  'https://api.audiogum.com/v1/remotecontrol/tokens'
  ```

**建立websocket连接**

```
v1::sv6D3AMLr5R0DYcnjrvvBQ==::sw7FoIm2BBYcOW0G09+G9QnOA0HcrSIuX7vDRZGFD3hVUpXZhXPVe0decf4M2vTcMR/FhASYXUUk9g8kLJRWj5yEMnUruCb0QKSUPZiSeUqgpet+95vjgli2r8eysu5fqn8vBy723GQVHQj29kErSJX6B+vshgkpGVvaB5CW2IQfT5rVWtTR3+nSNVdTgiszY5GdcWui05e9oXyHhxQzF047PxpVrf+/9u0k6kDkm9oFL9A17KrCjJ1WgDkrd0Ej

v1::KbsbKUcukmeZWXo5lEauGw==::QjM8e/49+wFyzjKWJZKRSXWldAfCQ6/nmTqQisPuhQ8AOjxKsEBydTGs2PFxWHQcYeSARG7AvsTm9Eg8JIPiR7YO3dwAh2mauNSt1OkfsI4=

&capabilities=play,stop,skipnext,skipprev
```
# error

**error 1**

```
/usr/local/lib/libcurl.so.4: no version information available

> cd /usr/local/lib
> locate libcurl.so.4
> sudo rm libcurl.so.4
> sudo ln -s /usr/lib/x86_64-linux-gnu/libcurl.so.4.4.0 /usr/local/lib/libcurl.so.4
参考：
https://stackoverflow.com/questions/30017397/error-curl-usr-local-lib-libcurl-so-4-no-version-information-available-requ/38896967
```

**error 2**

```
dereferencing pointer to incomplete type
检查库的路径是否设置正确
```

**error 3**

```
error:
fatal error: sys/syslog.h: No such file or directory
include <sys/syslog.h>
resolve:
在ndk12b 下没有sys/syslog.h这个路径，在ndk15c下存在这个路径，使用ndk15c来编译则不会出现这个错误
＃find . -name syslog.h
./toolchain_15c/sysroot/usr/include/sys/syslog.h
./toolchain_15c/sysroot/usr/include/syslog.h
./toolchain_12b/sysroot/usr/include/syslog.h
```

**error4**

```
[2018/10/23 12:04:20:1103] INFO: closing conn at LWS_CONNMODE...SERVER_REPLY
[2018/10/23 12:04:20:1109] INFO: reason: lws_ssl_client_connect2 failed
[2018/10/23 12:04:20:1111] WARN: LWS_CALLBACK_CLIENT_CONNECTION_ERROR 'lws_ssl_client_connect2 failed'
Socket state received: SERVER UNREACHABLE - check you have a valid connection - library will attempt to reconnect

解答：
这个是根证书的路径设置的不正确，在代码中添加一行代码info.client_ssl_ca_filepath="./ca-certificates.crt";设置一下根证书的路径，否则会按照系统默认设置查找。之前一直没定位到，是因为觉得websocket使用curl,而使用curl在编译的时候就设置好了根证书的路径，现在看来，websocket使用的ssl,并没有使用curl.
```



#参考链接

[总结使用libwebsockets](https://blog.csdn.net/qq_39101111/article/details/78627448)

[curl使用](https://blog.csdn.net/cyl937/article/details/52850304)

[openssl API](https://www.openssl.org/docs/man1.0.2/ssl/SSL_get_error.html)

[openssl编译参数解释](http://www.jinbuguo.com/linux/openssl_install.html)

[oAuth2](https://www.cnblogs.com/Wddpct/p/8976480.html)

[SSL和TLS的概述](https://segmentfault.com/a/1190000009002353)

