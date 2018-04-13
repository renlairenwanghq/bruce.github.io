##CMAKE


[CMake和Make之间的区别 ](https://blog.csdn.net/android_ruben/article/details/51698498)

[cmake入门实战](http://www.hahack.com/codes/cmake/)

[cmake中文手册](http://www.cnblogs.com/coderfenghc/tag/%E6%89%8B%E5%86%8C/)

CMake是一个跨平台的安装（[编译](https://baike.baidu.com/item/%E7%BC%96%E8%AF%91)）工具，可以用简单的语句来描述所有平台的安装(编译过程)。他能够输出各种各样的makefile或者project文件，能测试[编译器](https://baike.baidu.com/item/%E7%BC%96%E8%AF%91%E5%99%A8)所支持的C++特性,类似UNIX下的automake。

CMake说白了就是一个为CMakeLists.txt的工程脚本配置文件。CMake可以针对不同操作系统和IDE环境生成不同的脚本或工程文件，如VS解决方案、Mac OSX的XCode文件、Unix/Linux系统的Makefile文件等等。试图直接使用子目录的CMakeList.txt是无效的，因为系统找不到许多在根目录的CMakeLists文件中配置的参数和宏，因而会产生错误提示，无法继续执行。

总之，如果工程用CMake来配置，然后一个命令就可以整齐地输出链接库和可执行文件。

## CMAKE使用方法

CMake的所有的语句都写在一个叫:CMakeLists.txt 的文件中。当CMakeLists.txt文件确定后，可以用ccmake命令对相关 的变量值进行配置。这个命令必须指向CMakeLists.txt所在的目录。配置完成之后，应用cmake命令生成相应的makefile（在Unix like系统下）或者 project文件（指定用window下的相应编程工具编译时）。**需要为任何子目录建立一个CMakeLists.txt**。

其基本操作流程为：

```
ccmake directory
cmake directory
cmake -DCMAKE_BUILD_TYPE=Debug/Release directory
make
```

其中，directory为CMakeLists.txt所在目录，Makefile等文件的生成目录为cmake执行目录：

- 第一条语句用于配置编译选项，如VTK_DIR目录，一般这一步不需要配置，直接执行第二条语句即可，但当出现错误时，这里就需要认为配置了，这一步才真正派上用场；
- 第二条命令用于根据CMakeLists.txt生成Makefile文件；
- 三条命令用于执行Makefile文件，编译程序，生成可执行文件。

### CMAKE指令详解

[cmake指令详解](https://blog.csdn.net/bytxl/article/details/50635016)

[CMake常用语法总结](https://www.jianshu.com/p/8909efe13308)

**project**

```
project(THFsamples)
这条指令会自动创建两个变量：
<projectname>BINARY_DIR(二进制文件保存路径)    <projectname>SOURCE_DIR（源代码路径）
cmake系统也帮助我们预定义了PROJECT_BINARY_DIR和PROJECT_SOURCE_DIR其值与上述对应相等
```

**set(变量名 变量值)**

```
set(THF_ROOT ${CMAKE_SOURCE_DIR}/../..)
set(THF_INCLUDE ${THF_ROOT}/include)
```

**add_library**

```
add_library(libname [SHARED|STATIC|MODULE] [EXCLUDE_FROM_ALL] source1 source2 ... sourceN)
生成动态静态库
eg.
add_library(snsr STATIC IMPORTED)
```

**ADD_EXECUTABLE**

```
ADD_EXECUTABLE(可执行文件名  生成该可执行文件的源文件)
说明源文件需要编译出的可执行文件名
eg：
ADD_EXECUTABLE(hello ${SRC_LIST})
说明SRC_LIST变量中的源文件需要编译出名为hello的可执行文件
```

**ADD_EXECUTABLE**

```
ADD_EXECUTABLE(可执行文件名  生成该可执行文件的源文件)
说明源文件需要编译出的可执行文件名
eg：
ADD_EXECUTABLE(hello ${SRC_LIST})
说明SRC_LIST变量中的源文件需要编译出名为hello的可执行文件
```

**SET_TARGET_PROPERTIES**

```
设置目标的一些属性来改变它们构建的方式。
为一个目标设置属性。该命令的语法是列出所有你想要变更的文件，然后提供你想要设置的值。你能够使用任何你想要的属性/值对，并且在随后的代码中调用GET_TARGET_PROPERTY命令取出属性的值。
set_target_properties(target1 target2 ...
					  PROPERTIES prop1 value1
					  prop2 value2 ...)
eg.
set_target_properties(snsr PROPERTIES IMPORTED_LOCATION
     ${THF_LIBRARIES}/${CMAKE_STATIC_LIBRARY_PREFIX}snsr${CMAKE_STATIC_LIBRARY_SUFFIX})
```

**set_property**

在给定的作用域内设置一个命名的属性

```
set_property(<GLOBAL | 
            DIRECTORY [dir] | 
            TARGET [target ...] | 
            SOURCE [src1 ...] | 
            TEST [test1 ...] | 
            CACHE [entry1 ...]>
             [APPEND] 
             PROPERTY <name> [value ...])

```

第一个参数决定了属性可以影响的作用域,必须为以下值：

- GLOBAL 全局作作用域,不接受名字
- DIRECTORY 默认为当前路径，但是同样也可以用[dir]指定路径
- TARGET 目标作用，可以是0个或多个已有的目标
- SOURCE 源作用域， 可以是0个过多个源文件
- TEST 测试作用域, 可以是0个或多个已有的测试
- CACHE 必须指定0个或多个cache中已有的条目

**add_subdirectory**

如果当前目录下还有子目录时可以使用`add_subdirectory`，子目录中也需要包含有`CMakeLists.txt`

```
# sub_dir指定包含CMakeLists.txt和源码文件的子目录位置
# binary_dir是输出路径， 一般可以不指定
add_subdirecroty(sub_dir [binary_dir])
```

### CMake常用变量和环境变量

[CMake使用心得](https://blog.csdn.net/jonathan321/article/details/51987416)

####工程顶层目录/cmake

```
CMAKE_SOURCE_DIR
PROJECT_SOURCE_DIR
<projectname>_SOURCE_DIR

// 以下环境变量在in source编译情况下为工程顶层目录
CMAKE_BINARY_DIR
PROJECT_BINARY_DIR
<projectname>_BINARY_DIR
```