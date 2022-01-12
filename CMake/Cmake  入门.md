## 示例
CMakeLists.txt
```
PROJECT (HELLO)
SET (SRC_LIST main.cpp)
MESSAGE(STATUS "This is BINARY dir" ${HELLO_BINARY_DIR})
MESSAGE(STATUS "This is BINARY dir" ${HELLO_BINARY_DIR})
ADD_EXECUTABLE(hello ${SRC_LIST})

```
## 关键字
### PROJECT 关键字
可以用来指定工程的名字和支持的语言，默认支持所有语言。
PROJECT(HELLO) 指定了工程的名字，并且支持所有语言 --建议
PROJECT(HELLO CXX) 指定了工程的名字，并且支持语言是C++
PROJECT(HELLO C CXX JAVA) 指定了工程的名字，并且支持语言是C, C++和JAVA 
```shell
该指定隐式定义了两个CMAKE的变量，projectname，这里是HELLO
<projectname>_BINARY_DIR, 本例是 HELLO_BINARY_DIR
<projectname>_SOURCE_DIR, 本例是HELLO_SOURCE_DIR
// MESSAGE关键字就可以直接使用这两个变量，当前都指向当前的工作目录。
// 如果改了工程名，这两个变量名也会变，如何解决？
CMake又定义了两个预定义变量：PROJECT_BINARY_DIR和PROJECT_SOURCE_DIR，这两个变量与HELLO_BINARY_DIR和HELLO_SOURCE_DIR的值是一样的。
```
### SET 关键字
用来显式的指定变量。
```
SET(SRC_LIST main.cpp) 
// 表示定义了一个变量SRC_LIST，该变量包含main.cpp这个文件
也可以 SET(SRC_LIST main.cpp t1.cpp t2.cpp) // 多个用空格来分割
```

### MESSAGE 关键字
向终端输出用户自定义的信息。
```
主要包含三种信息：
SEND_ERROR，产生错误，生成过程被跳过。
STATUS，输出前缀为--(横杠)的信息。
FATAL_ERROR，立即终止所有cmake过程。
```
### ADD_EXECUTABLE 关键字
生成可执行文件。
ADD_EXECUTABLE(hello ${SRC_LIST}) 生成的可执行文件名叫hello，源文件读取变量SRC_LIST中的内容。也可以直接写 ADD_EXECUTABLE(hello main.cpp)
> 注意： 工程名的HELLO和生成的可执行文件hello是没有任何关系的。

## 语法的基本原则
```
变量使用${}方式取值，但是在IF控制语句中是直接使用变量名。
指令(参数1 参数2....) 参数使用括弧括起，参数之间使用空格或分号分开，例如 ADD_EXECUTABLE(hello main.cpp func.cpp) 或 ADD_EXECUTABLE(hello main.cpp;func.cpp)。
指令是大小写不敏感的，参数和变量是大小写敏感的，但推荐全部使用大写指令。
```
## 语法注意事项
```
SET(SRC_LIST main.cpp) 可以写成 SET(SRC_LIST "main.cpp")，如果源文件名中含有空格，就必须添加双引号。
ADD_EXECUTABLE(hello main) 后缀可以不写，它会自动去找.c和.cpp文件，但不推荐省略，建议写完整的文件名。
```
## 内部构建和外部构建
上述例子就是内部构建，它产生的临时文件特别多，不方便清理。
外部构建就是把生成的临时文件放在build目录下，不会对源文件有任何影响，一般都采用外部构建。

## 安装
```
一种是从代码编译后直接 make install 安装，注意，install的安装可以包括：二进制，动态库，静态库以及文件，目录，脚本等。使用CMAKE的一个新变量：CMAKE_INSTALL_PREFIX
一种是打包时指定目录安装
    简单的可以这样指定目录：make install DESTDIR=/tmp/test
    复杂点可以这样指定目录：./configure -prefix=/usr
```
例如：
```
// 目录树
helloworld
|-- build
|-- CMakeLists.txt
|-- COPYRIGHT
|-- doc
|    |-- hello.txt
| -- README
|-- runhello.sh
|-- src
    |-- CMakeLists.txt
    |-- main.cpp

# 安装文件 COPYRIGHT和README
INSTALL(FILES COPYRIGHT README DESTINATION share/doc/cmake)
说明：
1). FILES 表示文件。
2). DESTINATION 可以写绝对路径，也可以写相对路径，相对路径实际路径是：${CMAKE_INSTALL_PREFIX}/<DESTINATION 定义的路径>。
  1.CMAKE_INSTALL_PREFIX 默认是在/usr/local。
  2.cmake -DCMAKE_INSTALL_PREFIX=/url 在cmake的时候指定CMAKE_INSTALL_PREFIX变量的路径>。

# 安装脚本 runhello.sh
PROGRAMS： 非目标文件的可执行程序安装(比如脚本之类)
INSTALL(PROGRAMS runhello.sh DESTINATION bin)
说明：
实际安装到的是/usr/bin目录。

# 安装 doc中的hello.txt
1). 通过在doc目录建立CMakeLists.txt，通过install下的file
2). 直接在工程目录通过 INSTALL(DIRECTORY doc/ DESTINATION share/doc/cmake)
说明：
DIRECTORY 后面连接的是所在Source目录的相对路径
注意：
   abc 和 abc/ 有很大的区别
    目录名不以/结尾，这个目录(整个目录)将被安装为目标路径下。
    目录名以/结尾，将这个目录下的内容安装到目标路径。

# 安装过程
进入到项目的build目录，
cmake ..
make
make install
```

