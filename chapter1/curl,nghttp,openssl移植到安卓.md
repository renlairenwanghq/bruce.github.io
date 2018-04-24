#curl 、nghttp、openssl交叉编译到安卓平台

##1. 简介

为了将avs的sdk移植到基于android裁剪出来的系统上，首先需要编译curl，nghttp等依赖库。

####参考链接

nghttp2官网描述的编译过程
http://www.nghttp2.org/documentation/building-android-binary.html

根据上述链接描述，生成一个目录结构,后面会根据该目录结构设置环境变量

```
avsdir3
----android_home
	----toolchain
----usr
	----local
```



##2. pkg-config

curl,nghttp这些的编译如果想通过--with-xxx来包含第三方库，编译过程中会使用pk-config去查找对应的第三方库及头文件。所以在生成Makefile时需要添加一些参数来确保编译过程中能够找到这些库和头文件。至于pkg-config如何使用，参考链接[干货:pkg-config工具在实际工程中的用法](https://blog.csdn.net/mantis_1984/article/details/52847435)

##3. 设置环境变量
```
PATH=$PATH:/workspace/avsdir2/android_home/toolchain/bin
```

##4. zlib

常用库之四：zlib的交叉编译
https://blog.csdn.net/npy_lp/article/details/6991704

* 设置环境变量

```
export CC=arm-linux-androideabi-gcc
```

* 生成Makefile

```
./configure --prefix=/workspace/avsdir3/android_home/usr/local
```

* 编译执行make

**使用ndk16时，会报错，更改为低一点的版本，可以编译通过**

***********************************************************************************************************
error:
arm-linux-androideabi-gcc -O3 -D_LARGEFILE64_SOURCE=1 -DHAVE_HIDDEN -o example example.o -L. libz.a
example.o:example.c:function test_compress: error: undefined reference to 'stderr'
example.o:example.c:function test_gzio: error: undefined reference to 'stderr'
example.o:example.c:function test_deflate: error: undefined reference to 'stderr'
example.o:example.c:function test_inflate: error: undefined reference to 'stderr'
***********************************************************************************************************
错误参考：

[Android NDK: undefined reference to 'stderr'](https://stackoverflow.com/questions/39322852/android-ndk-undefined-reference-to-stderr)

* 执行make install

##5. openssl

* 生成Makefile

```
./config CC=arm-linux-androideabi-gcc CXX=arm-linux-androideabi-g++ --openssldir=/workspace/avsdir3/android_home/usr/local  --prefix=/workspace/avsdir3/android_home/usr/local --sysroot=/workspace/avsdir3/android_home/toolchain/sysroot  no-asm no-shared zlib
```

* 对生成的Makefile文件做修改

  删除　-m64　参数，共两处

* make

**出现错误**
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
第一次make设置的toolchain是ndk10,修改为ndk11之后，编译通过。

* 安装 (去除文档安装)

```
make install_sw 
```

查看，已经安装到对应目录，并且可以搜索到pkg-config需要的`.pc`文件。
```
/workspace/avsdir3/android_home/usr/local$ ls
bin  include  lib

/workspace/avsdir3/android_home/usr/local$ find . -name *.pc
./lib/pkgconfig/openssl.pc
./lib/pkgconfig/libssl.pc
./lib/pkgconfig/libcrypto.pc

```


##6. nghttp2
* 查看android-config文件

**由于其中包含ANDROID_HOME，所以需要设置环境变量**

```
export ANDROID_HOME=/workspace/avsdir3/android_home/
```

**设置安装的路径--prefix**

修改好的android-config文件内容如下:
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
TOOLCHAIN="$ANDROID_HOME"/toolchain
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

**注意:**
之所以通过这两项配置，可以查找的openssl，是因为openssl安装到了$PREFIX路径下，因为安装的时候存在`.pc`文件，所以
pkg-config可以查找到对应的头文件。　
--with-openssl="$PREFIX"
PKG_CONFIG_LIBDIR="$PREFIX/lib/pkgconfig"

* 执行./android-config

查看结果: OpenSSL这一项是yes，说明编译时候找到了openssl对应的库文件和头文件。

```
configure: summary of build options:

    Package version: 1.32.0-DEV
    Library version: 30:1:16
    Install prefix:  /workspace/avsdir3/android_home//usr/local
    System types:
      Build:         x86_64-pc-linux-gnu
      Host:          arm-unknown-linux-androideabi
      Target:        arm-unknown-linux-androideabi
    Compiler:
      C compiler:     /workspace/avsdir3/android_home//toolchain/bin/arm-linux-androideabi-gcc
      CFLAGS:         -g -O2
      LDFLAGS:        -fPIE -pie -L/workspace/avsdir3/android_home//usr/local/lib
      C++ compiler:   /workspace/avsdir3/android_home//toolchain/bin/arm-linux-androideabi-g++
      CXXFLAGS:       -g -O2
      CXXCPP:         /workspace/avsdir3/android_home//toolchain/bin/arm-linux-androideabi-g++ -E
      C preprocessor: /workspace/avsdir3/android_home//toolchain/bin/arm-linux-androideabi-gcc -E
      CPPFLAGS:       -fPIE -I/workspace/avsdir3/android_home//usr/local/include
      WARNCFLAGS:     
      WARNCXXFLAGS:   
      CXX1XCXXFLAGS:   -std=c++11
      EXTRACFLAG:     -fvisibility=hidden
      LIBS:           
    Library:
      Shared:         no
      Static:         yes
    Python:
      Python:         /workspace/avsdir3/android_home//toolchain/bin/python
      PYTHON_VERSION: 2.7
      pyexecdir:      ${exec_prefix}/lib/python2.7/site-packages
      Python-dev:     
      PYTHON_CPPFLAGS:
      PYTHON_LDFLAGS: 
      Cython:         cython
    Test:
      CUnit:          no (CFLAGS='' LIBS='')
      Failmalloc:     yes
    Libs:
      OpenSSL:        yes (CFLAGS='-I/workspace/avsdir3/android_home/usr/local/include' LIBS='-L/workspace/avsdir3/android_home/usr/local/lib -lssl -lcrypto')
      Libxml2:        no (CFLAGS='' LIBS='')
      Libev:          no (CFLAGS='' LIBS='')
      Libc-ares       no (CFLAGS='' LIBS='')
      Libevent(SSL):  no (CFLAGS='' LIBS='')
      Jansson:        no (CFLAGS='' LIBS='')
      Jemalloc:       no (LIBS='')
      Zlib:           no (CFLAGS='' LIBS='')
      Systemd:        no (CFLAGS='' LIBS='')
      Boost CPPFLAGS: 
      Boost LDFLAGS:  
      Boost::ASIO:    
      Boost::System:  
      Boost::Thread:  
    Third-party:
      http-parser:    no
      MRuby:          no (CFLAGS='' LIBS='')
      Neverbleed:     no
    Features:
      Applications:   no
      HPACK tools:    no
      Libnghttp2_asio:no
      Examples:       no
      Python bindings:no
      Threading:      no

```

* 执行`./android-make`
* 执行`./android-make install`

查看结果： nghttp2对应的头文件，库文件，及pkg-config需要的`.pc`文件已经存在该目录结构下
```
$ cd include/
milo@milo-OptiPlex-7040:/workspace/avsdir3/android_home/usr/local/include$ ls
nghttp2  openssl
milo@milo-OptiPlex-7040:/workspace/avsdir3/android_home/usr/local/include$ cd ..
milo@milo-OptiPlex-7040:/workspace/avsdir3/android_home/usr/local$ cd lib/
milo@milo-OptiPlex-7040:/workspace/avsdir3/android_home/usr/local/lib$ ls
engines-1.1  libcrypto.a  libnghttp2.a  libnghttp2.la  libssl.a  pkgconfig
milo@milo-OptiPlex-7040:/workspace/avsdir3/android_home/usr/local/lib$ cd ..
milo@milo-OptiPlex-7040:/workspace/avsdir3/android_home/usr/local$ find . -name *.pc
./lib/pkgconfig/openssl.pc
./lib/pkgconfig/libssl.pc
./lib/pkgconfig/libcrypto.pc
./lib/pkgconfig/libnghttp2.pc./configure --host=arm-linux-androideabi CC=arm-linux-androideabi-gcc CXX=arm-linux-androideabi-g++ PKG_CONFIG_LIBDIR=/workspace/avsdir3/android_home/usr/local/lib/pkgconfig --prefix=/workspace/avsdir3/android_home/usr/local　--with-nghttp2=/workspace/avsdir3/android_home/usr/local --with-ssl=/workspace/avsdir3/android_home/usr/local
```

##7. libcurl
* 参考nghttp2的编译过程，设置参数，生成Makefile文件

```
./configure --host=arm-linux-androideabi CC=arm-linux-androideabi-gcc CXX=arm-linux-androideabi-g++ PKG_CONFIG_LIBDIR=/workspace/avsdir3/android_home/usr/local/lib/pkgconfig --prefix=/workspace/avsdir3/android_home/usr/local　--with-nghttp2=/workspace/avsdir3/android_home/usr/local --with-ssl=/workspace/avsdir3/android_home/usr/local
```

查看结果：　发现nghttp2是enable状态，但是ssl这一项仍然是no

```
configure: Configured to build curl/libcurl:

  curl version:     7.54.0
  Host setup:       arm-unknown-linux-androideabi
  Install prefix:   /workspace/avsdir3/android_home/usr/local　--with-ssl=/workspace/avsdir3/android_home/usr/local
  Compiler:         arm-linux-androideabi-gcc
  SSL support:      no      (--with-{ssl,gnutls,nss,polarssl,mbedtls,cyassl,axtls,winssl,darwinssl} )
  SSH support:      no      (--with-libssh2)
  zlib support:     enabled
  GSS-API support:  no      (--with-gssapi)
  TLS-SRP support:  no      (--enable-tls-srp)
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
  Protocols:        DICT FILE FTP GOPHER HTTP IMAP POP3 RTSP SMTP TELNET TFTP

  SONAME bump:     yes - WARNING: this library will be built with the SONAME
                   number bumped due to (a detected) ABI breakage.
                   See lib/README.curl_off_t for details on this.

```
重新执行，然后grep筛选相关信息, 发现checking for ssl_version in -laxtls这一项check失败
```
$ ./configure --host=arm-linux-androideabi CC=arm-linux-androideabi-gcc CXX=arm-linux-androideabi-g++ PKG_CONFIG_LIBDIR=/workspace/avsdir3/android_home/usr/local/lib/pkgconfig --prefix=/workspace/avsdir3/android_home/usr/local　--with-nghttp2=/workspace/avsdir3/android_home/usr/local --with-ssl=/workspace/avsdir3/android_home/usr/local |grep ssl
configure: WARNING: using cross tools not prefixed with host triplet
checking for ldapssl.h... no
checking for ldap_ssl.h... no
configure: WARNING: Cannot find libraries for LDAP support: LDAP disabled
configure: WARNING: the previous check could not be made default was used
checking for openssl options with pkg-config... found
configure: pkg-config: SSL_LIBS: "-lssl -lcrypto"
checking for ssl_version in -laxtls... no
configure: WARNING: SSL disabled, you will not be able to use HTTPS, FTPS, NTLM and more.
configure: WARNING: Use --with-ssl, --with-gnutls, --with-polarssl, --with-cyassl, --with-nss, --with-axtls, --with-winssl, or --with-darwinssl to address this.
configure: WARNING: skipped the ca-cert path detection when cross-compiling
configure: WARNING: libpsl was not found
configure: WARNING: Cannot find libraries for IDN support: IDN disabled
configure: WARNING: This libcurl built is probably not ABI compatible with previous
configure: WARNING: builds! You MUST read lib/README.curl_off_t to figure it out.
config.status: creating packages/Linux/RPM/curl-ssl.spec
  SSL support:      no      (--with-{ssl,gnutls,nss,polarssl,mbedtls,cyassl,axtls,winssl,darwinssl} )
```