## 静态库和动态库的构建
- 静态库和动态库的区别：
  - 静态库的扩展名一般为 ".a" 或 ".lib"，动态库的扩展名一般为 ".so" 和 ".dll"。
  - 静态库在编译时会直接整合到目标程序中，编译成功的可执行文件可独立运行。
  - 动态库在编译时不会放到连接的目标程序中，即可执行文件无法单独运行。
### 构建实例
任务：
1. 建立一个静态库和动态库，提供HelloFunc函数功其他程序编程使用，HelloFunc向终端输出Hello World字符串。
2. 安装头文件与共享库。
```
# 目录树
cmake02
|-- build
|-- CMakeLists.txt
|-- lib
    |-- CMakeLists.txt
    |-- hello.cpp 
    |-- hello.h
// hello.h 中的内容
#ifndef HELLO_H
#define Hello_H

void HelloFunc();

#endif
// hello.cpp中的内容
#include "hello.h"
#include <iostream>
void HelloFunc(){
    std::cout << "Hello World" << std::endl;
}
// 先写项目根目录下的CMakeLists.txt内容
PROJECT(HELLO)
ADD_SUBDIRECTORY(lib bin)
// 再写lib目录下的CMakeLists.txt内容
SET(LIBHELLO_SRC hello.cpp)
ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})
说明：
ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})
  - hello 定义正常的库名，生成的名字前面会加lib，最终生成的文件是libhello.so
  - SHARED 表示动态库
  - STATIC 表示静态库
  - ${LIBHELLO_SRC} 表示源文件
```
### 同时构建静态和动态库
```
// 如果用这种方式，只会构建一个动态库，不会构建出静态库，虽然静态库的后缀是.a
ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})
ADD_LIBRARY(hello STATIC ${LIBHELLO_SRC})
// 修改静态库的名字，这样是可以的，但是我们往往希望他们的名字是相同的，只是后缀名不同而已
ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})
ADD_LIBRARY(hello_static STATIC ${LIBHELLO_SRC})

// SET_TARGET_PROPERTIES 这条指令可以用来设置输出的名称，对于动态库而言，还可以用来指定动态库版本和API版本
// 推荐使用如下方式，同时构建静态库和动态库
SET(LIBARAY_SRC hello.cpp)
ADD_LIBRARY(hello_static STATIC ${LIBHELLO_SRC})
// 对hello_static的重命名为hello
SET_TARGET_PROPERTIES(hello_static PROPERTIES OUTPUT_NAME "hello")
// cmake 在构建一个新的target时，会尝试清理掉其他使用这个名字的库，因为在构建libhello.so时，就会清理掉 libhello.a
SET_TARGET_PROPERTIES(hello_static PROPERTIES CLEAN_DIRECT_OUTPUT 1)

ADD_LIBRARY(hello SHARED ${LIBARAY_SRC})
SET_TARGET_PROPERTIES(hello PROPERTIES OUTPUT_NAME "hello")
SET_TARGET_PROPERTIES(hello PROPERTIES CLEAN_DIRECT_OUTPUT 1)
```
### 动态库的版本
一般动态库都有一个版本号的关联
```
libhello.so.1.2
libhello.so->libhello.so.1
libhello.so.1->libhello.so.1.2

// CMakeLists插入如下
SET_TARGET_PROPERTIES(hello PROPERTIES VERSION 1.2 SOVERSION 1)
说明：VERSION 表示动态库版本，SOVERSION 表示API版本
```
### 安装共享库和头文件
我们将本例中的hello的共享库安装到<prefix>/lib目录下。
```
// 文件放到该目录下
INSTALL(FILES hello.h DESTINATION include /hello)
// 二进制，静态库，动态库安装都用TARGETS
// ARCHIVE 特指静态库，LIBRABY 特指动态库，RUNTIME 特指可执行目标二进制
INSTALL(TARGETS hello hello_static LIBRABY DESTINATION lib ARCHIVE DESTINATION lib)
// 备注，安装的时候，指定以下路径 cmake -DCMAKE_INSTALL_PREFIX=/usr ..
```
